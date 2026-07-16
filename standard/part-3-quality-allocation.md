# AHES Part 3 — Quality Characteristic Allocation

**Status: Skeleton — allocation matrix framed; per-cell requirements to be drafted.**

## 3.1 Purpose

ISO/IEC 25059 defines quality characteristics for AI systems but does not say *which element owns each one*. Without allocation, model failure and harness failure are conflated: an incident review cannot say whether the model hallucinated, the harness failed to contain it, the integration mis-specified the boundary, or the organization operated outside the envelope. This part allocates each characteristic across four loci:

- **M** — the model (as supplied; often not engineerable by the harness team)
- **H** — the harness (engineerable; the subject of this standard)
- **S** — the integrated system (emergent from M×H composition; owned by integration)
- **O** — the operating organization (procedures, staffing, envelope discipline)

An allocation is not exclusive — most characteristics have components at several loci. The normative content (to be drafted) is the *harness-column requirement* for each characteristic: what H must contribute regardless of M.

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

## 3.3 The allocation rule (to be drafted as requirements)

For each characteristic, a conforming deployment **shall** record: the allocation among M/H/S/O, the harness requirements discharging the H component, and the residual accepted at M (what the harness explicitly does *not* contain, and who accepted that residual). The residual acceptance links into Part 4 (risk) and Part 5 (conformance evidence).

## 3.4 Incident separability test (to be drafted)

The allocation is adequate when a postmortem template can classify any failure as M / H / S / O within one review pass, from captured evidence alone (→ §10). Inability to classify is itself a Part 3 conformance defect — the allocation was underspecified.
