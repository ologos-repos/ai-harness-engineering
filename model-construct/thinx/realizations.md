# Model Construct — thinx — Domain Realizations

Consolidated view of every thinx-based informative example that appears inline across Part 2 §N.6. Each section restructures the existing clause example into the standard mapping form (*AHES clause concern → pattern observed → how thinx satisfies it*) and points back to its source clause. Nothing here is more specific about thinx than what is already published in Part 2 — see the [access note](README.md#access-note). Read [`README.md`](README.md) first for the self-citation-bias caveat that governs how much weight these mappings should carry.

---

## §1 — Model mediation

*Source: [`standard/part-2-domains/01-model-mediation.md`](../../standard/part-2-domains/01-model-mediation.md) §1.6.*

Realizes model mediation as the seam that keeps identity, policy, and memory constant while the model varies.

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| Constant identity across substitution | Mediation seam isolates model choice | One durable agent identity runs across interchangeable model substrates; the model is the only thing that varies |
| Deliberate selection (not auto-escalation) | Per-surface binding | A latency-sensitive channel binds a lighter model, depth work a heavier one; selection is set deliberately, not auto-escalated |
| Attribution to a concrete model | Selection recorded, running model captured per session | Behavior is always attributable to a concrete model + version |
| MM-06 fail-closed fallback | Boundary-crossing resilience is a defect | A fallback routing silently to an unqualified model reintroduces the capability drift the domain exists to prevent; crossing a qualification/trust boundary is treated as a defect, not a feature |

The same seam is the single place a substitution is qualified, pinned, and — on regression — rolled back, which is what makes §16 portability testable rather than aspirational.

---

## §2 — Context engineering

*Source: [`standard/part-2-domains/02-context-engineering.md`](../../standard/part-2-domains/02-context-engineering.md) §2.6.*

Realizes the compaction-survival controls CE-05/CE-06 for a harness whose history is auto-compacted when the window fills.

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| CE-05 governance surface must persist | Named risk class: governing instructions summarized away mid-work | Policy, agent identity, principal authority, and active mode are declared a non-compactable, pinned set |
| CE-06 reload from canonical source | Reconstruct, don't carry as summarizable text | The pinned surface is reloaded from canonical source at every context boundary rather than held as compactable prose |
| Structural refusal | Compaction that drops the pin = authority bypass | A compaction step that would summarize away any pinned surface is treated as structurally equivalent to authority bypass and refused |

This is the context-domain precondition for the §7 judgment layer to stay sound: policy can only be applied if the policy content is reliably present in context.

---

## §3 — Tool engineering

*Source: [`standard/part-2-domains/03-tool-engineering.md`](../../standard/part-2-domains/03-tool-engineering.md) §3.6.*

Realizes tool engineering as the classification the enforcement layer (→ §7) binds to.

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| TE-02 read-only vs. mutating split | Enforcement keys on side-effect class | Read-only capabilities are exempted from mediation; mutating and externally-visible ones are routed through pre-execution enforcement |
| TE-06 classification is the binding fact | The governance stack binds to the tool's declared class | A tool's side-effect classification is the fact the whole stack keys on, so a policy enforcement point can make a sound decision at all |
| TE-07 conservative default | Ambiguity resolved in the fail-safe direction | An unclassified tool is treated as the most dangerous class until proven otherwise |
| Resolved-target agreement (→ §7) | Class matches the operation, not the phrasing | Where a side effect is the resolved target (a repository push, a file write), classification and enforcement agree on that resolved target, not on the argument as typed |

---

## §6 — Memory engineering

*Source: [`standard/part-2-domains/06-memory-engineering.md`](../../standard/part-2-domains/06-memory-engineering.md) §6.6.*

A layered memory realization on the MxM (multi-mode meta-harness) reference architecture.

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| ME-A2 / ME-08 bounded recall surface | Two-tier store | A bounded, curated durable layer for frequent recall sits over a separate rarely-read raw-transcript store, so the recall surface stays searchable |
| ME-01 provenance | Distilled notes carry origin | Distilled notes carry their own dating and origin |
| ME-03 staleness handling | Observed-over-recalled convention | A stated convention holds that directly observed current state overrides recalled fact when the two conflict — the memory-side complement of the §7 judgment layer weighing stale recall |
| ME-05 secrets out of durable memory | Pointer, not copy | A soft-stop discipline keeps secret material out of durable memory, storing pointers rather than copies |
| ME-09 must-be-present surfaces | Structural load, not recall retrieval | Identity/policy surfaces that must be present every session load structurally at session start rather than through recall |

---

## §7 — Policy enforcement (the fully worked clause)

*Source: [`standard/part-2-domains/07-policy-enforcement.md`](../../standard/part-2-domains/07-policy-enforcement.md) §7.6.*

The most-developed thinx mapping in the standard; grok-console supplies a structurally different second data point at [`../grok-console/07-policy-enforcement.md`](../grok-console/07-policy-enforcement.md).

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| PE-A1 policy document | Enumerated policy | A policy document covering hard-stops, flagged actions, and trust scope |
| PE-06 structural enforcement point | Pre-execution dispatcher | A dispatcher hook as the structural layer, repo-versioned so a fresh checkout carries enforcement with it |
| PE-03 resolved-target gating | Real-target resolution | The highest-consequence gates resolve the real target rather than matching surface patterns |
| PE-04 attributable override | Per-command human marker + log | A per-command, human-authorized override marker with an append-only audit log |
| PE-09 non-overridable class | Hard category for third-party infra | A non-overridable category for infrastructure owned by a third party |
| PE-07 document ↔ inventory consistency | Session-start self-test | A self-test asserting the enforcement layer is actually wired in at session start; motivated by a documented incident class where gates were live but undisclosed in the policy document, caught in an audit pass — which is why the consistency is a named requirement, not an assumption |

---

## §8 — Human authority

*Source: [`standard/part-2-domains/08-human-authority.md`](../../standard/part-2-domains/08-human-authority.md) §8.6.*

The same §7 PE-04 machinery, read as a human-authority realization.

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| HA-06 override form | Structural denial releasable only by a human | A structural denial is releasable only by a per-command marker a human must add in-conversation |
| HA-02 named accountable human | Override tied to a human | The override is tied to a named accountable human |
| HA-04 per-action granularity | One marker per command | Release is at per-action granularity, each use appended to an audit log |
| PE-09 tie-through | Non-releasable class | A category of denials that no marker can release |
| HA-09 present-at-start | Session-open self-check | The gate machinery is asserted present at start rather than assumed |

---

## §9 — Principal authority

*Source: [`standard/part-2-domains/09-principal-authority.md`](../../standard/part-2-domains/09-principal-authority.md) §9.6.*

A reference deployment on the MxM (multi-mode meta-harness) reference architecture, representing principal authority as an explicit altitude.

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| PA-02 principal as first-class state | Altitude representation | Authority is represented as an explicit altitude — an operator-altitude tactical principal directing day-to-day work, distinguished from a higher standing-intent altitude that binds durable posture |
| PA-02 action attribution | Under a named principal, not the agent | Actions are taken *under* a named principal at a named altitude rather than under the agent's own identity |
| PA-03 altitude precedence | Documented conflict resolution | Altitude is carried and, on conflict, resolved by a documented precedence rather than model discretion |
| PA-06 trusted-default scope | Pre-granted, not absent | The autonomous, no-gate action scope is authority *pre-granted by* the operator principal, not authority absent |

---

## §10 — Evidence engineering (narrower realization)

*Source: [`standard/part-2-domains/10-evidence-engineering.md`](../../standard/part-2-domains/10-evidence-engineering.md) §10.6.*

An honest *partial*: the §7 override log is a single-control audit trail, not a general evidence architecture. grok-console supplies the schema-first contrast at [`../grok-console/10-evidence-engineering.md`](../grok-console/10-evidence-engineering.md).

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| EE-01 / EE-07 audit trail for a control | Append-only single-purpose log | A single-purpose, append-only override log — sufficient for one enforcement layer's audit need for that event class |
| General §10 architecture | *Not discharged* | The log does not cover session lifecycle or event types beyond overrides; it illustrates that a per-control audit trail satisfies its own domain's §N.5 without, on its own, discharging the general §10 architecture |

---

## §11 — Evaluation

*Source: [`standard/part-2-domains/11-evaluation.md`](../../standard/part-2-domains/11-evaluation.md) §11.6.*

Realizes evaluation as a gate wired into the session lifecycle rather than a separate test phase.

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| EV-01 gate before completion | Wrap-time pass/fail | A wrap-time check that must pass before work is claimed complete |
| EV-09 consistency check at start | Harness-floor self-test | A self-test asserting the enforcement layer is actually wired in before a session proceeds |
| EV-07 adversarial evaluation | Attack-transcript replay | Replay of prior attack transcripts against the current policy layer, so a bypass caught once is re-checked on every change rather than re-discovered |

The qualifying results this regime produces are the same baseline the §12 operational-assurance surface watches for drift against — one baseline, not two.

---

## §12 — Operational assurance

*Source: [`standard/part-2-domains/12-operational-assurance.md`](../../standard/part-2-domains/12-operational-assurance.md) §12.6.*

Realizes drift detection as a durable signal feed rather than an ephemeral impression.

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| OA-02 drift detection | Durable append-only feed | A model-fit mismatch (running model diverging from the shape of the work) is logged to an append-only record that an operator console panel surfaces, so the signal survives the session that noticed it |
| OA-01 / OA-03 recurring monitoring | Session-start readiness report | Daemon state, version-control state, and recent-activity ages presented against expected-normal each session |
| OA-06 rollback | Baseline-revert shared with §1 | A regressing model or instruction change reverts to a prior pinned baseline without code change |
| OA-09 incident feedback | Closed loop with §11 | A production incident feeds back as a new evaluation case, so evaluation and monitoring form one closed loop |

---

## §13 — Supply chain

*Source: [`standard/part-2-domains/13-supply-chain.md`](../../standard/part-2-domains/13-supply-chain.md) §13.6.*

Reduces transitive trust by policy.

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| SC-01 / SC-08 in-gate dependency graph | Vendor-in over reference | Rather than depending on an external repository's live content, useful logic is copied in and owned locally, so an upstream change cannot become a harness behavior change without passing through the local baseline; the maintenance cost of vendored code is accepted deliberately |
| SC-05 model as a dependency class | Model change is an admission event | Models are governed as a dependency class rather than a routing detail, so a model change is an admission event, not a silent substitution |

---

## §14 — Security architecture

*Source: [`standard/part-2-domains/14-security-architecture.md`](../../standard/part-2-domains/14-security-architecture.md) §14.6.*

Enforces the credential boundary as a standing rule.

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| SA-02 / SA-09 credential boundary | Pointer, not copy | Secrets live only in source (a secret store / `.env`); the durable memory layer holds pointers, never copies — slated for structural enforcement by a memory-write gate so a credential-bearing write is refused at the boundary rather than caught in review |
| SA-05 / SA-07 read/hold boundary | Harness-side resolution | Credentialed capabilities are resolved on the harness side, so the model exercises a capability without being shown the secret — keeping the boundary structural rather than conventional |

---

## §15 — Configuration baseline (non-normative pointer)

*Source: [`standard/part-2-domains/15-configuration-baseline.md`](../../standard/part-2-domains/15-configuration-baseline.md) §15.6.*

Flagged **non-normative** in the source: §15.6 states a fully worked, deployment-grounded realization is *not yet drafted*. Consolidated here as a pointer only, at the abstraction level already published.

| AHES concern | Pattern observed | How thinx satisfies it |
|---|---|---|
| CB-01 / CB-04 / CB-07 baseline as a unit | Config as a version-controlled release | Configuration surfaces (identity/instruction files, tool and policy definitions, memory schemas, evaluation suites) are treated as a version-controlled unit whose release is a bracketed lifecycle event rather than continuous live edit |

This clause is a candidate for a fuller `model-construct/` write-up alongside the §10 realization — not yet worked to the depth of the other sections above.
