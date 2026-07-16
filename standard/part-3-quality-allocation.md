# AHES Part 3 — Quality Characteristic Allocation

**Status: Draft v0.1 — allocation matrix framed (§3.2); allocation rule (§3.3) and separability test (§3.4) drafted as normative content.**

## 3.1 Purpose

ISO/IEC 25059 defines quality characteristics for AI systems but does not say *which element owns each one*. Without allocation, model failure and harness failure are conflated: an incident review cannot say whether the model hallucinated, the harness failed to contain it, the integration mis-specified the boundary, or the organization operated outside the envelope. This part allocates each characteristic across four loci:

- **M** — the model (as supplied; often not engineerable by the harness team)
- **H** — the harness (engineerable; the subject of this standard)
- **S** — the integrated system (emergent from M×H composition; owned by integration)
- **O** — the operating organization (procedures, staffing, envelope discipline)

An allocation is not exclusive — most characteristics have components at several loci. The normative content is the *harness-column requirement* for each characteristic: what H must contribute regardless of M.

## 3.2 Allocation matrix (framing draft)

| 25059 characteristic | M | H | S | O | Harness contribution (sketch) |
|---|:-:|:-:|:-:|:-:|---|
| Functional suitability | ● | ● | ● | | Context construction and tool surface determine whether model capability reaches the mission |
| Performance efficiency | ● | ● | ● | | Token budgeting, caching, routing (→ §1, §2) |
| Compatibility | | ● | ● | | Interface contracts, model mediation (→ §1) |
| Usability | | ● | ● | ● | Approval surfaces, denial messages, explanation surfaces (→ §7, §8) |
| Reliability | ● | ● | ● | ● | Fallback, retry, checkpoint/resume, degradation behavior (→ §1, §5, §12) |
| Security | ● | ● | ● | ● | Least privilege, credential boundaries, containment (→ §14) — harness-dominant |
| Maintainability | | ● | ● | | Configuration baseline, change control (→ §15, Part 1) |
| Portability | | ● | ● | | Substrate portability is a named harness domain (→ §16) — harness-dominant |
| Robustness | ● | ● | ● | | Input handling, injection containment, adversarial evaluation (→ §2, §11) |
| Transparency | ● | ● | ● | ● | Evidence and provenance make S transparent even where M is not (→ §10) |
| Explainability | ● | ● | ● | | Explanation surfaces; uncertainty propagation (→ §12; TR 24028 / TS 6254 informative) |
| Controllability | ● | ● | ● | ● | Policy enforcement, human authority, termination (→ §4, §7, §8) — harness-dominant |

The matrix above is the standard's *general* sketch: the loci that typically carry each characteristic across harness deployments. It is not itself the conformance claim. A specific deployment may allocate differently — a deployment that pins a single model version and forgoes fallback carries less H-reliability than the sketch implies; a deployment that adds an organizational review board carries more O-controllability. §3.3 requires each deployment to record the allocation it *actually* claims, so that the general sketch and the deployment-specific claim never silently diverge.

## 3.3 The allocation rule

The allocation is only load-bearing if it is recorded per deployment and traceable into the controls that discharge it. The following requirements make the allocation a conformance obligation rather than a design note.

- **AHES-QA-01** — A conforming deployment **shall** record, for each of the twelve ISO/IEC 25059 quality characteristics, the M/H/S/O allocation it *actually* claims. This per-deployment allocation **may** differ from the §3.2 sketch; where it does, the deviation **shall** be stated so the sketch and the claim do not silently diverge. A characteristic allocated to no locus is a specification defect, not a permitted state.

- **AHES-QA-02** — For every characteristic carrying an **H** allocation, the deployment **shall** trace that H component to the specific Part 2 domain controls that discharge it. The trace **shall** name concrete controls, not the domain in the abstract: harness reliability, for example, traces to the fallback, checkpoint/resume, and degradation controls of Model mediation (§1), Workflow engineering (§5), and Operational assurance (§12), not to a bare assertion that "the harness is reliable." A claimed H component with no discharging control is an unmet requirement.

