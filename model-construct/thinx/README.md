# Model Construct — thinx

**Subject:** **thinx** (*thinx-Claude*) — a Claude Code deployment, the operating harness of this standard's own author. Its underlying reference architecture is MxM (multi-mode meta-harness); most of the "MxM reference architecture" / "reference deployment" informative examples scattered through Part 2 §N.6 describe this same deployment.

**Substrate:** Anthropic Claude, via Claude Code. This is the Anthropic-substrate reference the substrate-independent exemplars (grok-console on xAI Grok; the mxh-p portability kernel) are chosen to contrast *against*.

## Why this exemplar — and the bias to name first

thinx is the **most broadly realized single deployment in the standard**: a thinx-based example exists inline for 13 of the 16 Part 2 domains, more than any other exemplar demonstrates from one harness. That breadth is the reason to consolidate it here — no other single harness in the standard grounds this many domains at once.

But the breadth must be read with an explicit caveat, not presented as an independent endorsement of the standard's generality. **thinx is the author's own harness.** It is the most-cited exemplar precisely because it is the one the author can describe most accurately and most completely — a genuine selection bias, named here rather than hidden. Unlike grok-console (chosen deliberately *because* it was designed without AHES or thinx's patterns in mind, and so tests substrate-generality) thinx cannot serve as independent evidence that AHES's controls are substrate-agnostic. Its role is the opposite: it is the home deployment the standard was largely abstracted *from*, so a clause fitting thinx is close to tautological. The other `model-construct/` entries exist specifically to check that the clauses hold somewhere thinx's shape does not reach.

Read this entry as: *the completest worked realization*, and *the least independent one*.

## Domains realized

| AHES domain | What's demonstrated (thinx realization) |
|---|---|
| §1 Model mediation | Deliberate per-surface model selection (no auto-escalation), running model captured per session for attribution; the mediation seam holds identity/policy/memory constant while the model varies |
| §2 Context engineering | Governance surface declared non-compactable, pinned, and reloaded from canonical source at every context boundary; a compaction that would drop it is refused as an authority bypass |
| §3 Tool engineering | Side-effect class as the surface the enforcement layer binds to; conservative default for unclassified tools; classification and enforcement agree on the *resolved* target, not the typed argument |
| §6 Memory engineering | A layered, provenance-bearing memory: bounded curated recall over a raw-transcript store, current-state-overrides-recall convention, secrets-by-pointer, identity surfaces loaded structurally at session start |
| §7 Policy enforcement | *The fully worked clause.* Three-layer policy doc + repo-versioned dispatcher hook + per-command human override with audit log + a non-overridable third-party-infra category + session-start wiring self-test |
| §8 Human authority | The §7 override read as human authority: per-command marker a human must add in-conversation, tied to a named accountable human at per-action granularity, plus a non-releasable category |
| §9 Principal authority | Principal-altitude representation: an operator-altitude tactical principal distinguished from a higher standing-intent altitude; actions taken under a named principal, not the agent's own identity |
| §10 Evidence engineering | *Honest narrower realization.* A single-control, append-only override audit trail — sufficient for one enforcement layer, not a general session-lifecycle evidence architecture |
| §11 Evaluation | Lifecycle-integrated pass/fail gate (wrap-time) plus a harness-floor self-test at start; adversarial evaluation as replay of prior attack transcripts against the current policy layer |
| §12 Operational assurance | Drift detection as a durable append-only signal feed surfaced by an operator console; a session-start readiness report as the recurring monitoring surface; rollback as baseline-revert |
| §13 Supply chain | Vendor-in-over-reference by policy (copy and own locally, not depend live); models governed as a dependency class, so a model change is an admission event |
| §14 Security architecture | Credential boundary by pointer not copy (secrets in source only; memory holds pointers); credentialed capabilities resolved harness-side so the model never sees the secret |
| §15 Configuration baseline | *Non-normative pointer only.* Config surfaces treated as a version-controlled unit released as a bracketed lifecycle event — flagged in §15.6 as not-yet-fully-worked |

## Access note

thinx is a private repository. The mappings in [`realizations.md`](realizations.md) describe architectural patterns and design decisions — not exact file paths, field names, verbatim policy text, or internal ID/labeling schemes. They stay at the same abstraction level the Part 2 §N.6 examples already use; nothing more specific about thinx is disclosed here than is already published inline in the standard. AHES requirement IDs (PE-A1, ME-03, etc.) are the standard's own vocabulary, not thinx internals.

## Domains not attempted

Three of the 16 domains carry **no** thinx-based inline example in Part 2, and so are absent here:

- **§4 Agent engineering** — the §4.6 example is grok-console (a governed console for a non-Anthropic model CLI), not thinx. See [`../grok-console/04-agent-engineering.md`](../grok-console/04-agent-engineering.md).
- **§5 Workflow engineering** — §5.6 carries only a general-pattern note (the session-boundary checkpoint test), with no named deployment realization to consolidate.
- **§16 Substrate portability** — the §16.6 examples are the portability kernel ([`../mxh-p/`](../mxh-p/)) and its consuming grok-console harness, not thinx. This is by design: portability is the one domain the *other* exemplars exist to prove, precisely because thinx alone cannot.

Absence here is "no inline thinx example exists to consolidate," not a claim that thinx lacks the capability.
