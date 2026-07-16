# AHES Part 5 — Conformance Levels and Assessment

**Status: Draft v0.1 — level structure fixed; per-level selection, assessment procedure, and claim format drafted.**

## 5.1 Purpose

Conformance scales by level, not by exemption: a small tool-less chat deployment and an autonomous multi-agent system claim against the same clause set at different depths. Levels are cumulative. Requirement IDs in this part use the `AHES-CF-<nn>` prefix.

## 5.2 Levels

| Level | Name | Claim | Evidence basis |
|---|---|---|---|
| **L1** | Documented | The harness is *defined*: all Part 2 required artifacts exist, the Part 3 allocation is recorded, the Part 4 register is populated | Self-attested inventory; artifacts inspectable |
| **L2** | Enforced | Controls are *structural and operating*: Part 2 technical controls implemented, verification criteria executed, runtime evidence captured | Documented evidence review — an assessor maps captured evidence to requirement IDs |
| **L3** | Verifiable | Conformance is *independently checkable*: evidence chain is attributable, tamper-evident, and sufficient for a third party to reconstruct any action's authority, policy baseline, and configuration | Third-party verification against the evidence architecture (§10, §15) |

The level structure deliberately parallels assurance-maturity profiles such as OAgents-standard (self-assessment → documented evidence review → third-party verification), so an organization can run one evidence architecture for both.

## 5.3 Relationship to OAgents-standard

Complementary, non-overlapping: **OAgents** profiles *organizational assurance* (who attests, at what maturity, against NIST AI RMF); **AHES** specifies *how the harness artifact is engineered*. An OAgents attestation can cite an AHES conformance claim as its engineering evidence; AHES L2/L3 evidence feeds OAgents documented-evidence review directly. §5.3.1 below draws the crosswalk out in full.

### 5.3.1 OAgents crosswalk

