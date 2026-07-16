# AHES Part 2 §4 — Agent Engineering

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 4.1 Purpose

Agent engineering governs harness-managed execution contexts that pursue goals across multiple model invocations: identity, role, state, goal binding, delegation to sub-agents, termination conditions, and recovery. Unengineered, the failure class is **unbounded agency**: contexts that outlive their mandate, delegate without attenuation, or cannot be attributed or terminated. A related and easily-missed failure is **identity-as-authority confusion** — treating an agent's declared role as if it conferred the directing authority of a principal (→ §9). An agent holds an identity, a role, and a bounded permission set; it acts *under* a principal's authority, it does not *hold* it. Everything in this domain follows from keeping those two things separate and making each one inspectable.

## 4.2 Required artifacts

- **AHES-AE-A1** — An **agent contract set**: one addressable contract artifact per agent role, stating in structured form the role's purpose, its goal binding, its scope, and an explicit negative case (what the role is *not* for and when it must decline). A reviewer shall be able to point at a single artifact and read a role's full contract, rather than reconstructing it from dispatch-time prose. Owner: the harness maintainer for the role; change control: versioned with the harness configuration baseline (→ §15).
- **AHES-AE-A2** — A **delegation model**: the permitted delegation graph among roles (which role may spawn which), the maximum delegation depth, and the attenuation rule that governs how a sub-agent's permissions relate to its parent's. Owner: harness architect; change control: baseline-versioned.
- **AHES-AE-A3** — A **termination and recovery specification**: for each role, its termination condition(s), the completion check that defines "done," and the disposition of state when an agent is interrupted, times out, or overruns its scope. Owner: harness maintainer; change control: baseline-versioned.

## 4.3 Technical controls

- **AHES-AE-01** — Every agent **shall** have an identity contract (AE-A1) that binds, at minimum, a role, a goal, a scope, and a permission set. An execution context that pursues goals across multiple model invocations without a declared contract **shall not** be treated as a conforming agent.
- **AHES-AE-02** — Agent identity **shall** be distinct from principal authority (→ §9). An agent's contract **shall** record the principal and altitude under whose authority it acts; a role label, an instruction in context, or a model's self-description **shall not** be sufficient to establish directing authority.
- **AHES-AE-03** — An agent's permission binding **shall** be expressed as structured, machine-checkable metadata on its contract, verifiable against the actual grant the harness enforces — not inferred from observed behavior. Divergence between declared permission and enforced grant is a conformance defect.
- **AHES-AE-04** — Every agent **shall** declare a termination condition, and the harness **shall** enforce it structurally where feasible (e.g., invocation-count or wall-clock bound). Where structural enforcement is infeasible, the contract **shall** carry an explicit stop-and-report condition on scope overrun ("if the task requires surfaces outside this scope, stop and report"), and the infeasibility of structural enforcement **shall** be documented (AE-A3).
- **AHES-AE-05** — Delegation **shall** be depth-bounded and attenuating: a sub-agent's permission set **shall not** exceed its parent's, and delegation **shall not** exceed the maximum depth declared in the delegation model (AE-A2). A role **shall not** acquire, by delegating, authority it does not itself hold.
- **AHES-AE-06** — Completion of substantive work **should** be defined by an external check (e.g., passing tests, a designated review role as a pipeline stage per → §5) rather than the working agent's own self-report. A harness **should not** treat an agent's unverified claim of "done" as completion for consequential work.
- **AHES-AE-07** — An agent's scope **shall** inherit the harness-wide authority boundary rather than restate or redefine it independently. Per-role scope narrows the harness boundary; it **shall not** widen it. A single harness-wide self-containment rule remains the ceiling for every role.
- **AHES-AE-08** — Every agent action **shall** be attributable, in evidence, to both the agent identity that took it and the principal under whose authority it was taken (→ §9, §10). An action that cannot be attributed to a declared agent identity is a conformance defect.
- **AHES-AE-09** — On interruption, timeout, or scope overrun, an agent's state **shall** reach a defined disposition (checkpointed for recovery, compensated, or cleanly abandoned) per its recovery specification (AE-A3, → §5). A context that is neither terminated nor recoverable — an orphaned agent — **shall not** persist silently.

