# Model Construct — grok-console — §4 Agent Engineering

## Mapping

| AHES clause concern | Pattern observed | How it's satisfied |
|---|---|---|
| Identity | Each agent role is defined in its own dedicated contract artifact, named for its function | One artifact per role — identity is a first-class, individually addressable thing, not an ad hoc label |
| Role / goal binding | Each contract states, in structured form, what the role is for *and* an explicit negative case (what it is not for) | Dispatch criteria and scope statement live in the same place; a role is defined by both what it does and what it explicitly declines |
| Permission binding | Access level is declared as structured metadata on the contract, not buried in prose | Machine-checkable against the actual grant, rather than inferred from behavior |
| Delegation and termination | Contracts state an explicit stop condition on scope overrun ("if the task requires surfaces outside it, stop and report") | Not merely a hope that the model self-limits — the boundary condition is written into the role's own contract |
| State / recovery boundary | Completion is defined by an external check (e.g., passing tests) rather than self-report; a designated second role serves as the review/verification step after substantive work | Agent engineering here composes with §5 workflow (a review role as a pipeline stage) rather than trusting one role's own claim of done |
| Self-containment constraint | Role contracts explicitly inherit the harness-wide scope boundary (no reach beyond the declared workspace) rather than restating it independently | Ties per-role permission back to a single harness-wide authority boundary instead of letting each role define its own |

## What's different from other exemplars

The standard's other domains (§7 in particular) draw on thinx, where "agents" are ad hoc dispatches with a type and a prompt, governed centrally by dispatch rules rather than per-role contract artifacts. grok-console's realization is the more granular data point: each role is a standalone artifact with its own declared contract, which is closer to what §4's "identity, role, state, goals" language implies a conforming harness should be able to produce on inspection — a reviewer can point at one artifact and see a role's full contract, rather than reconstructing it from dispatch-time prose.

## Gaps

- No formal state machine or structurally enforced termination condition beyond the contract-level stop-and-report convention — termination is a contract clause, not a structurally enforced property.
- Delegation depth between roles (can one role invoke another, and is that depth bounded?) is implied by pairing convention but not specified as a control.
