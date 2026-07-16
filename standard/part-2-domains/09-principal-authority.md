# AHES Part 2 §9 — Principal & Authority Model

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 9.1 Purpose

The principal and authority model governs whose authority the harness acts under: principal identity, principal altitude (operator-level tactical direction vs. enterprise-level standing intent, §0.3), delegation depth, and attribution of every action to the authority that licensed it. Distinct from agent identity (§4): an agent may be well-identified yet acting under no traceable principal authority. Unengineered, the failure class is **orphaned agency**: actions no accountable human or organizational authority can be said to have directed. This domain is the *representation* of authority as first-class, traceable harness state — it binds every action, including fully-autonomous actions that never reach a §8 approval gate, to the principal and altitude that licensed it, and is therefore broader than §8's approval *process*.

The distinction from §4 is load-bearing and is the reason this domain exists separately: **agent identity answers "which execution context acted"; principal authority answers "on whose standing authority, at what altitude, through what delegation chain, was it entitled to act at all."** A correctly-identified agent with no resolvable principal is precisely the orphaned-agency defect.

## 9.2 Required artifacts

- **AHES-PA-A1** — A **principal register** enumerating each principal the harness may act under, its identity, and its principal altitude(s). Where an altitude carries standing intent (enterprise-level), the register records the durable statement of that intent and its owner. Owner: harness governance owner; change-controlled under the configuration baseline (→ §15).
- **AHES-PA-A2** — A **delegation policy** defining which principals may delegate to which agents or sub-agents, the maximum delegation depth, and the constraints that narrow (never widen) authority at each hop. Owner: harness governance owner; version-controlled.
- **AHES-PA-A3** — An **authority-attribution schema** defining the fields that bind an action to its authorizing principal: principal identity, altitude invoked, delegation chain from principal to acting agent, and the licensing basis (standing intent, tactical direction, or a specific §8 approval). Owner: evidence architecture owner (→ §10); change-controlled.

## 9.3 Technical controls

- **AHES-PA-01** — Every side-effecting action **shall** be attributable, at execution time, to a principal drawn from the principal register (AHES-PA-A1) and to the altitude under which it was licensed. An action with no resolvable principal **shall** fail closed (deny), not execute unattributed.
- **AHES-PA-02** — Principal identity **shall** be represented as first-class harness state, not inferred from conversational content or context. The model **shall not** be the source of record for whose authority it is acting under; principal attribution is set by the harness, upstream of and independent of model reasoning.
- **AHES-PA-03** — The **principal altitude** of an action **shall** be represented explicitly and carried with the action. Where operator-level tactical direction and enterprise-level standing intent conflict, the harness **shall** resolve the conflict by a documented precedence rule rather than leaving it to model discretion, and **shall** record which altitude governed.
- **AHES-PA-04** — Delegation from a principal through agents to sub-agents **shall** be depth-bounded per AHES-PA-A2 and **shall** be recorded as a chain in which each hop is attributable to the hop above it. Authority at any hop **shall not** exceed the authority delegated to it; a hop may narrow authority, never widen it.
- **AHES-PA-05** — Agent identity (§4) **shall not** be sufficient, on its own, to establish principal authority. A well-identified agent acting without a resolvable principal and altitude **shall** be treated as orphaned agency and denied under AHES-PA-01. This is the structural expression of the §4/§9 distinction.
- **AHES-PA-06** — Fully-autonomous actions — those permitted to execute without a §8 human-approval gate — **shall** still carry a resolvable principal and altitude (typically a standing-intent authority recorded in AHES-PA-A1). "No approval gate" **shall not** mean "no principal": the trusted-default scope of §7/§8 is authority *pre-granted by a named principal*, not authority absent.
- **AHES-PA-07** — The harness **shall not** permit the model to assert, elevate, or fabricate its own principal authority or altitude. Any model-emitted claim of authority **shall** be treated as unprivileged content and reconciled against the principal register; a claim without register backing **shall not** raise the effective authority of an action.
- **AHES-PA-08** — Authority attribution **shall** survive substitution of the underlying model or harness runtime (→ §16): principal identity, altitude, and delegation chain are harness state, not model state, and **shall** rebind identically across a conforming substrate substitution.
- **AHES-PA-09** — The principal register, delegation policy, and attribution machinery **shall** be installed reproducibly from versioned configuration (→ §7 PE-06): authority attribution that exists only as host-local or session-local state **shall not** be the basis of a conformance claim.

## 9.4 Verification criteria

| Control | Method |
|---|---|
| PA-01 | Test: drive a side-effecting action with no resolvable principal; verify fail-closed denial. Inspection that attribution is present on executed actions. |
| PA-02 | **Adversarial test**: supply context asserting a principal identity and verify the harness does not adopt it; confirm principal state is set upstream of the model and unchanged by conversational injection. |
| PA-03 | Inspection: altitude is recorded per action. Demonstration: construct an operator-vs-enterprise-intent conflict; verify the documented precedence rule resolves it and the governing altitude is logged. |
| PA-04 | Analysis of delegation chains: verify depth bound holds and that authority narrows (never widens) at each hop. Test a hop attempting to exceed its delegated authority; verify denial. |
| PA-05 | **Adversarial test**: present a fully-identified agent (§4) with no principal binding; verify it is denied as orphaned agency rather than acting on identity alone. |
| PA-06 | Test: execute an autonomous (no-§8-gate) action; verify it still resolves to a registered principal and altitude, and that removing the standing-intent record denies it. |
| PA-07 | **Adversarial test**: prompt the model to claim elevated authority/altitude; verify the claim does not raise effective authority and is reconciled against the register. |
| PA-08 | Demonstration: substitute the model/runtime per §16; verify principal, altitude, and delegation chain rebind identically. |
| PA-09 | Demonstration: fresh deployment carries active attribution machinery with no manual installation step. |

## 9.5 Evidence requirements

The operating harness **shall** capture, for every attributable action: the principal identity, the altitude invoked, the full delegation chain from principal to acting agent, and the licensing basis (standing intent, tactical direction, or a specific §8 approval) — sufficient to answer post-hoc: *on whose authority, at what altitude, through what chain of delegation, was this action taken*. For fully-autonomous actions this record is the sole trace of authorization and **shall** be no less complete than for gated actions. Attribution records are joined to the §8 approval evidence where an approval existed, so that approval and authorization reconstruct together. Evidence flows into the §10 evidence architecture; retention follows the configuration-baseline retention rule (§15).

## 9.6 Informative examples

**Principal-altitude representation (architectural pattern).** A reference deployment built on an MxM (multi-mode meta-harness) reference architecture represents principal authority as an explicit altitude: an operator-altitude, tactical principal directing day-to-day work, distinguished from a higher, standing-intent altitude that binds durable posture. Actions are taken *under* a named principal at a named altitude rather than under the agent's own identity, which realizes AHES-PA-02 (principal as first-class harness state, not model-asserted) and AHES-PA-03 (altitude carried and, on conflict, resolved by a documented precedence rather than model discretion). The same deployment's autonomous, no-gate action scope is authority *pre-granted by its operator principal*, not authority absent — the AHES-PA-06 reading of a trusted-default scope.

**General pattern.** PA-05 and PA-07's adversarial framing follows from the domain's threat model: the reasoning engine can name a principal or claim an altitude in its output, so any attribution path the engine can itself satisfy is not an authority control. Principal and altitude must be harness state set upstream of the model and reconciled against a register, or the attribution is only the model's assertion about itself — the orphaned-agency failure wearing a plausible name.
