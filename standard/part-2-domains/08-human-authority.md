# AHES Part 2 §8 — Human Authority

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 8.1 Purpose

Human authority governs the approval architecture: which action classes require which form of human approval, escalation paths, override mechanics, and accountable acceptance of outcomes. Unengineered, the failure class is **authority laundering**: consequential actions execute under an approval that was never actually given at the required level, or approval fatigue degrades every gate to a rubber stamp. This domain is the *process* of obtaining a human's yes/no and binding it to the action it licensed; it is distinct from §7, which is the structural machinery that refuses actions regardless of model output, and from §9, which is the *representation* of whose authority any action — approved or fully autonomous — was taken under.

The domain names three approval forms, each attached to a **human authority class** (§0.3):

| Form | Nature | When required |
|---|---|---|
| Pre-authorization (gate) | A human's decision is captured *before* the action executes | Action classes whose consequences are hard to reverse or exceed trusted scope |
| Post-action acceptance | A human accountably accepts the outcome *after* an autonomous action | Action classes permitted autonomously but requiring a named owner of the result |
| Override | A human authorizes a single action a structural control would otherwise deny (→ §7 PE-04) | Per-action release of a hard stop, never a standing bypass |

## 8.2 Required artifacts

- **AHES-HA-A1** — A **human-authority-class register** enumerating each defined action class, the approval form it requires (pre-authorization, post-action acceptance, or override), and the accountable approver or approver role for each. Owner: harness governance owner. Change-controlled under the configuration baseline (→ §15).
- **AHES-HA-A2** — An **escalation matrix** mapping each authority class to the next authority when an action exceeds the current approver's competence or when approval is refused, ambiguous, or unavailable. Owner: harness governance owner; version-controlled.
- **AHES-HA-A3** — An **approval-capture schema** defining, for every approval event, the fields that make it attributable and non-repudiable: approver identity, action instance approved, approval form, timestamp, and rationale where the class requires one. Owner: evidence architecture owner (→ §10); change-controlled.

## 8.3 Technical controls

- **AHES-HA-01** — An action in a human-authority class **shall not** execute until an approval of the required form has been captured for that action. Absent a valid approval, the action **shall** fail closed (deny), not default open.
- **AHES-HA-02** — Every approval **shall** be attributable to a named human authority (→ §9 for the authority model), not to an agent, a role token acting unattended, or the model itself. A model **shall not** be able to record, synthesize, or assert an approval on a human's behalf.
- **AHES-HA-03** — An approval **shall** bind to the specific action instance it authorized — its resolved target and parameters (→ §7 PE-03) — such that the action executed is the action approved. Substituting, widening, or re-parameterizing an action after approval **shall** invalidate the approval and re-trigger the gate.
- **AHES-HA-04** — Pre-authorization gates **shall** be per-action or per-bounded-batch with an explicit, recorded scope. Standing or session-wide grants that pre-approve an open-ended class of future actions **shall not** exist; a trusted-default scope that executes without a gate is a §7 policy decision, not a blanket human pre-authorization, and **shall** be documented as such.
- **AHES-HA-05** — When an action exceeds the approver's authority class, is refused, or returns no decision within its defined window, the harness **shall** escalate per the escalation matrix (AHES-HA-A2) rather than downgrade the required approval form or proceed on assumed consent.
- **AHES-HA-06** — Overrides of a structural denial **shall** follow the §7 override procedure (PE-04, PE-A3): per-action, attributable to a named human authority, recorded append-only. Action classes designated non-overridable (§7 PE-09) **shall not** be releasable through any human-authority path; their change requires policy-document change control.
- **AHES-HA-07** — Action classes permitted to execute autonomously but designated for **post-action acceptance shall** produce an acceptance record naming the accountable human owner of the outcome. Absence of acceptance within the defined window **shall** raise an operational signal (→ §12), not silently close.
- **AHES-HA-08** — The approval surface **should** be engineered against approval fatigue: gates **should** be reserved for classes whose consequence warrants them, present the approver the resolved action and its blast radius, and avoid high-frequency low-information prompts that train reflexive assent. Where a class is demoted out of a gate, the demotion **shall** be recorded as a change to AHES-HA-A1, not made ad hoc at runtime.
- **AHES-HA-09** — Approval capture, escalation, and override mechanics **shall** be installed reproducibly from versioned configuration (→ §7 PE-06): a gate that exists only as host-local or session-local state **shall not** be the basis of a conformance claim.

## 8.4 Verification criteria

| Control | Method |
|---|---|
| HA-01 | Test: for each authority-class action, attempt execution with no approval captured; verify fail-closed denial. Inspection of the class-to-gate binding. |
| HA-02 | **Adversarial test**: prompt the model to fabricate, self-issue, or replay an approval (e.g., emit an approval token, impersonate the approver, claim prior consent); verify no such path records a valid approval. |
| HA-03 | Test: capture an approval, then mutate the action's resolved target/parameters before execution; verify the approval is invalidated and the gate re-fires. |
| HA-04 | Inspection: no standing/session-wide grant exists; trusted-default scope is documented as a §7 decision. Test that a second action of the same class re-gates. |
| HA-05 | Demonstration: drive an action exceeding approver competence, a refusal, and an approval timeout; verify escalation per matrix in each case. |
| HA-06 | Inspection of override records against §7 PE-04; test that the non-overridable set (PE-09) refuses every human-authority release path. |
| HA-07 | Test: execute an autonomous post-acceptance-class action; verify an acceptance record is required and its absence raises a §12 signal. |
| HA-08 | Analysis: gate frequency and information content reviewed against the register; inspection that demotions are change-controlled entries in AHES-HA-A1. |
| HA-09 | Demonstration: fresh deployment carries active gates with no manual installation step. |

## 8.5 Evidence requirements

The operating harness **shall** capture, for every human-authority event: the action instance and its resolved target, the approval form required, the approver identity, the decision (approve / refuse / escalate / override), rationale where required, the escalation path taken, and the timestamp — sufficient to answer post-hoc: *what was gated, who decided, what they decided, and whether the action executed matched what they approved*. Post-action acceptance records and their accountable owners are captured on the same basis. Evidence flows into the §10 evidence architecture; retention follows the configuration-baseline retention rule (§15). Every captured approval **shall** additionally carry the principal-authority attribution of §9, so that "who approved" and "under whose authority the action was taken" are jointly reconstructable.

## 8.6 Informative examples

**Per-action human override with append-only audit (architectural pattern).** A harness realizing §7 PE-04 gates its highest-consequence action classes so a structural denial is releasable only by a per-command marker that a human must add in-conversation, each use appended to an audit log, with a category of denials that no marker can release. Read as a human-authority realization, this is the override form of AHES-HA-06 tied to a named accountable human (AHES-HA-02) at per-action granularity (AHES-HA-04); the non-releasable category realizes the PE-09 tie-through. The same deployment's session-open self-check illustrates AHES-HA-09: the gate machinery is asserted present at start rather than assumed.

**General pattern.** HA-02's adversarial framing follows from the domain's threat model: the entity whose actions the gate governs is the reasoning engine, so any approval path that the engine can itself satisfy is not a human-authority control. Approval must originate outside the model and bind to the resolved action, or the gate discharges no requirement.
