# AHES Part 4 — Risk-to-Control Mapping

**Status: Draft v0.1 — risk register framed (§4.2); per-risk control traces drafted (§4.3). Mitigating-requirement citations are by Part 2 domain name/number pending finalization of Part 2 requirement IDs; a consistency pass will backfill exact IDs.**

## 4.1 Purpose

ISO/IEC 23894 and the NIST AI RMF structure AI risk but prescribe no technical architecture to mitigate it. This part is the traceability bridge: each harness-relevant risk maps to the Part 2 controls that mitigate it, so a risk assessment can cite enforceable requirements rather than narrative intent. The mapping is bidirectional — every Part 2 `shall` traces to at least one risk, and every register risk traces to controls or to an explicitly accepted residual.

## 4.2 Harness risk register (quick reference)

The table is the index; §4.3 carries the full entry for each row. Every risk in the table has a matching §4.3 subsection (R1–R14) and vice versa.

| # | Risk | Primary Part 2 domains | AI RMF function |
|---|---|---|---|
| R1 | Prompt injection (direct and via retrieved/tool content) | §2, §3, §7 | Manage |
| R2 | Excessive agency / unbounded delegation | §4, §8, §9 | Govern, Manage |
| R3 | Approval bypass | §7, §8 | Govern |
| R4 | Hallucinated actions (fabricated targets, invented parameters) | §3, §7, §11 | Measure, Manage |
| R5 | Uncontrolled propagation (agent-spawned work exceeding mandate) | §4, §5 | Manage |
| R6 | Data disclosure (credentials or sensitive data into context/memory/logs) | §2, §6, §14 | Manage |
| R7 | Unreliable retrieval treated as ground truth | §2, §10 | Measure |
| R8 | Memory poisoning / stale-memory action | §6 | Manage |
| R9 | Model drift and silent regression | §1, §11, §12 | Measure |
| R10 | Model selection/substitution risk | §1, §16 | Map, Manage |
| R11 | Supply-chain compromise (models, prompts, tools, packages, connectors) | §13 | Map, Manage |
| R12 | Insufficient evidence (unreconstructible incidents) | §10, §15 | Govern |
| R13 | Identity confusion (agent acting under wrong/no principal authority) | §9, §14 | Govern |
| R14 | Context-boundary loss of governing instructions (compaction, truncation) | §2, §7 | Manage |

## 4.3 Risk entries

Each entry has a fixed shape: **risk statement · affected quality characteristics (→ Part 3) · mitigating requirements (Part 2 domains) · verification pointer (the domain §N.4 that catches a mitigation failure) · residual · acceptance authority.** Every entry names a non-empty residual: a full-mitigation risk with zero residual is a red flag, because no harness control fully eliminates the underlying model or operational uncertainty — a claimed zero residual is more likely an unexamined boundary than a solved problem.

Mitigating requirements are cited by domain name and number, not by exact requirement ID, because Part 2's per-domain IDs are being finalized concurrently. A consistency pass will backfill exact IDs where a specific control is the load-bearing one.

### R1 — Prompt injection (direct and via retrieved/tool content)

**Risk statement.** Adversarial instructions reach the model through the input surface — directly in user input, or indirectly embedded in retrieved documents, tool outputs, or memory — and steer the harness into actions no authority intended.

**Affected quality characteristics.** Robustness (primary), Security, Controllability, Functional suitability.

**Mitigating requirements.** §2 Context engineering — provenance labeling of every context element and isolation of untrusted (retrieved / tool-returned) content from trusted instruction content; §3 Tool engineering — precondition checks and side-effect classification so a steered tool call is still gated on its declared effects; §7 Policy enforcement — structural hard-stops that a steered model cannot reason its way past, since the policy's subject *is* the reasoning engine.

**Verification pointer.** §7.4 (adversarial test — attempt each hard-stopped class including via injected/indirect instruction and verify structural denial); §2's verification criteria for provenance and trusted/untrusted isolation. A mitigation failure surfaces as a hard-stopped action executing under injected instruction.

**Residual.** Injection that stays *within* the trusted, permitted action envelope — steering the model toward a harmful-but-allowed action — is not caught structurally; it falls to the judgment layer and to evaluation coverage, neither of which is exhaustive. Novel indirect-injection vectors precede the provenance rules that would isolate them.