OAgents-standard (JD Longmire, CC BY 4.0; concept DOI [10.5281/zenodo.19425020](https://doi.org/10.5281/zenodo.19425020); canonical spec now at [`ologos-repos/aide-canon/constructs/oagents/`](https://github.com/ologos-repos/aide-canon/tree/main/constructs/oagents)) specifies **26 controls across 7 categories**, **3 conformance levels**, and alignment to **all four NIST AI RMF functions** (Govern / Map / Measure / Manage). Its conformance basis is observable evidence artifacts rather than assertions — the same posture as AHES §5.2. Both frameworks share an author, and both converged independently on the same three-tier assurance shape; the crosswalk below records that convergence and then draws the scope boundary honestly.

**Level crosswalk.** The two ladders line up almost verbatim, because both derive from the self-assessment → documented-evidence-review → third-party-verification maturity progression:

| AHES level (§5.2) | OAgents level | Shared evidence basis |
|---|---|---|
| **L1 Documented** | **1 · OAgent-Basic** (human oversight standard) | Self-attested inventory — artifacts exist and are inspectable; no independent review |
| **L2 Enforced** | **2 · OAgent-Standard** (exception-based oversight) | Documented evidence review — an assessor maps captured runtime evidence to requirement IDs / controls |
| **L3 Verifiable** | **3 · OAgent-Autonomous** (minimal oversight) | Third-party verification — evidence is attributable and sufficient for an independent party to reconstruct the claim |

The language already lines up closely enough that a single evidence architecture can substantiate both a claimed AHES level and the corresponding OAgents level without a second collection pass.

**Scope boundary — what each side actually assesses.** The level ladders match; the *subject* of assessment does not. OAgents' 7 categories are behavioral and operational controls on an **already-running agent** — feedback memory, named failure-mode catalogs, session lifecycle, incident tracking, hallucination detection. AHES' 16 domains (Part 2) are engineering requirements on the **harness artifact that hosts the agent** — context construction, tool schemas, policy-enforcement mechanics, evidence architecture, substrate portability. Some OAgents categories map closely onto specific AHES domains; others sit above the harness surface entirely and are the honest answer to "AHES does not specify this."

| OAgents category (control focus) | Closest AHES domain(s) | Relationship |
|---|---|---|
| **Enforcement Mechanisms** — executable action gates, severity escalation, protocol-compliance verification | **§7 Policy enforcement** (pre/runtime/post controls, hard stops); **§8 Human authority** (escalation) | Close map — AHES §7 engineers the mechanism OAgents attests is operating |
| **Operational Discipline** — session lifecycle, impact-level classification, incident tracking, structured logging | **§12 Operational assurance** (monitoring, incident response); **§4 Agent engineering** (termination/recovery); **§10 Evidence engineering** (structured logging) | Close map across three domains |
| **Quality Gates** — independent output review, process-enforcement gates, security audit, schema validation | **§11 Evaluation**; **§3 Tool engineering** (schema validation); **§14 Security architecture** (audit) | Partial map — AHES engineers the gate substrate; the *review discipline* is OAgents-side |
| **Knowledge Injection** — persistent memory, domain skill loading, lessons-learned pipeline | **§6 Memory engineering** (scope, persistence, provenance) | Partial map — AHES engineers the store; the *injection/lessons-learned practice* is OAgents-side |
| **Project Governance** — centralized work tracking, platform sovereignty, asset registry, vendor independence | **§16 Substrate portability** (vendor independence); **§15 Configuration baseline** (asset/version registry) | Partial map — organizational-sovereignty controls exceed the harness artifact |
| **Behavioral Shaping** — feedback memory, failure-mode catalogs, context-degradation detection | **§2 Context engineering** (context degradation); **§6 Memory engineering** (feedback memory) | Weak map — primarily *agent-behavior quality*, not harness engineering |
| **Anti-Hallucination** — state verification, memory-staleness detection, hallucination tracking | **§6 Memory engineering** (staleness) touches it; otherwise unmapped | **AHES does not specify this** — an AHES-conformant harness still needs an OAgents-style behavioral profile layered on top |

The boundary is not "OAgents assures, AHES engineers" applied uniformly. For **Enforcement Mechanisms** and **Operational Discipline** the two frameworks genuinely engineer and attest the *same* controls from two sides. For **Behavioral Shaping** and **Anti-Hallucination** they do not overlap: those categories are properties of agent behavior that a well-engineered harness enables but does not itself guarantee, so an AHES conformance claim is silent on them by design. Composition, not subsumption: neither framework makes the other redundant.

**Worked composition.** An organization running one deployment claims against both. It engineers its policy layer, evidence trace, and operational monitoring to AHES **L2 Enforced** for **§7 (Policy enforcement)**, **§10 (Evidence engineering)**, and **§12 (Operational assurance)** — producing captured, requirement-ID-mapped runtime evidence. That same evidence body is then the engineering-evidence input to an OAgents **Standard-level** attestation for the **Enforcement Mechanisms** and **Operational Discipline** categories: the OAgents assessor does not re-collect the harness evidence, they map the AHES L2 records to the OAgents controls and add the organizational-assurance layer (who attests, at what oversight maturity) that AHES does not carry. For **Anti-Hallucination**, there is no AHES claim to cite; the organization satisfies that category with an OAgents-native behavioral profile on top of the AHES-conformant harness. One evidence architecture, two claims, no double collection.

*(AHES' own reference architecture is MxM (multi-mode meta-harness); the crosswalk here is framework-to-framework and does not depend on that reference implementation.)*

## 5.4 Per-level requirement selection

Part 2's clauses share a fixed shape — *purpose → required artifacts (§N.2) → technical controls (§N.3) → verification criteria (§N.4) → evidence requirements (§N.5)*. Rather than enumerate every domain's individual requirement IDs (which the domain clauses own and which evolve with them), AHES selects requirements per level **structurally, by clause section**. The rule composes with whatever the domain clauses specify.

- **AHES-CF-01** — At **L1 (Documented)**, for every applicable domain, the **Required Artifacts (§N.2)** of that domain **shall** exist and be inspectable, the Part 3 quality-characteristic allocation **shall** be recorded, and the Part 4 risk register **shall** be populated. L1 is a claim that the harness is *defined*; it does not require that controls are operating.
- **AHES-CF-02** — At **L2 (Enforced)**, in addition to L1, for every applicable domain the **Technical Controls (§N.3)** **shall** be implemented and operating, the **Verification Criteria (§N.4)** **shall** have been executed with recorded results, and the **Evidence Requirements (§N.5)** **shall** be captured by the operating harness. L2 is a claim that controls are *structural and operating*, substantiated by documented evidence review.
- **AHES-CF-03** — At **L3 (Verifiable)**, in addition to L2, the **Verification Criteria (§N.4)** **shall** have been *independently executed* — not merely self-attested — and the captured evidence (§N.5, → §10 Evidence engineering) **shall** be attributable and tamper-evident to a degree sufficient for a third party to reconstruct, for any sampled action, its authority, policy baseline (→ §7 Policy enforcement), and configuration baseline (→ §15 Configuration baseline).
- **AHES-CF-04** — Levels are cumulative and **shall not** be claimed partially across levels: a harness that meets L2 for some domains and only L1 for others **shall** claim L1 overall, and **may** additionally record the higher-level domains as a per-domain conformance profile (§5.6). A single-number level claim is the floor across all applicable domains.

Insertion processes from Part 1 bind through the same structural rule: a Part 1 insertion is treated as operating at L2 when it produces the artifacts and evidence Part 1 §1.2 names, and as verifiable at L3 when a third party can confirm from evidence that the process ran for a given change (Part 1 §1.4).

## 5.5 Assessment procedure

An assessment produces a disposition against a stated claim. The procedure is:

*Informative — an automated-tooling realization of this procedure is worked in [`model-construct/thinx-codex/part5-conformance.md`](../model-construct/thinx-codex/part5-conformance.md): a fail-closed CI gate family over a traceable artifact graph, mapping the requirement-selection and disposition steps below onto executable structural checks.*

1. **Scoping statement.** The assessment **shall** open with a written scope: the harness deployment assessed, the configuration baseline (→ §15) it was assessed at, the conformance level claimed, and the assessing party. This is the claim under test (§5.7).
2. **Clause applicability matrix.** For each of the sixteen Part 2 domains and each Part 1 insertion, the assessment **shall** record applicable or inapplicable. A domain is inapplicable only when the deployment makes no claim the domain governs — for example, §16 Substrate portability does not apply to a single-model deployment that makes no portability claim, and §9 Principal & authority delegation depth is minimal where no agent delegates onward.
   - **AHES-CF-05** — An inapplicability claim **shall** be recorded explicitly, with the rationale for why the domain does not bind this deployment. Silent omission of a domain **shall** be treated as a gap, not as an inapplicability. Applicability is part of the assessed claim, so that a later baseline change that introduces (for example) a second model surface re-activates §16 rather than inheriting a stale exemption.
3. **Requirement selection.** Apply §5.4 to the applicable domains to derive the binding requirement set for the claimed level.
4. **Evidence review and sampling.** For L2 and L3, the assessor reviews captured evidence against the selected requirements. Full-population re-verification is not required.
   - **AHES-CF-06** — The assessment **shall** state its sampling approach and **shall** cover, at minimum: (a) every hard-stop and non-overridable action class (→ §7) — these are census, not sampled; (b) at least one evidence record per applicable domain's §N.5; and (c) a risk-weighted sample of the remaining evidence population, sized and selected by a stated rule, biased toward the highest-consequence action classes and toward controls whose verification (§N.4) is adversarial. A sample that surfaces any failing record **shall** trigger expanded sampling in the affected domain before disposition.
5. **Disposition.** The assessment **shall** close with a disposition per §5.6.

### 5.6 Disposition format

- **AHES-CF-07** — An assessment **shall** issue one of three dispositions, each against the stated scope and level: **Pass** — all selected requirements for the claimed level are met across all applicable domains; **Conditional** — selected requirements are met except for a named, bounded set of findings, each with a remediation owner and a re-assessment trigger, and the conditional disposition **shall not** be represented as an unqualified claim; **Fail** — one or more selected requirements are unmet with no accepted remediation path, and the claimed level **shall not** be asserted. The disposition **shall** enumerate findings by the requirement ID (or clause section, where the domain ID is not yet fixed) they trace to, and **shall** record a per-domain conformance profile where domains reach different levels (§5.4, AHES-CF-04).

## 5.7 Conformance claim format

A conformance claim is a data record, not free prose, so that a claim can be validated, indexed, and invalidated by baseline change.

- **AHES-CF-08** — A conformance claim **shall** be a record containing, at minimum: the **AHES version** claimed against; the **conformance level** (L1/L2/L3); the **configuration baseline** (→ §15) assessed, identified by its version anchor; the **assessment date**; the **assessor identity** and whether the assessment was self-attested (L1/L2) or independent (L3); the **scope statement** (§5.5 step 1); the **applicable-domains list** with each domain marked applicable or inapplicable-with-rationale (§5.5 step 2); and the **disposition** (§5.6).
- **AHES-CF-09** — A conformance claim **shall** be bound to the configuration baseline it was assessed at and **shall not** survive a baseline change unassessed. A change to any baseline element that a claimed requirement depends on (model, instructions, tools, policy, memory schema, evaluations — → §15) **shall** either be re-assessed for the affected domains or **shall** downgrade the claim to the last baseline at which it held. A claim that names no baseline is not a conformant claim.

## 5.8 Reference-implementation conformance studies

Reference-implementation conformance studies are informative, not normative — see [Annex A](annex-a-reference-implementations.md) for the current index of exemplars, what each demonstrates, and what remains unattempted. [`model-construct/`](../model-construct/) is the underlying source directory; Annex A is the document-level summary of it. ThinxS (Go) and nemo-harness remain candidate subjects not yet attempted.
