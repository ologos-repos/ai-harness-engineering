# AHES Part 2 §5 — Workflow Engineering

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 5.1 Purpose

Workflow engineering governs multi-step orchestration: topology, concurrency, checkpoints, idempotency, resumption, and compensation for partially completed work. Unengineered, the failure class is **unrecoverable partial execution**: side effects applied through step N with no record of N and no compensating path. A workflow is not a transcript of what a model happened to do across turns; it is an engineered structure with a declared shape, durable progress markers, and a defined behavior when it is interrupted between any two steps. Because an agent's execution can be interrupted by context compaction, session loss, model substitution, or crash at an arbitrary point, the harness — not the model's in-context memory of "where I was" — shall be the authority on what has completed and what remains.

## 5.2 Required artifacts

- **AHES-WE-A1** — A **workflow topology definition** for each engineered workflow: its steps, their ordering and dependency edges, which steps have side effects, and which steps may execute concurrently. Owned by the harness maintainer; versioned under the configuration baseline (→ §15) so a workflow's shape at time of execution is reconstructible.
- **AHES-WE-A2** — A **checkpoint schema** defining what a durable progress marker records: workflow identity, the step boundary reached, the inputs consumed and outputs produced up to that boundary, and the configuration baseline in force. Owned by the harness maintainer; change-controlled with the evidence schema (→ §10).
- **AHES-WE-A3** — A **compensation catalog**: for each side-effecting step, whether its effect is reversible and, if so, the compensating action that undoes it; irreversible steps are named as such. Owned by the harness maintainer; changes reviewed against the tool registry (→ §3) so that every side-effecting tool a workflow invokes has a compensation disposition on record.

## 5.3 Technical controls

- **AHES-WE-01** — A workflow's topology **shall** be declared before execution, not inferred from the model's turn-by-turn choices. The set of reachable steps and their side-effect classification **shall** be knowable independent of a particular run.
- **AHES-WE-02** — Each side-effecting step **shall** be idempotent or guarded by an idempotency key, such that re-execution after an uncertain outcome does not double-apply the effect. Where a step's underlying action cannot be made idempotent, the infeasibility **shall** be documented and the step treated as irreversible under AHES-WE-A3.
- **AHES-WE-03** — The harness **shall** write a durable checkpoint at each side-effecting step boundary before proceeding, such that completed side effects are recoverable from the checkpoint record and not only from the model's context. A checkpoint is itself an evidence artifact and **shall** flow into the §10 evidence architecture.
- **AHES-WE-04** — On interruption, the harness **shall** resume from the last durable checkpoint rather than restart the workflow from its beginning, and **shall not** re-execute steps recorded as complete. Resumption **shall** reconstruct progress from the checkpoint record, not from model recall of prior turns (→ §6 for the analogous recall-is-not-authority rule on memory).
- **AHES-WE-05** — When a workflow terminates with side effects applied through step N and no clean completion, the harness **shall** invoke the compensating actions for the reversible completed steps in reverse dependency order, and **shall** surface the irreversible completed steps to a human authority (→ §8) rather than silently leaving them.
- **AHES-WE-06** — Concurrent steps **shall not** be permitted to interleave in a way that violates a declared shared-state invariant; steps that write shared state **shall** either be serialized or hold an explicit concurrency guard. The absence of a concurrency model for a workflow that fans out **shall** be treated as a defect, not a default.
- **AHES-WE-07** — Every workflow **shall** carry a termination condition — a bounded step budget, a completion predicate, or both — so that a workflow cannot loop or spawn without a defined stopping point (→ §4 for the agent-level termination requirement this specializes).
- **AHES-WE-08** — A step whose side effect exceeds a declared consequence threshold **shall** route through the applicable policy enforcement point (→ §7) and, where required, a human authority class (→ §8) *before* execution — checkpointing and compensation do not substitute for pre-execution control on high-consequence steps.
- **AHES-WE-09** — Resumption **should** verify that the configuration baseline in force at resume matches the baseline recorded in the checkpoint (→ §15); where it differs (e.g., a model or tool was substituted mid-workflow), the harness **should** flag the divergence rather than resume silently, since a partially completed workflow finished under a changed substrate is not the workflow that was begun.

## 5.4 Verification criteria

| Control | Method |
|---|---|
| WE-01 | Inspection of the topology definition (WE-A1) against the workflow's executable form; analysis that side-effect classification is present for every step. |
| WE-02 | Test: execute each side-effecting step twice with the same idempotency key; verify single application. Analysis of documented infeasibility for any non-idempotent step. |
| WE-03 | Test: interrupt after a side-effecting step; inspect the checkpoint record for the completed effect. Demonstration that the checkpoint reaches the §10 store. |
| WE-04 | **Adversarial test**: interrupt at each step boundary and resume; verify no completed step re-executes, including when the model's context is deliberately reset or replaced so that only the checkpoint carries progress. |
| WE-05 | Test: force termination mid-workflow; demonstrate compensations fire in reverse dependency order and irreversible steps are escalated. |
| WE-06 | Analysis of the concurrency model; test that concurrent writers to shared state serialize or are guarded; demonstration of invariant preservation under fan-out. |
| WE-07 | Inspection: every workflow declares a termination condition. Test that the step budget or predicate halts a runaway run. |
| WE-08 | Test: a high-consequence step is denied/held at its enforcement point before execution even when prior checkpoints exist. Inspection of the consequence-threshold definition. |
| WE-09 | Test: substitute the model/tool baseline between checkpoint and resume; verify the divergence is flagged. |

## 5.5 Evidence requirements

The operating harness **shall** capture, for each workflow run: the workflow identity and topology version, a checkpoint record at every side-effecting step boundary, the compensations invoked (with outcome) on any non-clean termination, and any irreversible completed steps escalated to a human. This is sufficient to answer, post-hoc: *which steps completed, which side effects were applied, what was compensated, what was left irreversibly done, and under which configuration baseline*. Checkpoints and compensation records are evidence artifacts and flow into the §10 evidence architecture; retention follows the configuration-baseline retention rule (→ §15).

## 5.6 Informative examples

**General pattern.** The session-boundary case is the canonical workflow-engineering test: an agent's own turn-by-turn recollection of "what I have already done" is exactly the thing that context compaction or session loss destroys, so a conforming harness makes the durable checkpoint — not the model's narrative of its own progress — the authority on completion state. WE-04's adversarial framing (reset the context, resume from the checkpoint alone) follows directly: if resumption depends on the model still remembering, the workflow was never recoverable.
