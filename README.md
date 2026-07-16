# AI Harness Engineering Standard (AHES)

**An Ologos product.** Normative engineering standard for the *AI harness* — the engineered software and control environment surrounding one or more AI models.

**Status: Draft v0.1 — full normative draft.** All parts and all sixteen Part 2 domains are drafted with numbered requirements; nothing here has had independent/external review. Not yet released for conformance use.

---

## Definition

> **AI Harness Engineering** — the application of systems, software, security, data, assurance, and AI-specific engineering processes to the design, construction, integration, verification, operation, and retirement of the control environment through which AI models receive context, exercise capabilities, interact with resources, and produce accountable outcomes.

The harness comprises: model interfaces, prompts and context, retrieval, memory, tools, agents, workflow orchestration, identity, permissions, policy enforcement, evaluation, observability, evidence, human approvals, deployment, rollback, and lifecycle management.

## The gap this standard fills

No standard identified in the survey behind this document specifies the engineering of the AI harness as a discipline. ISO/IEC 5338:2023 provides the nearest lifecycle spine; ISO/IEC/IEEE 15288 and 12207 provide the systems/software engineering base; ISO/IEC 25059 provides an AI quality model; ISO/IEC 23894 and the NIST AI RMF provide risk structure; ISO/IEC 42001 provides organizational governance. **None of them specifies harness architecture** — model gateways, context construction, prompt governance, tool registration, agent state, authorization mediation, memory boundaries, evidence capture, orchestration topology, model substitution, or runtime containment. This is a claim about the standards this survey found, not a claim to have exhaustively searched every published standard.

Emerging agentic-AI standards work (NIST AI Agent Standards Initiative; IEEE P3709, P7022, P3394) addresses fragments, but is unpublished or pre-standardization. AHES integrates the published substrate into a coherent discipline and specifies what the substrate leaves open.

**This is distinct from "no one has written about harness engineering."** A substantial practitioner and industry literature already uses that exact term — OpenAI, Anthropic, Martin Fowler's blog, LangChain, and others have published essays on it, predating AHES by months; see [`references/industry-practice.md`](references/industry-practice.md) for the full, attributed account. That literature is essayistic: patterns and principles, no mechanism for an independent party to check conformance. Its domain boundaries converge closely with AHES's own sixteen — evidence the taxonomy is well-founded, not a competing claim to priority. AHES's actual contribution is the normative apparatus laid over that shared territory: numbered `shall` requirements, verification criteria, and conformance levels, credited to the practitioners whose real-world convergence AHES formalizes.

## Structure

| Part | Title | Status |
|---|---|---|
| [Part 0](standard/part-0-scope-terms-conformance.md) | Scope, terms, and conformance language | Drafted |
| [Part 1](standard/part-1-lifecycle.md) | Lifecycle processes | Drafted — 7 process insertions, 13 requirements |
| [Part 2](standard/part-2-domains/README.md) | Harness domain clauses (16 domains) | Drafted — all 16; §7 Policy enforcement is the most-worked (two independent realizations) |
| [Part 3](standard/part-3-quality-allocation.md) | Quality characteristic allocation | Drafted — allocation rule + incident-separability test |
| [Part 4](standard/part-4-risk-control-mapping.md) | Risk-to-control mapping | Drafted — all 14 risks as full entries |
| [Part 5](standard/part-5-conformance.md) | Conformance levels and assessment | Drafted — assessment procedure, claim format, OAgents crosswalk |
| [Annex A](standard/annex-a-reference-implementations.md) | Reference-implementation conformance studies (informative) | Drafted — 5 entries indexed (4 real exemplars + the synthetic composite) |
| [Normative references](references/normative-references.md) | Standards base with verification status | Drafted |
| [Industry practice](references/industry-practice.md) | Practitioner "harness engineering" literature — attributed prior art, convergence with AHES's domains | Drafted — 9 sources |
| [Model construct](model-construct/) | Worked realizations: 4 real-deployment exemplars + 1 synthetic composite | thinx, grok-console, mxh-p, thinx-codex, and the all-16-domain AHES Reference Harness |

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
- **Complementary standard:** [OAgents](https://github.com/ologos-repos/aide-canon/tree/main/constructs/oagents) (26 controls / 7 categories / 3 conformance levels; NIST AI RMF implementation profile; concept DOI [10.5281/zenodo.19425020](https://doi.org/10.5281/zenodo.19425020)) — organizational assurance profile for *running agents*. OAgents specifies *who attests and at what level*; AHES specifies *how the harness artifact hosting the agent is engineered*. Full crosswalk in [Part 5 §5.3.1](standard/part-5-conformance.md); the two are complementary, not overlapping — see that section for exactly where they do and don't map.

## Attribution

Developed and maintained by **Ologos Corp**. Co-authors: JD Longmire (ORCID: [0009-0009-1383-7698](https://orcid.org/0009-0009-1383-7698)), Micah Longmire (ORCID: [0009-0006-7608-9322](https://orcid.org/0009-0006-7608-9322)).

## License

Normative text and documentation: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Conformance tooling and schemas, if and when shipped, will carry Apache-2.0.