**Acceptance authority.** §8 Human authority — the accountable approver for the affected action classes accepts the judgment-layer residual.

### R2 — Excessive agency / unbounded delegation

**Risk statement.** An agent pursues goals or delegates to sub-agents beyond the scope its principal authorized, accumulating capability or reach that no single approval sanctioned.

**Affected quality characteristics.** Controllability (primary), Security, Functional suitability, Reliability.

**Mitigating requirements.** §4 Agent engineering — bounded delegation depth, explicit termination conditions, and per-agent permission scoping; §8 Human authority — approval classes gating consequential actions and accountable acceptance of scope expansion; §9 Principal & authority model — attribution of every action to the principal and altitude it was taken under, so authority is never implicit.

**Verification pointer.** §4's verification criteria for delegation-depth bounds and termination; §9's verification of authority attribution. A failure surfaces as an action whose authority chain cannot be traced to a principal, or a delegation exceeding its declared depth.

**Residual.** Actions individually within scope can compose into an aggregate effect exceeding intent without any single step tripping a bound; a principal can authorize a broad scope that later proves too broad. Bounds constrain structure, not the wisdom of the granted scope.

**Acceptance authority.** §9 Principal & authority model — the principal accepts the residual of the scope they granted; §8 governs the per-action approval residual.

### R3 — Approval bypass

**Risk statement.** A consequential action executes because the model reasoned its way to it, when no accountable authority permitted it — policy existed only as advisory context the model misweighed or was steered around.

**Affected quality characteristics.** Controllability (primary), Security, Usability.

**Mitigating requirements.** §7 Policy enforcement — pre-execution structural enforcement points, hard-stops not bypassable by model reasoning, real-target resolution, per-action attributable overrides with an append-only audit log, and document↔inventory consistency; §8 Human authority — defined approval classes with accountable approvers behind the flagged actions the structural layer surfaces.

**Verification pointer.** §7.4 (adversarial test that every hard-stopped class is structurally denied including via indirection; override-without-authority fails; append-only property holds). A mitigation failure surfaces directly as a hard-stopped action that executed.

**Residual.** Actions the policy classifies as *trusted* proceed without approval by design; a mis-classification (an action that should have been flagged but sits in the trusted default scope) bypasses approval without any structural failure. The trusted/flagged boundary is a human judgment that can be wrong.

**Acceptance authority.** §8 Human authority — the named authority who owns the policy document accepts the residual of the trusted/flagged classification.

### R4 — Hallucinated actions (fabricated targets, invented parameters)

**Risk statement.** The model emits an action referencing a target or parameter that does not correspond to reality — a non-existent file, a mis-resolved remote, an invented identifier — and the harness executes it against whatever the fabricated reference actually resolves to.

**Affected quality characteristics.** Functional suitability (primary), Reliability, Robustness, Controllability.

**Mitigating requirements.** §3 Tool engineering — preconditions that validate a target exists and matches its declared type before execution, and postcondition checks; §7 Policy enforcement — real-target resolution rather than surface-pattern matching, so the *resolved* target is what the gate sees; §11 Evaluation — component and adversarial evaluation measuring fabrication rates against the tool surface, including the schema-level claim-verification pattern in [`model-construct/mxh-p/`](../model-construct/mxh-p/) (a completion claim cannot validate without evidence of an actual planned-generated-evaluated chain, structurally refusing to treat an unverified "this is done" as equivalent to a verified one).

**Verification pointer.** §3's precondition/postcondition verification and §7.4's disguised-target tests (renamed paths, remotes under unexpected local names). A failure surfaces as an action executing against a target that precondition validation should have rejected.

**Residual.** A fabricated target that *coincidentally resolves to a real, valid, permitted object* passes every precondition — the action is well-formed and permitted; only its intent was wrong. Structure validates existence and permission, not correspondence to intent.

**Acceptance authority.** §8 Human authority — accepts the residual for the affected action classes; consequential fabrications should sit behind an approval class.

### R5 — Uncontrolled propagation (agent-spawned work exceeding mandate)

