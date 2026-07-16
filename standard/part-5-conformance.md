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

Complementary, non-overlapping: **OAgents** profiles *organizational assurance* (who attests, at what maturity, against NIST AI RMF); **AHES** specifies *how the harness artifact is engineered*. An OAgents attestation can cite an AHES conformance claim as its engineering evidence; AHES L2/L3 evidence feeds OAgents documented-evidence review directly. Crosswalk table to be drafted with the OAgents maintainer.

## 5.4 Per-level requirement selection

Part 2's clauses share a fixed shape — *purpose → required artifacts (§N.2) → technical controls (§N.3) → verification criteria (§N.4) → evidence requirements (§N.5)*. Rather than enumerate every domain's individual requirement IDs (which the domain clauses own and which evolve with them), AHES selects requirements per level **structurally, by clause section**. The rule composes with whatever the domain clauses specify.

- **AHES-CF-01** — At **L1 (Documented)**, for every applicable domain, the **Required Artifacts (§N.2)** of that domain **shall** exist and be inspectable, the Part 3 quality-characteristic allocation **shall** be recorded, and the Part 4 risk register **shall** be populated. L1 is a claim that the harness is *defined*; it does not require that controls are operating.
- **AHES-CF-02** — At **L2 (Enforced)**, in addition to L1, for every applicable domain the **Technical Controls (§N.3)** **shall** be implemented and operating, the **Verification Criteria (§N.4)** **shall** have been executed with recorded results, and the **Evidence Requirements (§N.5)** **shall** be captured by the operating harness. L2 is a claim that controls are *structural and operating*, substantiated by documented evidence review.
- **AHES-CF-03** — At **L3 (Verifiable)**, in addition to L2, the **Verification Criteria (§N.4)** **shall** have been *independently executed* — not merely self-attested — and the captured evidence (§N.5, → §10 Evidence engineering) **shall** be attributable and tamper-evident to a degree sufficient for a third party to reconstruct, for any sampled action, its authority, policy baseline (→ §7 Policy enforcement), and configuration baseline (→ §15 Configuration baseline).
- **AHES-CF-04** — Levels are cumulative and **shall not** be claimed partially across levels: a harness that meets L2 for some domains and only L1 for others **shall** claim L1 overall, and **may** additionally record the higher-level domains as a per-domain conformance profile (§5.6). A single-number level claim is the floor across all applicable domains.

Insertion processes from Part 1 bind through the same structural rule: a Part 1 insertion is treated as operating at L2 when it produces the artifacts and evidence Part 1 §1.2 names, and as verifiable at L3 when a third party can confirm from evidence that the process ran for a given change (Part 1 §1.4).

## 5.5 Assessment procedure

An assessment produces a disposition against a stated claim. The procedure is:

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

- Reference-implementation conformance studies as informative annexes (candidate subjects: thinx (Claude Code), ThinxS (Go), nemo-harness). [`model-construct/`](../model-construct/) has begun this against grok-console and mxh-p for §4/§7/§10/§16, at an architectural-pattern level of detail (private-repo sources, redacted to abstraction — see that directory's README); **thinx-codex**'s fail-closed CI conformance gates (checking authored strategy/requirements/architecture/verification artifacts against a structural integrity rule set) are a strong candidate for the Part 5 assessment-procedure exemplar specifically, flagged but not yet reviewed in depth
