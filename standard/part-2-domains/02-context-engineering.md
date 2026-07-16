# AHES Part 2 §2 — Context Engineering

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 2.1 Purpose

Context engineering governs construction of each model invocation's input: instruction composition, retrieval, token budgeting, provenance of every context element, isolation between trust domains, and pinning of governance-critical content across context boundaries (compaction, summarization, session restart). When this domain is unengineered, two failure classes follow. The first is **prompt injection via unattributed content**: retrieved or external text is placed in context indistinguishably from governing instructions, and the model acts on data as though it were direction. The second is **silent loss of governing instructions under context pressure**: policy, identity, and principal authority are summarized away or dropped when the window fills, and the harness continues operating with its governance quietly degraded. Context is the model's primary injection surface, so this domain's threat model explicitly includes the model itself and the parties who can place content in front of it.

## 2.2 Required artifacts

- **AHES-CE-A1** — A **context-assembly specification**: the ordered composition of each invocation's input (instructions, memory, retrieved content, tool schemas, conversation state) and the rule by which each element is included or excluded. Owner: harness maintainer. Change control: versioned with the configuration baseline (→ §15).
- **AHES-CE-A2** — A **provenance schema** defining, for every context element, its source, trust class, and time of capture, such that the assembled context is reconstructable from record. Owner: harness maintainer. Change control: versioned.
- **AHES-CE-A3** — A **token-budget policy** stating how the input is bounded, the eviction order under pressure, and the protected set that eviction shall never touch. Owner: harness maintainer. Change control: versioned.
- **AHES-CE-A4** — A **pinned-content manifest**: the governance-critical elements (policy, agent identity, principal and authority model, active operating mode) that shall survive every context boundary, and the canonical source each is reloaded from. Owner: harness maintainer. Change control: versioned; the manifest is itself governance-critical.

## 2.3 Technical controls

- **AHES-CE-01** — Every context element **shall** carry provenance (source, trust class, capture time) per the CE-A2 schema. Content assembled into a model invocation without recorded provenance **shall not** be treated as trusted instruction.
- **AHES-CE-02** — The assembly **shall** structurally demarcate trust domains so that governing instructions are distinguishable from data. Content from a lower trust class **shall not** be composed into context in a form that presents it as higher-trust instruction.
- **AHES-CE-03** — Retrieved and externally sourced content **shall** be labeled as data, not instruction, and **shall not** be able to elevate its own trust class by its content. Instruction-trust status is assigned by the assembly layer structurally, never claimed by the content itself.
- **AHES-CE-04** — Token budgeting **shall** be deterministic and specified (CE-A3). When the budget forces exclusion, the eviction order **shall** follow the policy, and pinned governance-critical content (CE-A4) **shall never** be the content evicted.
- **AHES-CE-05** — Governance-critical content **shall** be pinned across context boundaries — compaction, summarization, session restart — and **shall** be reloaded from its canonical source rather than carried as summarizable conversation text. This content is the enforcement substrate the judgment layer depends on (→ §7); its loss is an approval-bypass precondition, not a cosmetic degradation.
- **AHES-CE-06** — A compaction or summarization step that would drop or degrade any pinned element (CE-A4) **shall** be refused or treated as a failure of the context boundary, not silently completed. The harness **shall** re-establish the pinned set from canonical source at every boundary and **shall** be able to demonstrate it did so.
- **AHES-CE-07** — Instruction composition **shall** be reconstructable: the recorded provenance (CE-A2) plus the assembly specification (CE-A1) **shall** be sufficient to reproduce the context that a given invocation received (→ §10).
- **AHES-CE-08** — Context **shall** be isolated between trust domains: one agent's or session's context **shall not** leak into another's except through an explicit, provenanced channel (→ §6 memory, → §14 security). Cross-domain inclusion without a recorded channel is a conformance defect.
- **AHES-CE-09** — Retrieval **should** bound what it admits by relevance and trust class rather than admitting the maximum the budget allows; over-retrieval of low-trust content enlarges the injection surface and **shall** be treated as a soft-stop-adjacent risk where the retrieved content can influence side-effecting actions (→ §7).

## 2.4 Verification criteria

| Control | Method |
|---|---|
| CE-01 | Inspection of the provenance schema; test that an assembled context records source/trust/time for every element; analysis for any unprovenanced path. |
| CE-02 | **Adversarial test**: place lower-trust content that mimics instruction formatting into a retrieval/external source; verify the assembly demarcates it as data and the model does not act on it as direction. |
| CE-03 | **Adversarial test**: inject content that asserts its own elevated authority ("ignore prior instructions", embedded directives); verify trust class is assigned structurally and does not rise. |
| CE-04 | Test under forced budget pressure: drive the input past the budget and confirm eviction follows policy order and never removes a pinned element. |
| CE-05 | Demonstration: after a context boundary, confirm each pinned element is present and sourced from canonical origin, not a summary. |
| CE-06 | **Adversarial test**: force a compaction/summarization that would drop a pinned element (large synthetic history); verify it is refused or flagged as failure and the pinned set is re-established. |
| CE-07 | Test: reconstruct a past invocation's context from recorded provenance + assembly spec; compare against what the model received. |
| CE-08 | **Adversarial test**: attempt to read one trust domain's context from another agent/session; verify isolation and that any legitimate cross-domain channel is explicit and provenanced. |
| CE-09 | Analysis of retrieval bounds against trust class; inspection that over-retrieval into side-effecting paths is treated as a flagged risk. |

## 2.5 Evidence requirements

The operating harness **shall** capture, per invocation: a context manifest listing each element with its provenance (source, trust class, capture time), the pinned-set load event at every context boundary (what was re-established, from which canonical source), and any eviction or compaction event with what it removed. This is sufficient to answer, post-hoc: *what did the model actually see, where did each element come from, was any of it untrusted content presented as instruction, and did the governing instructions survive every boundary in this session*. Evidence flows into the §10 evidence architecture; retention of the context baseline follows the configuration-baseline retention rule (→ §15).

## 2.6 Informative examples

**Pinned governance surface across compaction (MxM reference architecture).** A harness whose conversation history is automatically compacted when the window fills faces a documented, named risk class: the governing instructions — policy, agent identity, principal authority, active mode — can be summarized away while the surrounding work continues, leaving the agent operating with silently degraded governance. The engineered response is the architectural pattern CE-05/CE-06 specify: the governance surface is declared non-compactable, held as a pinned set, and reloaded from canonical source at every context boundary rather than carried as summarizable text, so a compaction that would drop it is structurally equivalent to an authority bypass and is refused. This is the context-domain precondition for the policy-enforcement judgment layer (→ §7) to remain sound: the judgment layer can only apply policy content that is reliably present in context.

**General pattern.** The heavy use of adversarial verification in §2.4 is not stylistic. Because context is the surface through which the model receives everything — including content authored by parties hostile to the harness's governance — every control whose subject is "what the model is allowed to treat as instruction" has a threat model that includes both the model and the content's author, and cannot be discharged by inspection alone.