**Risk statement.** An agent spawns sub-agents or work items that themselves spawn further work, propagating execution beyond the mandate of the originating request without a converging termination.

**Affected quality characteristics.** Controllability (primary), Reliability, Performance efficiency, Functional suitability.

**Mitigating requirements.** §4 Agent engineering — termination conditions, delegation-depth bounds, and recovery/cancellation semantics for spawned work; §5 Workflow engineering — orchestration with checkpoints, idempotency, and compensation so runaway propagation is checkpointed and reversible rather than fire-and-forget.

**Verification pointer.** §5's verification of checkpoint/compensation and idempotency; §4's delegation-depth and termination verification. A failure surfaces as spawned work continuing past its termination condition or lacking a compensation path.

**Residual.** Work already committed by propagated agents before a bound trips may have external side effects that compensation cannot fully reverse (a sent message, a mutated external record). Bounds limit further propagation, not the irreversibility of what already executed.

**Acceptance authority.** §8 Human authority — accepts the residual of irreversible side effects within the authorized envelope.

### R6 — Data disclosure (credentials or sensitive data into context/memory/logs)

**Risk statement.** Secrets or sensitive data flow into a durable or wide-reach surface — model context, persistent memory, or evidence logs — where they are exposed beyond their intended boundary or persist past their intended lifetime.

**Affected quality characteristics.** Security (primary), Transparency, Maintainability.

**Mitigating requirements.** §2 Context engineering — provenance and content controls governing what may enter context; §6 Memory engineering — scope, provenance, expiration, and a store discipline that holds pointers to secrets rather than copies; §14 Security architecture — credential boundaries, least privilege, and containment so secrets are not reachable into the harness's durable surfaces in the first place.

**Verification pointer.** §14's credential-boundary verification and §6's expiration/scope verification. A failure surfaces as a credential-bearing value found in a memory store or evidence log during inspection.

**Residual.** Data whose sensitivity is not recognized at write time (an unrecognized secret format, sensitive content in free text) escapes format-based controls; evidence capture inherently records inputs and actions, so a disclosure that occurs *within* a legitimately captured action is preserved by the evidence architecture itself.

**Acceptance authority.** §8 Human authority — accepts the residual of unrecognized-sensitivity data; where the disclosure boundary is a principal-scope question, §9 co-governs.

### R7 — Unreliable retrieval treated as ground truth

**Risk statement.** Retrieved content of uncertain accuracy or provenance is consumed by the model as authoritative fact, and the harness acts on a conclusion built on an unreliable source.

**Affected quality characteristics.** Functional suitability (primary), Robustness, Transparency, Reliability.

**Mitigating requirements.** §2 Context engineering — provenance for every retrieved element and isolation of retrieved content from trusted instruction, so retrieval is marked as *claimed* rather than *given*; §10 Evidence engineering — capture of retrieval provenance sufficient to reconstruct what source drove a conclusion.

**Verification pointer.** §2's provenance/isolation verification; §10's reconstruction verification (can the source behind a conclusion be recovered from evidence). A failure surfaces as a conclusion whose driving source cannot be traced, or retrieved content indistinguishable from trusted instruction in captured context.

**Residual.** Provenance marks *where* content came from, not *whether it is true*; a high-provenance source can still be wrong, and the model may over-weight marked-uncertain content regardless of the mark. Truth-assessment of sources is not a structural property the harness can supply.

**Acceptance authority.** §8 Human authority — accepts the residual accuracy risk for retrieval-driven action classes.

### R8 — Memory poisoning / stale-memory action

**Risk statement.** Persisted memory carries a value that is adversarially planted, corrupted, or simply stale, and a later session acts on it as current fact — the current world state contradicts the recalled one.

**Affected quality characteristics.** Reliability (primary), Robustness, Security, Functional suitability.

**Mitigating requirements.** §6 Memory engineering — provenance on every memory entry, expiration policy, cross-session isolation, and a discipline that current observed state overrides recalled state on conflict rather than the reverse.

**Verification pointer.** §6's verification of expiration, provenance, and cross-session isolation. A failure surfaces as an action taken on a memory value that observable current state contradicted, or a memory entry crossing a session-isolation boundary it should not.

