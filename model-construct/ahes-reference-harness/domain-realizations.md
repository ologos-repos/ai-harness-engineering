# AHES Reference Harness — Domain Realizations

Synthetic composite; see [`README.md`](README.md) for what that means. Design decisions below are generalized from pattern *classes* observed across this standard's real exemplars, not descriptions of any one of them — no source system is identifiable from what's written here.

## §1 Model mediation — *Means*

| Requirement | Design decision |
|---|---|
| AHES-MM-01–03 (abstraction, routing, versioning) | A single substrate-interface layer mediates all model access; nothing above it calls a model provider's SDK directly. Each logical surface (a class of invocation) binds to a specific qualified model version, not "whatever's current." |
| AHES-MM-04 (fallback) | A declared fallback tier per surface, ordered by qualification status, not by cost or availability alone — a fallback model must have passed the same qualification gate (→ §11) as the primary. |
| AHES-MM-05–09 (capability gating, substitution) | Substitution requires the Part 1 §1.2.2 qualification record before cutover; the substrate interface refuses to route to an unqualified model for a surface that requires qualification. |

## §2 Context engineering — *Mind*

| Requirement | Design decision |
|---|---|
| AHES-CE-01–03 (composition, retrieval, budgeting) | Context assembly is a single, auditable step — not scattered ad hoc string concatenation across the codebase — so provenance tagging (below) has one place to attach. |
| AHES-CE-04–06 (provenance, isolation) | Every context element is tagged trusted (harness-authored) or untrusted (retrieved, tool-returned, user-supplied) at assembly time. Untrusted content is never concatenated into a position the model would read as an instruction; it's delimited and labeled as data. |
| AHES-CE-07–09 (pinning across boundaries) | A fixed governance block (policy summary, active identity, standing constraints) is reloaded from canonical source at every context boundary — session start, post-compaction, post-truncation — rather than assumed to have survived. This is the structural answer to Part 4 R14 (governing instructions silently lost to a context boundary). |

## §3 Tool engineering — *Means*

| Requirement | Design decision |
|---|---|
| AHES-TE-01–03 (schemas) | Every tool has a declared input/output schema; a call that doesn't validate against it is rejected before dispatch, not after. |
| AHES-TE-04–06 (preconditions/postconditions) | Preconditions check target existence and type before execution; postconditions check the actual effect matches the declared side-effect class after execution — catching a tool whose real-world behavior has drifted from its declared classification. |
| AHES-TE-07–09 (side-effect classification, permission binding) | Every tool is classified read-only / mutating / externally-visible at registration (→ Part 1 §1.2.3); permission binding is checked against the calling agent's declared access level (→ §4), not against a global "the harness can do X" assumption. |

## §4 Agent engineering — *Means*

| Requirement | Design decision |
|---|---|
| AHES-AE-01–03 (identity, role, goal binding) | Every agent role is a standalone, named contract: identity, purpose, and an explicit negative scope ("not for..."). A role is dispatched by matching the task to a contract, not by ad hoc prompt construction. |
| AHES-AE-04–06 (permission binding, delegation) | Access level is structured metadata on the role contract, checked mechanically at the tool-engineering layer (§3), not inferred from behavior. Delegation depth is bounded and explicit — a role that spawns another role records the depth and the bound. |
| AHES-AE-07–09 (termination, recovery, self-containment) | Roles declare a stop condition on scope overrun ("if this needs surfaces outside my declared scope, stop and report") rather than trusting the model to self-limit. A distinct verification role checks completed work rather than the generating role self-certifying — this composes with §11's claim-verification gate below. |

## §5 Workflow engineering — *Means*

