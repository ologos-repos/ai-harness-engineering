# AHES Part 0 — Scope, Terms, and Conformance Language

**Status: Draft v0.1**

## 0.1 Scope

This standard specifies engineering requirements for the **AI harness**: the engineered software and control environment through which one or more AI models receive context, exercise capabilities, interact with resources, and produce accountable outcomes.

**In scope:**

- The harness as a system-of-interest per ISO/IEC/IEEE 15288, containing models, software, data, humans, infrastructure, and procedures as system elements
- The sixteen harness domains specified in Part 2
- Allocation of quality characteristics among model, harness, integrated system, and operating organization (Part 3)
- Translation of AI risk into enforceable harness controls (Part 4)
- Conformance levels and their evidence requirements (Part 5)

**Out of scope:**

- Engineering of the models themselves (training, fine-tuning, alignment) — see ISO/IEC 5338 and 23053
- Organizational AI governance and management systems — see ISO/IEC 42001, 38507, 42005
- Domain-specific application requirements (medical, automotive, defense) — sector standards apply above this one
- Model evaluation benchmarks as such — Part 2 §11 specifies *that and how* evaluation is integrated, not which benchmarks

**Applicability.** This standard applies to any deployment where model outputs can cause actions — tool invocation, data mutation, external communication, or decisions a human or downstream system relies on. A chat interface with no tool access has a small harness; an autonomous multi-agent system has a large one. The standard scales by conformance level (Part 5), not by exemption.

## 0.2 Conformance language

The verbal forms of ISO/IEC Directives Part 2 apply:

| Form | Meaning |
|---|---|
| **shall** | requirement — conformance is not claimed without it |
| **should** | recommendation — deviation requires documented rationale |
| **may** | permission |
| **can** | capability or possibility, non-normative |

Requirements are individually numbered (`AHES-<domain>-<nn>`) for traceability into requirements-management tooling.

## 0.3 Terms and definitions

Terms defined in ISO/IEC 22989:2022 apply. The following terms are specific to this standard:

**AI harness (harness)** — engineered software and control environment surrounding one or more AI models, comprising model interfaces, context construction, retrieval, memory, tools, agents, workflow orchestration, identity, permissions, policy enforcement, evaluation, observability, evidence capture, human approval surfaces, deployment, rollback, and lifecycle management.

**principal** — the human or organizational authority under whose direction a harness-governed agent acts. Distinct from *agent identity* (§0.3 agent) and from *user* (who may interact without holding directing authority).

**principal altitude** — the structural level at which a principal's directing intent binds an agent (e.g., operator-level tactical direction vs. enterprise-level standing intent). A harness shall be able to represent whose authority an action was taken under.

**agent** — a harness-governed execution context that pursues goals across multiple model invocations, holding identity, state, permissions, and a termination condition.

**model mediation** — the harness layer that abstracts model access: routing, substitution, versioning, fallback, and capability gating.

**context construction** — assembly of the input provided to a model invocation: instructions, retrieved content, memory, tool schemas, and conversation state, with provenance for each element.

**policy enforcement point** — a structural control that mediates a harness action *before execution*, independent of model reasoning. Distinct from *policy document* (the human-readable statement of what is permitted and why).

**hard stop** — an action class that a policy enforcement point denies structurally; not bypassable by model reasoning alone.

**human authority class** — a defined category of action requiring a defined form of human approval, escalation, or override, with an accountable approver.

**evidence** — durable, attributable records of harness activity sufficient for post-hoc reconstruction: inputs, context provenance, model identity and version, actions, approvals, policy decisions, and configuration baseline at time of action.

**substrate portability** — the property that harness identity, policy, and memory survive substitution of the underlying model or harness runtime with bounded, specified behavioral change.

**configuration baseline** — the versioned set of models, instructions, prompts, tools, policies, memory schemas, and evaluations that defines a harness deployment at a point in time.

## 0.4 Structure of this standard

- **Part 1** adapts the ISO/IEC/IEEE 15288 / 12207 / ISO/IEC 5338 lifecycle to harness-specific processes.
- **Part 2** specifies sixteen harness domains as normative clauses. Every clause has a fixed shape: *purpose → required artifacts → technical controls → verification criteria → evidence requirements*.
- **Part 3** allocates ISO/IEC 25059 quality characteristics among model, harness, integrated system, and operating organization, so that model failure and harness failure are separable in specification and in incident review.
- **Part 4** maps AI risks (ISO/IEC 23894, NIST AI RMF) to the Part 2 controls that mitigate them.
- **Part 5** defines three conformance levels and the evidence each requires.

## 0.5 Normative references

See [references/normative-references.md](../references/normative-references.md), which carries per-reference verification status. References marked UNVERIFIED shall not be cited as normative in a released version of this standard.