**Residual.** Memory that is stale but not *expired*, and not contradicted by any state the session happened to observe, is acted on as current — the harness cannot verify every recalled fact against the world on every use. Expiration bounds staleness by time, not by truth.

**Acceptance authority.** §8 Human authority — accepts the residual staleness risk; where poisoning implicates a principal's authority to write memory, §9 co-governs.

### R9 — Model drift and silent regression

**Risk statement.** A model's behavior changes — through provider-side updates, version rollover, or environmental shift — degrading mission performance without any error being raised, so the regression is silent until an incident surfaces it.

**Affected quality characteristics.** Reliability (primary), Functional suitability, Maintainability, Performance efficiency.

**Mitigating requirements.** §1 Model mediation — version pinning and controlled substitution so behavior change is a deliberate, recorded event rather than a silent one; §11 Evaluation — regression and mission-level evaluation run against a baseline to detect behavioral drift; §12 Operational assurance — drift and anomaly monitoring in operation.

**Verification pointer.** §11's regression-evaluation verification against a pinned baseline; §12's drift-detection verification. A failure surfaces as a behavioral change that reached operation without an evaluation gate catching it.

**Residual.** Evaluation covers the behaviors it measures; drift in an unmeasured behavior, or drift subtle enough to stay within evaluation tolerances while still degrading a specific mission, escapes detection until it manifests. Coverage is finite; behavior space is not.

**Acceptance authority.** §8 Human authority — accepts the residual of un-evaluated behavior space for the deployment's mission profile.

### R10 — Model selection/substitution risk

**Risk statement.** A model is selected or substituted — for cost, availability, or capability — and the substitution introduces behavioral change that the harness's identity, policy, and memory do not survive, or that silently alters mission outcomes.

**Affected quality characteristics.** Portability (primary), Reliability, Compatibility, Functional suitability.

**Mitigating requirements.** §1 Model mediation — routing, substitution, versioning, fallback, and capability gating as an engineered abstraction rather than an ad hoc swap; §16 Substrate portability — the requirement that harness identity, policy, and memory survive model substitution with *bounded, specified* behavioral change, making substitution a testable conformance target.

**Verification pointer.** §16's AHES-SP-06 (adversarial substitution test: measure the behavioral delta with the governing payload held constant; invariant surfaces unchanged, variable surfaces within the stated bound); §1's fallback/capability-gating verification. A failure surfaces as a substitution producing behavioral change outside the specified bound.

**Residual.** The behavioral-change bound is *specified*, not zero — MxM (multi-mode meta-harness) portability guarantees bounded change, not identical behavior. Substitution to a model outside the capability envelope the harness was engineered against exceeds any bound the harness can honor.

**Acceptance authority.** §9 Principal & authority model — the principal accepts the specified behavioral-change bound as the substitution envelope; §8 governs per-substitution operational acceptance.

### R11 — Supply-chain compromise (models, prompts, tools, packages, connectors)

**Risk statement.** A component the harness depends on — a model, a prompt asset, a tool implementation, a package, or an external connector — is compromised or substituted upstream, introducing malicious or altered behavior the harness ingests as trusted.

**Affected quality characteristics.** Security (primary), Reliability, Maintainability, Functional suitability.

**Mitigating requirements.** §13 Supply-chain control — provenance, integrity verification, and controlled admission for every dependency class (models, prompts, tools, data, packages, connectors, external services), so an upstream substitution is detected at admission rather than trusted by default.

**Verification pointer.** §13's integrity-and-provenance verification for each dependency class. A failure surfaces as a dependency admitted without integrity verification, or an integrity check that a substituted component passed.

**Residual.** Integrity verification confirms a component is the one expected, not that the expected component is itself benign — a compromise upstream of the trust anchor (a poisoned-but-correctly-signed artifact) passes verification. First-use of a new dependency precedes the provenance baseline that would flag its later alteration.

**Acceptance authority.** §8 Human authority — accepts the residual trust in verified-but-not-independently-audited dependencies; where a connector represents an external principal relationship, §9 co-governs.

### R12 — Insufficient evidence (unreconstructible incidents)

**Risk statement.** An incident occurs and the captured evidence is inadequate to reconstruct what happened — which model, which context, which action, under which authority and configuration baseline — so accountability and separability (Part 3) both fail.

