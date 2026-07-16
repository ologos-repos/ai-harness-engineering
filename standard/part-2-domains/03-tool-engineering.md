# AHES Part 2 §3 — Tool Engineering

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 3.1 Purpose

Tool engineering governs the capability surface exposed to models: schemas, preconditions, postconditions, permission binding, and side-effect classification (read-only vs. mutating vs. externally visible). When this domain is unengineered, the failure class is **capability ambiguity**: the harness cannot state what a tool can affect, so policy enforcement and evidence capture have nothing sound to bind to. A policy enforcement point (→ §7) can only mediate an action whose side-effect class is declared; evidence (→ §10) can only record an invocation whose contract is known. Tool engineering is therefore upstream of both — it is the domain that makes a capability *legible* to the rest of the harness.

## 3.2 Required artifacts

- **AHES-TE-A1** — A **tool registry** enumerating every capability exposed to a model, each with a machine-readable input/output schema, a stated purpose, and a version. Owner: harness maintainer. Change control: versioned with the configuration baseline (→ §15); adding or modifying a tool is a baseline change.
- **AHES-TE-A2** — A **side-effect classification** assigning every registered tool to a class (read-only / mutating / externally-visible), with the rationale for the assignment. Owner: harness maintainer. Change control: versioned; reclassification is a baseline change and a policy-relevant event (→ §7).
- **AHES-TE-A3** — A **contract specification** per tool: preconditions that shall hold before invocation and postconditions the harness may rely on after. Owner: tool author. Change control: versioned with the tool.
- **AHES-TE-A4** — A **permission-binding map** relating each tool (or side-effect class) to the permission an invoking agent shall hold to call it. Owner: harness maintainer. Change control: versioned; consumed by policy enforcement (→ §7) and the authority model (→ §9).

## 3.3 Technical controls

- **AHES-TE-01** — Every capability a model can invoke **shall** be declared in the tool registry (TE-A1) with a machine-readable schema. Ambient or undeclared capability — an effect a model can reach that is not a registered tool — **shall not** exist in a conformance-claimed deployment.
- **AHES-TE-02** — Every registered tool **shall** carry a side-effect classification (TE-A2). The classification **shall** be authoritative for policy binding: policy enforcement (→ §7) and evidence capture (→ §10) consume the declared class, so an incorrect classification is a governance defect, not merely a documentation error.
- **AHES-TE-03** — Tool inputs **shall** be validated against the declared schema at the harness boundary before execution. Inputs that fail validation **shall** be rejected structurally, before the tool runs; schema validation **shall not** be delegated to model cooperation or to the tool's own internals alone.
- **AHES-TE-04** — Each tool invocation **shall** be bound to a permission requirement (TE-A4) and **shall** be denied when the invoking agent's grant does not cover it (→ §7, §9). Permission binding is checked structurally at invocation, independent of the model's belief about its own authorization.
- **AHES-TE-05** — Preconditions declared for a tool (TE-A3) **shall** be checked before execution where structurally feasible; where a precondition can only be enforced procedurally, the infeasibility of structural enforcement **shall** be documented in the contract.
- **AHES-TE-06** — Externally-visible side effects (external communication, third-party API calls, actions incurring cost or commitment, mutation of shared state) **shall** be a distinct classification requiring the strictest binding, and **shall** route through the enforcement points and human-authority classes their blast radius warrants (→ §7 hard/soft stops, → §8 human authority).
- **AHES-TE-07** — Side-effect classification **shall** default conservatively: a tool whose class is unassigned or ambiguous **shall** be treated as mutating and externally-visible (fail-safe) until classified, never as read-only by default.
- **AHES-TE-08** — Postconditions and results of an invocation **shall** be captured with the invocation record (→ §10), including the declared side-effect class, the permission decision, and the arguments as validated, so that what a tool actually did is reconstructable.
- **AHES-TE-09** — The tool registry (TE-A1) and the capability surface actually reachable by a model **shall** be kept consistent: every reachable capability is registered and classified, and every registry entry is reachable or retired. A reachable-but-unregistered tool is a conformance defect.

## 3.4 Verification criteria

| Control | Method |
|---|---|
| TE-01 | Analysis: enumerate the model-reachable capability surface; demonstrate each entry is in the registry with a schema. **Adversarial test**: attempt to reach an effect not backed by a registered tool. |
| TE-02 | Inspection of the classification against each tool's actual effect; analysis that policy and evidence layers consume the declared class. |
| TE-03 | **Adversarial test**: submit malformed, out-of-schema, and injection-bearing arguments; verify structural rejection before execution. |
| TE-04 | Test: invoke a tool with an agent grant that does not cover it; verify structural denial. Adversarial test: attempt to assert authorization through model reasoning and confirm it does not satisfy the check. |
| TE-05 | Inspection of contract preconditions; test that violated preconditions block execution where structural; inspection of documented procedural-only cases. |
| TE-06 | Inspection: externally-visible tools carry the strictest binding; test that they route through the required enforcement/authority path. |
| TE-07 | Test: introduce an unclassified tool; verify it is treated as mutating + externally-visible, not read-only. |
| TE-08 | Inspection of the invocation record schema; test that a completed invocation captures arguments, class, permission decision, and result. |
| TE-09 | Analysis: automated cross-check (self-test) of registry ↔ reachable-capability set, runnable at deployment start and in CI. |

## 3.5 Evidence requirements

The operating harness **shall** capture, per tool invocation: the tool identity and version, the validated arguments, the declared side-effect class, the permission decision (grant checked, allowed/denied), and the result or error. This is sufficient to answer, post-hoc: *which capability was invoked, with what inputs, under what authorization, in which side-effect class, and what did it change*. The record binds to the policy-decision evidence (→ §7) and the agent/principal attribution (→ §9) for the same action. Evidence flows into the §10 evidence architecture; the tool registry and classification active at time of action are retained under the configuration-baseline retention rule (→ §15).

## 3.6 Informative examples

**Side-effect class as the binding surface for enforcement (MxM reference architecture).** A harness that gates actions structurally before execution depends on tool engineering to tell it *which* actions to gate: the enforcement layer (→ §7) matches on the side-effect class and resolved target of a tool call, so the tool's declared classification is the fact the whole governance stack binds to. The architectural pattern TE-02/TE-06/TE-07 describe — read-only capabilities exempted from mediation, mutating and externally-visible ones routed through pre-execution enforcement, and an unclassified tool treated as the most dangerous class until proven otherwise — is what lets a policy enforcement point make a sound decision at all. Where a tool's side effect is the resolved target of an operation rather than its surface phrasing (a repository push, a file write), the classification and the enforcement layer must agree on that resolved target (→ §7), not on the argument as typed.

**General pattern.** TE-07's conservative default follows from the domain's failure class: capability ambiguity is only safe to resolve in the fail-safe direction. Treating an unclassified tool as read-only would let an unengineered capability slip past the enforcement points that key on its class, which is precisely the ambiguity the domain exists to remove.