- **AHES-QA-03** — For every characteristic carrying an **M** allocation, the deployment **shall** record the *residual accepted at the model layer* — the portion of that characteristic the harness explicitly does **not** contain — together with the named authority who accepted it and the rationale for acceptance. A residual of "none" (the claim that the harness fully contains a model-carried characteristic) **shall** itself be recorded as an affirmative, authority-attributed claim, because it is the strongest claim available and the one most likely to be overstated. Model-layer residual acceptance is the same act of residual acceptance governed by Part 4; its acceptance-authority requirements (→ Part 4 §4.3, acceptance authority; → §8 Human authority, §9 Principal & authority model) apply here without duplication.

- **AHES-QA-04** — The set of recorded M-layer residuals (AHES-QA-03) **shall** form part of the deployment's conformance-claim scope statement (→ Part 5). A conformance claim that asserts a quality characteristic without disclosing its M-layer residual is out of scope of any conformance level: the residual record *is* the boundary of what the harness is claiming to contain, and a claim with an undisclosed boundary cannot be evaluated.

## 3.4 Incident separability test

The allocation is adequate when a postmortem can classify any failure as M / H / S / O within one review pass, from captured evidence alone. This subsection makes that adequacy testable and then makes the test itself a conformance obligation.

### 3.4.1 Worked micro-example

Consider a generic, non-attributed incident: *a harness produces an incorrect output — an action was taken against the wrong target.* The separability test walks the captured evidence against the allocation matrix to assign a locus:

1. **Reconstruct the model invocation from evidence (→ §10).** Recover the exact context supplied to the model, the model identity and version, the model's output, and the action the harness derived from that output.

2. **Ask the M question — was the model's output itself wrong given its input?** If the context correctly described the intended target and the model nonetheless emitted the wrong one, the failure has an **M** component: a model reasoning failure. This is the residual the harness accepted under AHES-QA-03 for Functional suitability — provided the harness contained the *consequence* (see step 4).

3. **Ask the H question — did the harness construct the input correctly, and did it resolve the action's real target?** If the supplied context misdescribed the target (a context-construction failure, → §2), or if the harness matched a surface pattern rather than resolving the action's actual target before executing (→ §7 real-target resolution), the failure has an **H** component regardless of what the model did. A correct model output built on a mis-constructed context is still a harness failure.

4. **Ask the S question — was the failure emergent from the M×H boundary?** If context construction and target resolution were each individually correct but the *interface contract* between them was mis-specified — the model was asked for one granularity of answer and the harness consumed it as another — the failure is **S**: an integration-boundary failure owned by neither M nor H in isolation.

5. **Ask the O question — was the harness operated outside its declared envelope?** If the deployment ran against a configuration baseline the organization had not authorized, or a human approver waved through an action the policy flagged, the failure has an **O** component: an operational/organizational failure, not a technical one.

The loci are not mutually exclusive — a real incident often carries components at several — but the test resolves *which loci carry weight and which do not* from evidence, so the postmortem apportions correctly. A model that emitted a wrong target (M) whose consequence the harness failed to contain (H) is a two-locus finding, and the correct remediation touches both columns; classifying it as M-only would wrongly exempt the harness from a containment gap it actually owns.

### 3.4.2 Requirements

- **AHES-QA-05** — A conforming deployment **shall** maintain a postmortem template that assigns an M / H / S / O classification to any failure using **only** captured evidence (→ §10 Evidence engineering), without recourse to the reasoning transcript of the review itself, undocumented operator recollection, or re-execution. If the classification cannot be reached from captured evidence, the evidence architecture — not merely the incident — is the finding: the gap is reported against §10.

- **AHES-QA-06** — Inability to classify a failure to at least one locus within a single review pass **shall** be recorded as a Part 3 conformance defect: the allocation for the affected characteristic was underspecified (AHES-QA-01) or its evidence was insufficient (AHES-QA-05). The defect **shall** drive a corrective update to the allocation record or the evidence architecture, closing the loop rather than being absorbed as review friction.