*(Described using only generic distributed-systems vocabulary — no coordination-runtime specifics of any kind; see the README's IP-safety note.)*

| Requirement | Design decision |
|---|---|
| AHES-WE-01–03 (orchestration, concurrency) | Multi-step work is dispatched through a queue, not a synchronous call chain, so a step's failure doesn't take down the step that dispatched it. |
| AHES-WE-04–06 (checkpoints, idempotency) | Each step's result is checkpointed before the next step starts; each step carries an idempotency key so a retried step doesn't double-apply its effect. |
| AHES-WE-07–09 (resumption, compensation) | A failed multi-step unit resumes from its last checkpoint rather than restarting; where a step's effect can't simply be retried (an external side effect already happened), a declared compensating action reverses or reconciles it rather than leaving the system in a partially-applied state. |

## §6 Memory engineering — *Memory*

| Requirement | Design decision |
|---|---|
| AHES-ME-01–03 (scope, schema) | Memory is scoped (session / agent / principal) at write time, not inferred at read time; every store has a versioned schema. |
| AHES-ME-04–06 (provenance, expiration) | Every memory record carries its origin (who/what wrote it, when, under what baseline) and a staleness signal — a record isn't silently treated as current forever. |
| AHES-ME-07–09 (cross-session/cross-principal isolation) | A memory store scoped to one principal is not readable by a session acting under a different principal without an explicit, evidenced grant — the same authority discipline as §9, applied to recall. |

## §7 Policy enforcement — *Morals*

The flagship domain — see [`architecture.md`](architecture.md) §3 for the full design (both structural mechanisms this standard's §7 clause documents, used together rather than as alternatives).

| Requirement | Design decision |
|---|---|
| AHES-PE-01–03 | Every side-effecting action passes a pre-execution gate that resolves the action's real target, not its surface phrasing. |
| AHES-PE-04, PE-09 | Overrides are per-action, attributable, and logged; a non-overridable category exists for actions outside the harness's own authority to grant (e.g., infrastructure it doesn't own). |
| AHES-PE-05–08 | Denials state rationale and the legitimate path; the enforcement-point inventory and the policy document are automatically cross-checked for consistency at session start (a documented failure mode this standard names explicitly: a control live in code but undisclosed in policy is a defect, not a bonus). |
| AHES-PE-10 | An explicit conflict-priority rule (prohibitions outrank obligations outrank permissions) with a stated same-tier default: pause for human input rather than resolve silently. |

## §8 Human authority — *Morals*

| Requirement | Design decision |
|---|---|
| AHES-HA-01–03 (approval classes) | Flagged actions map to a named approval class, not an ad hoc "ask the human" — the class determines who's authorized to approve and what they need to see. |
| AHES-HA-04–06 (escalation) | An approval that sits unresolved past a defined window escalates rather than silently blocking or silently proceeding. |
| AHES-HA-07–09 (accountable acceptance) | Every approval decision is attributed to a named human authority and recorded — approval is an evidenced act, not an implicit one. |

## §9 Principal & authority model — *Mission*

| Requirement | Design decision |
|---|---|
| AHES-PA-01–03 (principal identity) | Every session establishes a principal context at start (§2 lifecycle step 1): who or what this action is ultimately under, distinct from which agent role is executing it. |
| AHES-PA-04–06 (altitude, delegation depth) | Authority carries an altitude marker (standing/enterprise-level direction vs. tactical/session-level direction) and, when delegated onward, a recorded depth and bound — not assumed ambient from context. |
| AHES-PA-07–09 (attribution) | Every consequential action's evidence record names the principal-authority chain it executed under, not just the immediate caller. |

## §10 Evidence engineering — *Memory*

| Requirement | Design decision |
|---|---|
| AHES-EE-01–03 (record shape) | A closed, versioned schema for evidence records; a record that doesn't validate isn't written, full stop — no free-text fallback path. |
| AHES-EE-04–06 (attribution, ordering) | Each record carries a collision-resistant ID, a session ID, and a monotonic per-session sequence number — total order recoverable independent of clock skew. |
| AHES-EE-07–09 (baseline binding, integrity) | Every record states the configuration baseline active when it was written and the authority class the underlying action fell under, so a postmortem can classify a failure's locus (→ Part 3 §3.4) from evidence alone. |

## §11 Evaluation — *Mind / Methods*

| Requirement | Design decision |
|---|---|
| AHES-EV-01–03 (gating, lifecycle integration) | A unit of work's completion is not the generating role's self-report. Completion validates only against evidence that a plan artifact, a generated artifact, and an independent-verification artifact all exist and are linked — a claim representing only that files exist, with no evidence the verification step actually ran, does not validate. This is a structural gate, checked mechanically, not a review policy someone can skip under time pressure. |
| AHES-EV-04–06 (bar-first, component evaluation) | The pass/fail bar for a given change is fixed before the change is evaluated against it, so the bar can't be quietly fitted to whatever the change happens to do. |
| AHES-EV-07–09 (adversarial evaluation, drift baseline) | Adversarial test cases (known prior failure modes) are replayed against every policy change, not just new ones. The qualifying evaluation run is the same baseline §12 watches for drift against — one baseline, not two. |

## §12 Operational assurance — *Methods*

| Requirement | Design decision |
|---|---|
| AHES-OA-01–03 (monitoring, drift) | Running behavior is compared against the §11 qualification baseline continuously, not just at deploy time. |
| AHES-OA-04–06 (anomaly detection, incident tracking) | A detected drift or anomaly opens a tracked incident with an owner, not a log line no one reads. |
| AHES-OA-07–09 (rollback) | A qualified prior baseline stays available as a rollback target until the current one has run clean for a defined observation window. |

## §13 Supply-chain control — *Means*

| Requirement | Design decision |
|---|---|
| AHES-SC-01–03 (provenance) | Every external dependency (models, packages, connectors, third-party tools) has a recorded source and version, checked at build/deploy time. |
| AHES-SC-04–06 (integrity, update policy) | Dependencies are pinned and vendored-in where the blast radius of a live external coupling is unacceptable, rather than live-linked to a source outside the harness's own change control (the same self-containment discipline §16 uses for its portability payload). |
| AHES-SC-07–09 (blast-radius awareness) | A dependency's blast radius on compromise is documented at onboarding (→ Part 1 §1.2.3-adjacent discipline), not discovered during an incident. |

## §14 Security architecture — *Morals*

| Requirement | Design decision |
|---|---|
| AHES-SA-01–03 (identity, least privilege) | Every credential is scoped to the narrowest role and principal that needs it — no ambient "the harness has full access" grant that individual roles inherit by default. |
| AHES-SA-04–06 (credential boundaries) | Credentials never enter model context; they're held and used only at the harness layer, referenced by pointer where a role needs to invoke something credentialed. |
| AHES-SA-07–09 (containment, data protection) | A compromised or misbehaving role's blast radius is bounded by its own declared access level (§4) — privilege doesn't silently aggregate across composed tools. |

## §15 Configuration baseline — *Memory*

| Requirement | Design decision |
|---|---|
| AHES-CB-01–03 (versioned bundle) | Models, instructions, tools, policy, memory schema, and evaluation suite are versioned together as one bundle, not independently — "the deployment as of version N" is a single answerable question. |
| AHES-CB-04–06 (release discipline) | A baseline change is a bracketed release event (reviewed, evaluated, recorded) — never a live edit to a running configuration. |
| AHES-CB-07–09 (retention, resolvability) | A baseline version referenced by an evidence record (§10) or a conformance claim (Part 5) stays resolvable for as long as that record or claim is retained — a baseline doesn't get garbage-collected out from under something that still cites it. |

## §16 Substrate portability — *Means*

| Requirement | Design decision |
|---|---|
| AHES-SP-01 (portability claim) | The deployment states explicitly what's invariant across substrate substitution (identity, policy, memory content) and what may vary (latency, phrasing, bounded quality deltas) — a claim that doesn't delimit its own bound isn't a portability claim. |
| AHES-SP-02–04 (portable payload) | A schema-defined governance envelope — covering the same governing surfaces this standard's own domain vocabulary specifies — is carried across every substitution boundary, brought in and owned rather than live-coupled to wherever it came from. |
| AHES-SP-05, SP-08 (authority, memory portability) | Authority and memory both travel as data inside the envelope, not assumed from ambient context on the new substrate. |
| AHES-SP-06 (measured bound) | The portability claim is demonstrated by an evaluation that actually measures the behavioral delta across a real substitution with the payload held constant — not asserted from the carrier's existence alone. This is the gap this standard's own §16 clause names as the domain's recurring failure mode (a shipped carrier with no measurement), and the reference harness treats the measurement as the requirement, not the carrier. |