**Affected quality characteristics.** Transparency (primary), Maintainability, Controllability.

**Mitigating requirements.** §10 Evidence engineering — durable, attributable capture of inputs, context provenance, model identity/version, actions, approvals, policy decisions, and configuration baseline at time of action, sufficient for post-hoc reconstruction; §15 Configuration baseline — the versioned baseline that evidence references, and the retention rule governing how long evidence is held.

**Verification pointer.** §10's reconstruction verification (can a past action be fully reconstructed from evidence alone) and §15's baseline-provenance and retention verification. A failure surfaces as an incident that cannot be classified M/H/S/O per Part 3 §3.4 from captured evidence.

**Residual.** Evidence captures what was instrumented; a failure mode no one anticipated instrumenting is not recorded, and retention windows bound how far back reconstruction reaches. The evidence architecture is complete only against the failure classes it was designed for.

**Acceptance authority.** §8 Human authority — accepts the residual of un-instrumented failure classes and the retention-window boundary as an evidence-scope decision.

### R13 — Identity confusion (agent acting under wrong/no principal authority)

**Risk statement.** An agent takes an action attributed to the wrong principal, or to no principal at all — authority is implicit, mis-attributed, or absent — so the action cannot be tied to an accountable directing intent.

**Affected quality characteristics.** Controllability (primary), Security, Transparency.

**Mitigating requirements.** §9 Principal & authority model — explicit principal identity, delegation depth, altitude, and authority attribution for every action, so no action executes under implicit or absent authority; §14 Security architecture — identity and least-privilege boundaries that bind an agent's identity to its permitted scope.

**Verification pointer.** §9's authority-attribution verification (every action traces to a named principal and altitude) and §14's identity-boundary verification. A failure surfaces as an action whose principal attribution is absent or inconsistent with its permission scope.

**Residual.** Attribution records *which* principal authority an action ran under; it cannot confirm that the principal's directing intent was genuinely present for that specific action versus inherited from a broad standing grant. A correctly-attributed action under an over-broad standing authority is still correctly attributed.

**Acceptance authority.** §9 Principal & authority model — the principal is the accountable authority for the residual of their own attribution and standing grants.

### R14 — Context-boundary loss of governing instructions (compaction, truncation)

**Risk statement.** Governing instructions — policy content, identity, standing constraints — are summarized away or truncated at a context boundary (compaction, window overflow), and the harness continues to act with its governing frame silently degraded.

**Affected quality characteristics.** Controllability (primary), Reliability, Robustness, Security.

**Mitigating requirements.** §2 Context engineering — pinning of governing surfaces as non-compactable, re-loading them from canonical sources at every context boundary so a compaction step cannot drop them; §7 Policy enforcement — structural hard-stops that hold *regardless* of context state, so even a fully-degraded judgment layer cannot execute a hard-stopped action (the structural layer is the floor that survives context loss).

**Verification pointer.** §2's pinning verification (governing surfaces survive a context boundary and re-load from canonical source) and §7.4's PE-08 inspection (judgment-layer policy loaded at every session start and surviving boundaries). A failure surfaces as a governing surface absent after a compaction event, or a hard-stop that weakened because context degraded.

**Residual.** Structural hard-stops survive context loss, but the *judgment* layer — everything structure cannot express (blast-radius reasoning, unfamiliar-state caution) — degrades with the context it lives in; pinning reduces but cannot eliminate the window where a boundary event has occurred and re-load has not yet completed. The floor holds; the judgment above it is only as current as the last successful re-load.

**Acceptance authority.** §8 Human authority — accepts the residual judgment-layer degradation across context boundaries as an operational envelope constraint.

## 4.4 Bidirectional-completeness note

Per §4.1, the mapping is intended to be bidirectional: every register risk (R1–R14) traces above to mitigating controls or to a named residual, and every Part 2 `shall` should trace back to at least one register risk. The reverse trace — Part 2 → risk — cannot be completed until Part 2's requirement IDs stabilize. The final consistency pass shall (a) backfill exact requirement-ID citations into the mitigating-requirements and verification-pointer fields above, and (b) verify that no Part 2 `shall` lacks a risk in this register, opening a new register row if one is found.
