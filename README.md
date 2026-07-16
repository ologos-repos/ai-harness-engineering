# AI Harness Engineering Standard (AHES)

**An Ologos product.** Normative engineering standard for the *AI harness* — the engineered software and control environment surrounding one or more AI models.

**Status: Draft v0.1 — framing baseline.** Not yet released for conformance use.

---

## Definition

> **AI Harness Engineering** — the application of systems, software, security, data, assurance, and AI-specific engineering processes to the design, construction, integration, verification, operation, and retirement of the control environment through which AI models receive context, exercise capabilities, interact with resources, and produce accountable outcomes.

The harness comprises: model interfaces, prompts and context, retrieval, memory, tools, agents, workflow orchestration, identity, permissions, policy enforcement, evaluation, observability, evidence, human approvals, deployment, rollback, and lifecycle management.

## The gap this standard fills

No published standard specifies the engineering of the AI harness as a discipline. ISO/IEC 5338:2023 provides the nearest lifecycle spine; ISO/IEC/IEEE 15288 and 12207 provide the systems/software engineering base; ISO/IEC 25059 provides an AI quality model; ISO/IEC 23894 and the NIST AI RMF provide risk structure; ISO/IEC 42001 provides organizational governance. **None of them specifies harness architecture** — model gateways, context construction, prompt governance, tool registration, agent state, authorization mediation, memory boundaries, evidence capture, orchestration topology, model substitution, or runtime containment.

Emerging agentic-AI standards work (NIST AI Agent Standards Initiative; IEEE P3709, P7022, P3394) addresses fragments, but is unpublished or pre-standardization. AHES integrates the published substrate into a coherent discipline and specifies what the substrate leaves open.

## Structure

| Part | Title | Status |
|---|---|---|
| [Part 0](standard/part-0-scope-terms-conformance.md) | Scope, terms, and conformance language | Drafted |
| [Part 1](standard/part-1-lifecycle.md) | Lifecycle processes | Skeleton |
| [Part 2](standard/part-2-domains/README.md) | Harness domain clauses (16 domains) | Template + 1 worked clause |
| [Part 3](standard/part-3-quality-allocation.md) | Quality characteristic allocation | Skeleton |
| [Part 4](standard/part-4-risk-control-mapping.md) | Risk-to-control mapping | Skeleton |
| [Part 5](standard/part-5-conformance.md) | Conformance levels and assessment | Skeleton |
| [Normative references](references/normative-references.md) | Standards base with verification status | Drafted |
| [Model construct](model-construct/) | Worked realizations against real deployments (grok-console, mxh-p) | §4/§7/§10/§16 realized |

## Normative stack

AHES builds on, and does not replace:

1. **ISO/IEC/IEEE 15288** — system lifecycle engineering (the harness as system-of-interest)
2. **ISO/IEC/IEEE 12207** — software lifecycle engineering (the software-intensive control plane)
3. **ISO/IEC 5338:2023** — AI-specific lifecycle processes
4. **ISO/IEC 25059:2023** + **TS 25058** — AI quality model and evaluation
5. **ISO/IEC 23894:2023** + **NIST AI RMF 1.0** — risk management
6. **ISO/IEC 42001:2023** — organizational AI management (above the harness layer)
7. **ISO/IEC 27001 family** — security controls
8. Emerging NIST agent standards and IEEE P3709/P7022 — monitored sources, not compliance baselines

## Provenance and related work

- **Theoretical basis:** the [Harness Engineering research programme](https://github.com/agent-harness-engineering/harness-engineering) (Lakatos-governed; **MxM** — multi-mode meta-harness — reference architecture). The programme supplies theory and conformance evidence; AHES supplies the normative layer. Content reused under CC BY 4.0 with attribution.
- **Complementary standard:** [OAgents-standard](https://github.com/ologos-corp) — organizational assurance profile (NIST AI RMF). OAgents specifies *who attests and at what level*; AHES specifies *how the artifact is engineered*. Cross-referenced in Part 5; the two do not overlap.

## Attribution

Developed and maintained by **Ologos Corp**. Principal author: JD Longmire (ORCID: [0009-0009-1383-7698](https://orcid.org/0009-0009-1383-7698)).

## License

Normative text and documentation: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Conformance tooling and schemas, if and when shipped, will carry Apache-2.0.
