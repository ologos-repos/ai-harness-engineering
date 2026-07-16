# AHES Part 2 — Harness Domain Clauses

**Status: Draft v0.1 — clause template fixed; all sixteen clauses drafted in full (§7 Policy enforcement remains the most-worked, with two independent realizations).**

Sixteen domains. Each is a normative clause with the fixed shape defined in [the clause template](clause-template.md): **purpose → required artifacts → technical controls → verification criteria → evidence requirements**. Requirement IDs: `AHES-<domain-abbrev>-<nn>`.

| § | Domain | Engineering concern | Prefix | Status |
|---|---|---|---|---|
| 1 | [Model mediation](01-model-mediation.md) | Abstraction, routing, substitution, versioning, fallback | `MM` | Drafted |
| 2 | [Context engineering](02-context-engineering.md) | Prompt composition, retrieval, token budgeting, provenance, isolation | `CE` | Drafted |
| 3 | [Tool engineering](03-tool-engineering.md) | Schemas, preconditions, postconditions, permissions, side-effect classification | `TE` | Drafted |
| 4 | [Agent engineering](04-agent-engineering.md) | Identity, role, state, goals, delegation, termination, recovery | `AE` | Drafted |
| 5 | [Workflow engineering](05-workflow-engineering.md) | Orchestration, concurrency, checkpoints, idempotency, compensation | `WE` | Drafted |
| 6 | [Memory engineering](06-memory-engineering.md) | Scope, persistence, provenance, expiration, cross-session isolation | `ME` | Drafted |
| 7 | [**Policy enforcement**](07-policy-enforcement.md) | Pre-execution controls, runtime controls, post-execution review, hard stops | `PE` | **Worked clause** — two independent realizations |
| 8 | [Human authority](08-human-authority.md) | Approval classes, escalation, override, accountable acceptance | `HA` | Drafted |
| 9 | [Principal & authority model](09-principal-authority.md) | Principal identity, delegation depth, altitude, authority attribution | `PA` | Drafted |
| 10 | [Evidence engineering](10-evidence-engineering.md) | Traces, decisions, inputs, outputs, actions, approvals, configuration provenance | `EE` | Drafted |
| 11 | [Evaluation](11-evaluation.md) | Component, model, tool, agent, workflow, adversarial, mission-level | `EV` | Drafted |
| 12 | [Operational assurance](12-operational-assurance.md) | Monitoring, drift, anomaly detection, rollback, incident response | `OA` | Drafted |
| 13 | [Supply-chain control](13-supply-chain.md) | Models, prompts, tools, data, packages, connectors, external services | `SC` | Drafted |
| 14 | [Security architecture](14-security-architecture.md) | Identity, least privilege, credential boundaries, containment, data protection | `SA` | Drafted |
| 15 | [Configuration baseline](15-configuration-baseline.md) | Versioned models, instructions, tools, policies, memory schemas, evaluations | `CB` | Drafted |
| 16 | [Substrate portability](16-substrate-portability.md) | Identity/policy/memory survival across model and runtime substitution | `SP` | Drafted |

Domains 9 and 16 extend the original fourteen-domain analysis: agent identity is not principal authority (§9), and portability is a testable conformance target rather than a mediation footnote (§16).

"Drafted" means all five clause-template sections are written with numbered requirements and a verification-criteria table; it does not yet mean externally reviewed or conformance-tested against a real deployment. §7 stays furthest along because it carries two independent architectural realizations (thinx's wrap-and-intercept pattern and grok-console's configure-the-native-gate pattern); most other domains carry zero or one informative example, or none where no grounded example was available.

Worked realizations — beyond the informative examples inline in each clause — live in [`model-construct/`](../../model-construct/): four real-deployment exemplars (a second, substrate-independent realization of §7; dedicated §4/§10/§16 realization files with more depth than those clauses' own inline examples) plus the [AHES Reference Harness](../../model-construct/ahes-reference-harness/), a synthetic composite design covering all sixteen domains in one coherent architecture.