## 4.4 Verification criteria

| Control | Method |
|---|---|
| AE-01 | Inspection: enumerate active agent roles; verify each resolves to a contract artifact binding role, goal, scope, and permissions. |
| AE-02 | Inspection of contract fields against §9's principal model; **adversarial test**: attempt to have an agent assert directing authority from its role label alone, and verify the harness attributes authority to the principal, not the agent. |
| AE-03 | Analysis: automated cross-check of declared permission metadata against the enforced grant (→ §7 enforcement points). |
| AE-04 | Test: drive an agent past its termination condition; verify structural stop where enforced, or stop-and-report where contract-level. **Adversarial test**: prompt the agent to continue past its declared scope and verify it halts rather than proceeds. |
| AE-05 | **Adversarial test**: attempt to delegate a permission the parent lacks, and to exceed maximum delegation depth; verify both are refused. Analysis of the delegation graph for attenuation. |
| AE-06 | Demonstration: consequential completion is gated on an external check or review-role stage, not self-report. |
| AE-07 | **Adversarial test**: attempt from within a role to act outside the harness-wide boundary; verify the harness ceiling holds regardless of role scope. |
| AE-08 | Inspection of evidence records: every agent action carries agent identity and principal attribution. |
| AE-09 | Test: interrupt/time-out an agent mid-task; verify state reaches a defined disposition and no orphaned context persists. |

## 4.5 Evidence requirements

The operating harness **shall** capture, for each agent lifecycle: agent identity and contract version at spawn; the principal and altitude under whose authority it acted; each delegation event (parent, child, permissions granted, depth); the termination event and its cause (completion check passed, condition reached, interruption, scope-overrun stop); and the disposition of state at termination. These records **shall** be sufficient to answer, post-hoc: *which agent did what, under whose authority, what it delegated, why it stopped, and what became of its state.* Evidence flows into the §10 evidence architecture; retention follows the configuration-baseline retention rule (§15).

## 4.6 Informative examples

**Governed console for a non-Anthropic model CLI (2026).** A realization in which each agent role is defined in its own dedicated contract artifact, named for its function — identity is a first-class, individually addressable thing rather than an ad hoc label (AE-01). Each contract states, in structured form, what the role is for *and* an explicit negative case (AE-A1), declares its access level as structured metadata rather than prose (AE-03), and carries an explicit stop-and-report condition on scope overrun (AE-04). Completion for substantive work is defined by an external check (e.g., passing tests) with a designated second role serving as the review stage, composing agent engineering with §5 workflow rather than trusting a single role's self-reported "done" (AE-06). Role contracts inherit the harness-wide scope boundary rather than each redefining it (AE-07). Two honest gaps in that realization map directly onto this clause's control set: termination is a contract clause rather than a structurally enforced property (the AE-04 structural/procedural split), and delegation depth between roles is implied by pairing convention but not specified as a bounded control (AE-05) — illustrating why depth-bounding and attenuation are named requirements rather than assumed properties.

**Independent convergence on the distinct-verification-role requirement (AE-09).** This is not an isolated design choice — Anthropic's own engineering team documents the identical separation ("generator-evaluator architecture," modeled on GANs, because self-evaluation is biased toward praising one's own output — Rajasekaran, Anthropic Labs, 2026-03-24) and a separate practitioner survey names the same pattern independently (Osmani, 2026-04-19); see `references/industry-practice.md` for the full account and its cross-reference from §11.

**General pattern.** AE-02's separation of agent identity from principal authority is the load-bearing distinction of this domain: a role is a contract the harness enforces, not a source of authority. Controls that let a declared role stand in for a principal's grant collapse §4 into §9 and reopen the unbounded-agency failure class the domain exists to close.
