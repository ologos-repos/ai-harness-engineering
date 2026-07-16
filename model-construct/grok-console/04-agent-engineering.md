# Model Construct — grok-console — §4 Agent Engineering

## Mapping

| AHES clause concern | Concrete artifact | How it's satisfied |
|---|---|---|
| Identity | `AGENT.md` YAML frontmatter `name:` per role (`coder`, `evaluator`, `generator`, `planner`, `reviewer`, `verifier`) | Six named roles, one file each, under `construct/means/agents/<role>/AGENT.md` |
| Role / goal binding | Frontmatter `description:` — e.g. coder: *"Implement a scoped repository change — feature, fix, refactor, or doc edit — with clear boundaries. Use when the work is defined enough for one focused pass; not for open-ended exploration"* | The description doubles as dispatch criteria and scope statement — a role is defined by what it's *for*, with an explicit negative case ("not for...") |
| Permission binding | Frontmatter `access:` field (e.g. coder: `workspace-write`) | Access level is declared per-role as structured metadata, not buried in prose — machine-checkable against the actual grant |
| Delegation and termination | Contract section, e.g. coder: *"Stay inside the named scope; if the task requires surfaces outside it, stop and report"* | Explicit stop condition on scope overrun, not merely a hope that the model self-limits |
| State / recovery boundary | *"Run tests that cover the change before claiming done; report failures verbatim"* + pairing convention (*"Pair with verifier after substantive edits"*) | Completion is defined by an external check (tests), and a designated second role (verifier) is the recovery/QA path — agent engineering here composes with §5 workflow (verifier as a pipeline stage) rather than trusting one role's self-report |
| Self-containment constraint | *"Self-containment (P10): no new sibling-repo imports or paths"* | Ties the agent contract directly to the harness's own morals.md permission scope (P10) — role contracts inherit, rather than restate independently, the harness-wide authority boundary |

## What's different from other exemplars

The standard's other domains (§7 in particular) draw on thinx, where "agents" are ad hoc `Agent` tool dispatches with type + prompt, governed centrally by `means/agents.md` dispatch rules rather than per-role contract files. grok-console's realization is the more granular data point: each role is a standalone artifact with its own frontmatter contract, which is closer to what §4's "identity, role, state, goals" language implies a conforming harness should be able to produce on inspection — you can point at one file and say "this is the coder role's full contract," rather than reconstructing it from dispatch-time prose.

## Gaps

- No formal state machine or explicit termination-condition field beyond the "stop and report" prose convention — termination is a contract clause, not a structurally enforced property (a role could in principle continue past its stated scope; nothing structural stops it, only the safety-catalog layer in §7 catches specific *actions*, not *scope drift* as such).
- Delegation depth between roles (can `planner` invoke `coder` invoke `verifier`, and is that depth bounded?) is implied by the pairing convention but not specified as a control.
