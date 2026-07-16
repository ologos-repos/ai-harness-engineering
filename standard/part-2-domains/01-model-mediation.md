# AHES Part 2 §1 — Model Mediation

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 1.1 Purpose

Model mediation governs how the harness abstracts model access: routing among models, substitution, version pinning, capability gating, and fallback on failure or degradation. When this domain is unengineered, the failure class is **silent capability drift**: a model change alters system behavior with no baseline to compare against, no qualification step to gate the change, and no rollback path to recover the prior behavior. Mediation is the layer that makes the model a *specified, substitutable component* rather than an ambient dependency — the precondition for substrate portability (→ §16) and for attributing an incident to the model versus the harness (→ Part 3).

## 1.2 Required artifacts

- **AHES-MM-A1** — A **model registry** enumerating every model the harness may invoke, each with a pinned version identifier, provider, and the capability grant it is qualified for. Owner: harness maintainer. Change control: versioned with the configuration baseline (→ §15); adding or repinning a model is a baseline change.
- **AHES-MM-A2** — A **routing specification** stating how a given invocation is mapped to a model (by surface, task class, or explicit selection), and who may set the selection. Owner: harness maintainer. Change control: versioned.
- **AHES-MM-A3** — A **fallback policy** naming, for each primary model, the qualified alternative(s), the trigger conditions (failure, timeout, degradation), and the behavioral bound on the substitution. Owner: harness maintainer. Change control: versioned.
- **AHES-MM-A4** — A **qualification record** for each model+version: the evaluation evidence (→ §11) under which it was admitted to the registry and the baseline it was promoted into.

## 1.3 Technical controls

- **AHES-MM-01** — Every model invocation **shall** resolve through the mediation layer. Direct model-SDK calls that bypass mediation **shall not** exist in a conformance-claimed deployment; the mediation layer is the sole point at which a model is selected, versioned, and recorded.
- **AHES-MM-02** — Models **shall** be pinned to an explicit version identifier. A floating or provider-default "latest" alias **shall not** be the bound model in a conformance-claimed deployment; if a provider offers only a moving alias, the resolved concrete version **shall** be captured at invocation time (→ §1.5).
- **AHES-MM-03** — Model selection **shall** be deterministic given its inputs and **shall** be recorded as an attributable decision: which model, which version, under which routing rule or explicit direction. Non-deterministic or unrecorded selection **shall not** be used for actions with side effects (→ §3, §7).
- **AHES-MM-04** — A model substitution (new model, or new version of an existing one) **shall** be qualified against the prior baseline before promotion. Qualification **shall** exercise the harness's evaluation suite (→ §11); promotion without a recorded qualification result is a conformance defect.
- **AHES-MM-05** — Capability grants **shall** be bound to a specific model+version, not to the mediation layer in general. A model **shall** be granted only the capability surface (→ §3) its qualification covers; a substitution **shall not** silently inherit a grant that its own qualification did not establish.
- **AHES-MM-06** — Fallback on failure or degradation **shall** be to a model qualified in the fallback policy (MM-A3), and the fallback event **shall** be recorded, not silent. Fallback to an unqualified model **shall not** occur; where no qualified alternative exists, the harness **shall** fail closed rather than route to an unqualified substitute.
- **AHES-MM-07** — A fallback substitution **shall not** escalate privilege or cross a trust boundary: the fallback model inherits no capability grant broader than the primary it replaces (→ §14). Degraded-mode behavior under fallback **shall** be specified in the fallback policy, not emergent.
- **AHES-MM-08** — The mediation layer **shall** expose a rollback path to a prior pinned model+version under configuration-baseline control (→ §15), such that a regressing substitution can be reverted to a known-good baseline without code change.
- **AHES-MM-09** — The registry (MM-A1) and the deployed set of invocable models **shall** be kept consistent: every model the harness can reach is in the registry, and every registry entry is qualified. A reachable-but-unregistered model, or a registered-but-unqualified one, is a conformance defect.

## 1.4 Verification criteria

| Control | Method |
|---|---|
| MM-01 | Analysis: static scan of the codebase for model-SDK call sites; demonstrate each resolves through the mediation layer. Inspection of any documented exemption. |
| MM-02 | Inspection of the registry for concrete version pins; test that an invocation records the resolved concrete version even where the provider exposes a moving alias. |
| MM-03 | Test: replay a fixed routing input and confirm identical model selection; inspection of the recorded selection decision against the routing specification. |
| MM-04 | Inspection: for each promoted model+version, a qualification record (MM-A4) exists and predates promotion. Analysis of the promotion procedure for the gate. |
| MM-05 | Test with a substituted model: confirm its capability grant is its own qualified surface, not the predecessor's, unless the qualification re-establishes it. |
| MM-06 | Test (fault injection): induce primary-model failure/timeout/degradation; confirm fallback routes only to a policy-qualified model and records the event; confirm fail-closed when no qualified alternative exists. |
| MM-07 | **Adversarial test**: attempt to reach a broader capability through the fallback path than the primary holds; verify the fallback grant is bounded and no trust boundary is crossed. |
| MM-08 | Demonstration: revert to a prior pinned model+version through the baseline mechanism with no code change; confirm behavior returns to the known-good baseline. |
| MM-09 | Analysis: automated cross-check (self-test) of registry ↔ reachable-model set, runnable at deployment start and in CI. |

## 1.5 Evidence requirements

The operating harness **shall** capture, for every invocation: the model identity, the resolved concrete version (not merely the requested alias), the routing rule or explicit direction that selected it, and — where one occurred — the fallback event with its trigger and the substituted model+version. This is sufficient to answer, post-hoc: *which model, at which version, produced this output, under which selection decision, and did a fallback silently change the substrate mid-session*. Evidence flows into the §10 evidence architecture; the active model registry and baseline version at time of action are retained under the configuration-baseline retention rule (→ §15).

## 1.6 Informative examples

**Model-agnostic mediation (MxM reference architecture).** A multi-mode meta-harness that runs one durable agent identity across interchangeable model substrates realizes model mediation as the seam that keeps identity, policy, and memory constant while the model varies. The architectural pattern: model selection is set deliberately per surface (a latency-sensitive channel bound to a lighter model, depth work to a heavier one) rather than auto-escalated, the selection is recorded, and the running model is captured per session so that behavior is always attributable to a concrete model+version. The same seam is what makes substrate portability (→ §16) testable rather than aspirational: the mediation layer is the single place a substitution is qualified, pinned, and — on regression — rolled back.

**General pattern.** MM-06's fail-closed requirement follows from the domain's failure class: a fallback that silently routes to an unqualified model reintroduces exactly the capability drift the domain exists to prevent, now masked as resilience. Resilience that crosses a qualification or trust boundary is a defect, not a feature.
