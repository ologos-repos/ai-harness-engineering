# AHES Part 2 — Harness Domain Clauses

**Status: Draft v0.1 — clause template fixed; one worked clause (§7 Policy enforcement); remaining clauses stubbed.**

Sixteen domains. Each is a normative clause with the fixed shape defined in [the clause template](clause-template.md): **purpose → required artifacts → technical controls → verification criteria → evidence requirements**. Requirement IDs: `AHES-<domain-abbrev>-<nn>`.

| § | Domain | Engineering concern | Status |
|---|---|---|---|
| 1 | [Model mediation](01-model-mediation.md) | Abstraction, routing, substitution, versioning, fallback | Stub |
| 2 | [Context engineering](02-context-engineering.md) | Prompt composition, retrieval, token budgeting, provenance, isolation | Stub |
| 3 | [Tool engineering](03-tool-engineering.md) | Schemas, preconditions, postconditions, permissions, side-effect classification | Stub |
| 4 | [Agent engineering](04-agent-engineering.md) | Identity, role, state, goals, delegation, termination, recovery | Stub |
| 5 | [Workflow engineering](05-workflow-engineering.md) | Orchestration, concurrency, checkpoints, idempotency, compensation | Stub |
| 6 | [Memory engineering](06-memory-engineering.md) | Scope, persistence, provenance, expiration, cross-session isolation | Stub |
| 7 | [**Policy enforcement**](07-policy-enforcement.md) | Pre-execution controls, runtime controls, post-execution review, hard stops | **Worked clause** |
| 8 | [Human authority](08-human-authority.md) | Approval classes, escalation, override, accountable acceptance | Stub |
| 9 | [Principal & authority model](09-principal-authority.md) | Principal identity, delegation depth, altitude, authority attribution | Stub |
| 10 | [Evidence engineering](10-evidence-engineering.md) | Traces, decisions, inputs, outputs, actions, approvals, configuration provenance | Stub |
| 11 | [Evaluation](11-evaluation.md) | Component, model, tool, agent, workflow, adversarial, mission-level | Stub |
| 12 | [Operational assurance](12-operational-assurance.md) | Monitoring, drift, anomaly detection, rollback, incident response | Stub |
| 13 | [Supply-chain control](13-supply-chain.md) | Models, prompts, tools, data, packages, connectors, external services | Stub |
| 14 | [Security architecture](14-security-architecture.md) | Identity, least privilege, credential boundaries, containment, data protection | Stub |
| 15 | [Configuration baseline](15-configuration-baseline.md) | Versioned models, instructions, tools, policies, memory schemas, evaluations | Stub |
| 16 | [Substrate portability](16-substrate-portability.md) | Identity/policy/memory survival across model and runtime substitution | Stub |

Domains 9 and 16 extend the original fourteen-domain analysis: agent identity is not principal authority (§9), and portability is a testable conformance target rather than a mediation footnote (§16).

Worked realizations against real deployments — beyond the informative examples inline in each clause — live in [`model-construct/`](../../model-construct/): a second, substrate-independent realization of §7, plus §4/§10/§16 realizations not yet drafted inline here.
