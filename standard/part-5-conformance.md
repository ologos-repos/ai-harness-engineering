# AHES Part 5 — Conformance Levels and Assessment

**Status: Skeleton — level structure fixed; per-level requirement selections to be drafted.**

## 5.1 Purpose

Conformance scales by level, not by exemption: a small tool-less chat deployment and an autonomous multi-agent system claim against the same clause set at different depths. Levels are cumulative.

## 5.2 Levels

| Level | Name | Claim | Evidence basis |
|---|---|---|---|
| **L1** | Documented | The harness is *defined*: all Part 2 required artifacts exist, the Part 3 allocation is recorded, the Part 4 register is populated | Self-attested inventory; artifacts inspectable |
| **L2** | Enforced | Controls are *structural and operating*: Part 2 technical controls implemented, verification criteria executed, runtime evidence captured | Documented evidence review — an assessor maps captured evidence to requirement IDs |
| **L3** | Verifiable | Conformance is *independently checkable*: evidence chain is attributable, tamper-evident, and sufficient for a third party to reconstruct any action's authority, policy baseline, and configuration | Third-party verification against the evidence architecture (§10, §15) |

The level structure deliberately parallels assurance-maturity profiles such as OAgents-standard (self-assessment → documented evidence review → third-party verification), so an organization can run one evidence architecture for both.

## 5.3 Relationship to OAgents-standard

Complementary, non-overlapping: **OAgents** profiles *organizational assurance* (who attests, at what maturity, against NIST AI RMF); **AHES** specifies *how the harness artifact is engineered*. An OAgents attestation can cite an AHES conformance claim as its engineering evidence; AHES L2/L3 evidence feeds OAgents documented-evidence review directly. Crosswalk table to be drafted with the OAgents maintainer.

## 5.4 To be drafted

- Per-level requirement selection tables (which Part 2 `shall`s bind at L1 vs L2 vs L3)
- Assessment procedure: scoping statement, clause applicability matrix, sampling rules for evidence review
- Claim format: a conformance claim names the AHES version, level, configuration baseline (§15) assessed, and the date — claims do not survive baseline changes unassessed
- Reference-implementation conformance studies as informative annexes (candidate subjects: thinx (Claude Code), ThinxS (Go), nemo-harness). [`model-construct/`](../model-construct/) has begun this against grok-console and mxh-p for §4/§7/§10/§16; **thinx-codex-03**'s fail-closed CI conformance gates (`conformance_gate.py` et al., requirement/architecture/verification trace IDs) are a strong candidate for the Part 5 assessment-procedure exemplar specifically, flagged but not yet reviewed in depth
