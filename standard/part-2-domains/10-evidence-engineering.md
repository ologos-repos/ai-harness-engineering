# AHES Part 2 §10 — Evidence Engineering

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 10.1 Purpose

Evidence engineering governs the durable record: traces, decisions, inputs, context provenance, model identity and version, actions, approvals, policy decisions, and the configuration baseline active at each action. Unengineered, the failure class is **unreconstructible history**: incidents that cannot be root-caused, and conformance claims that cannot be audited, because what actually happened was never captured in an attributable, ordered, tamper-evident form.

Evidence is a *runtime* product, distinct in kind from the *versioned* configuration that governs the harness (§15). The two are cross-linked but not interchangeable: configuration says what the harness was set up to be; evidence says what it then did, and under which configuration it did it. Every domain in Part 2 that carries an "evidence requirements" subsection (§N.5) discharges that requirement *into* this architecture — §10 is where those obligations are made good generally, not per-domain.

## 10.2 Required artifacts

- **AHES-EE-A1** — An **evidence schema**: the closed, explicitly versioned definition of the evidence record shape — the fields every record carries and their types. Owner: harness engineering. Change control: version-controlled; a shape change is a schema-version increment, never a silent field addition.
- **AHES-EE-A2** — An **event-type catalog**: the closed, enumerated vocabulary of evidence event types, covering at minimum session lifecycle, approval/policy decisions (→ §7, §8), and action-level outcomes. Owner: harness engineering. Change control: additions are schema changes under A1's mechanism, so the full event space is always derivable from the released schema.
- **AHES-EE-A3** — A **retention and disposal policy**: retention period per event class, disposal procedure, and the tamper-evidence guarantee the store provides. Owner: operating organization. Change control: documented, versioned; disposal is itself an evidenced event.

## 10.3 Technical controls

- **AHES-EE-01** — Every accountable harness action **shall** produce an evidence record: side-effecting actions (→ §3), approval and override decisions (→ §7, §8), policy decisions (→ §7), and workflow checkpoints (→ §5) each generate a record. Read-only actions **may** be exempted; the exemption boundary **shall** match the §7 enforcement exemption boundary so that "mediated but unrecorded" is not a reachable state.
- **AHES-EE-02** — Every record **shall** be validated against the closed, versioned schema (A1) **at write time**, using a minimal-dependency writer. A record that does not conform **shall not** enter the store. Validation at write time — not read time — is required so a malformed record cannot be committed and later missed by a consumer.
- **AHES-EE-03** — Every record **shall** carry a collision-resistant identifier that encodes creation time and establishes a total order within a session, by binding a session identifier to a monotonically increasing per-session sequence number. Ordering **shall not** depend on wall-clock timestamps alone, which clock skew and equal-millisecond writes leave ambiguous.
- **AHES-EE-04** — Event types **shall** be drawn from the closed vocabulary (A2). A consumer — assessor, dashboard, incident review — **shall** be able to enumerate the full event space from the schema alone, without reverse-engineering it from log samples.
- **AHES-EE-05** — Every record **shall** state the authority class under which the underlying action fell, drawn from the same authority-class vocabulary as the §7 enforcement layer and the §9 principal-and-authority model. A record **shall** answer *under what authority*, not only *what happened*, so that "was this action within its permitted class" is decidable from evidence alone.
- **AHES-EE-06** — Every record **shall** reference the configuration baseline active at the time of the action, by the baseline identifier defined in §15. The identifier **shall** be resolvable to the full baseline content (→ §15), so the exact harness definition that produced any recorded outcome is recoverable. This is the load-bearing §10 ↔ §15 cross-link.
- **AHES-EE-07** — The evidence store **shall** be append-only and tamper-evident: modification or deletion of a committed record **shall** be detectable. Evidence whose integrity depends only on the good behavior of the process writing it **shall not** be the basis of a conformance claim, since that process's subject includes the reasoning engine being audited.
- **AHES-EE-08** — Evidence **shall** be generated at runtime and stored separately from version-controlled configuration (→ §15). Runtime records **shall not** be committed into the configuration baseline, and configuration **shall not** be reconstructed from evidence; the two stores have different integrity models and lifecycles.
- **AHES-EE-09** — Records for model invocations **shall** capture model identity and version (→ §1) and the provenance of context elements assembled for the invocation (→ §2), sufficient for post-hoc reconstruction of what the model was shown and which model version produced the output.

## 10.4 Verification criteria

| Control | Method |
|---|---|
| EE-01 | Test: enumerate accountable action classes (side-effecting, approval, policy, checkpoint); demonstrate each emits a record. Inspection of the exemption boundary against §7's. |
| EE-02 | Test: attempt to write a schema-nonconforming record; verify rejection. Inspection: writer validates against the released schema version. |
| EE-03 | Analysis of the identifier scheme for collision-resistance and intra-session total order. **Adversarial test**: concurrent same-millisecond writes remain uniquely identified and ordered. |
| EE-04 | Inspection: event-type set in emitted records is a subset of the catalog (A2); the catalog is derivable from the schema. |
| EE-05 | Inspection: authority-class field is populated and drawn from the §7/§9 vocabulary; cross-check against enforcement-layer decisions for the same actions. |
| EE-06 | Test: for a sample of records, resolve the referenced baseline identifier to full baseline content (→ §15). **Adversarial**: a record referencing a baseline that cannot be resolved is a conformance defect. |
| EE-07 | **Adversarial test**: attempt to alter or delete a committed record; verify the tamper is detectable. Inspection of the append-only and integrity mechanism. |
| EE-08 | Inspection: evidence store and configuration store are distinct with distinct lifecycles; runtime records are absent from version control. |
| EE-09 | Inspection: sampled invocation records carry model identity/version and per-element context provenance sufficient to reconstruct the input. |

## 10.5 Evidence requirements

These are *meta*-requirements — what the evidence architecture must guarantee about its own integrity, since §10 is the store every other domain's §N.5 relies on:

- **Tamper-evidence.** The store **shall** make unauthorized modification or deletion of committed records detectable (EE-07); an evidence architecture that cannot vouch for itself cannot underwrite any other clause's audit claim.
- **Retention.** Records **shall** be retained per the A3 retention policy, and for at least as long as the configuration baselines they reference are needed to reconstruct the recorded actions (→ §15). Disposal **shall** itself be an evidenced event.
- **Completeness monitoring.** The schema and write-time validator guarantee that *committed* records are well-formed; they do **not** on their own guarantee that every action produced a record. Detection of missing-record conditions is an operational-assurance concern (→ §12 AHES-OA-10) and **shall** be monitored there, not assumed here.
- **Incident separability.** Captured evidence **shall** be sufficient to classify a failure by locus — model, harness, integrated system, or operating organization — from the record alone (→ Part 3 quality-allocation). The authority-class binding (EE-05) and baseline reference (EE-06) are the fields that make this decidable.

## 10.6 Informative examples

**General evidence architecture (grok-console realization).** [`model-construct/grok-console/10-evidence-engineering.md`](../../model-construct/grok-console/10-evidence-engineering.md) realizes this clause as a general architecture the whole harness writes into: evidence records validate against a closed, explicitly versioned schema; each record carries a collision-resistant identifier encoding creation time, a session identifier, and a monotonic per-session sequence (EE-03); event types are a fixed enumerated set spanning session lifecycle, approval decisions, and command-level outcomes (EE-04); every record states the authority class the action fell under, using the §7 enforcement layer's own class vocabulary (EE-05); records are validated at write time by a minimal-dependency writer, so a malformed record cannot enter the store (EE-02); and evidence is generated at runtime and kept out of version control, distinct from the versioned configuration baseline (EE-08, → §15).

**Narrower realization (single-control audit trail).** The thinx deployment cited in §7.6 is a single-purpose, append-only override log — sufficient for one enforcement layer's audit need (EE-01, EE-07 for that event class), but not a general schema covering session lifecycle or event types beyond overrides. It illustrates that a per-control audit trail satisfies its own domain's §N.5 without, on its own, discharging the general §10 architecture.

**General pattern.** EE-07's adversarial framing follows from the domain's threat model, shared with §7: the audited subject includes the reasoning engine, so evidence integrity that depends on that engine's cooperation cannot underwrite a conformance claim — hence tamper-evidence is structural, not procedural.
