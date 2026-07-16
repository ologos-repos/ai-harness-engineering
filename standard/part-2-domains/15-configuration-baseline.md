# AHES Part 2 §15 — Configuration Baseline

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 15.1 Purpose

Configuration baseline governs the versioned definition of a harness deployment: models, instructions, prompts, tools, policies, memory schemas, and evaluations as one identifiable, restorable unit. Unengineered, the failure class is an **undefined system**: no two people can agree what version of the harness produced a given outcome, a deployment cannot be reproduced or rolled back as a whole, and a live deployment can drift silently from whatever it is claimed to be.

Configuration is the *versioned* counterpart to §10's *runtime* evidence: the baseline says what the harness was configured to be at a point in time; evidence says what it then did. The cross-link is directional and load-bearing — every runtime evidence record references the baseline active when the action occurred (→ §10, EE-06), which imposes back on this domain an obligation to keep referenced baselines resolvable and retained.

## 15.2 Required artifacts

- **AHES-CB-A1** — A **baseline manifest**: the enumerated, versioned set defining the deployment — models and their versions, instructions, prompts, tool definitions, policy/enforcement configuration, memory schemas, and the evaluation suite — carrying a single baseline identifier for the set as a whole. Owner: harness engineering. Change control: version-controlled; every element resolves to a specific version.
- **AHES-CB-A2** — A **baseline change register**: the record of baseline transitions — from-identifier, to-identifier, timestamp, authorizing authority, and the evaluation-gate result that admitted the transition. Owner: harness engineering. Change control: append-only; each entry is an evidenced transition (→ §10).
- **AHES-CB-A3** — A **restoration procedure**: how a named prior baseline is redeployed as a unit to reproduce the harness's configured behavior. Owner: operating organization. Change control: versioned and tested against real prior baselines, not only the current one.

## 15.3 Technical controls

- **AHES-CB-01** — A harness deployment **shall** be defined by an identifiable configuration baseline: a single named, versioned unit enumerating models, instructions, prompts, tools, policies, memory schemas, and evaluations. Configuration that exists only as an accretion of ad-hoc live changes, with no identifiable unit, **shall not** be the basis of a conformance claim.
- **AHES-CB-02** — Every element of the baseline **shall** resolve to a specific version. Floating references (a "latest" model alias, an unpinned tool or dependency, a mutable prompt) **shall not** appear in a released baseline, since they make the baseline identifier a false promise of reproducibility.
- **AHES-CB-03** — A baseline **shall** be restorable: a prior baseline can be redeployed as a unit and reproduce the harness's configured behavior within bounded, specified variance (model-substitution variance is bounded by §16). Restoration **shall not** require manual reassembly from memory or from runtime evidence.
- **AHES-CB-04** — A baseline change **shall** be an atomic, authorized transition under change control (→ §9 for the authorizing authority). A live deployment **shall not** be mutated element-by-element outside the baseline mechanism; where an emergency change is unavoidable, it **shall** be reconciled into a new numbered baseline before the next session, and the out-of-band interval recorded.
- **AHES-CB-05** — The running harness **shall** be verifiable against its claimed baseline: the active configuration can be checked to match the named baseline, and divergence is detectable. Silent drift between what a deployment *claims* to be and what it *is* is the domain's primary adversarial failure and **shall** be a detectable, not assumed-absent, condition.
- **AHES-CB-06** — Each baseline **shall** carry a unique, resolvable identifier that runtime evidence records reference (→ §10, EE-06). The identifier **shall** resolve to the full baseline content, so any recorded action's exact harness definition is recoverable.
- **AHES-CB-07** — Baseline release **shall** be a defined lifecycle process (→ Part 1), with a named entry condition (what triggers a candidate baseline) and a promotion gate that **shall** include passing the baseline's own evaluation suite (→ §11) before the candidate becomes the active baseline. Promotion without a recorded gate result **shall not** occur.
- **AHES-CB-08** — The policy and enforcement configuration **shall** be part of the baseline and installable from it, so a fresh deployment carries its policy layer with it (consistent with §7, PE-06). Enforcement or policy that exists only as host-local state outside the baseline **shall not** be claimed as configured.
- **AHES-CB-09** — A superseded baseline **shall** be retained for at least as long as any retained evidence record references it (→ §10 retention). A baseline that runtime evidence points to but which can no longer be resolved defeats EE-06 and **shall** be treated as a conformance defect.

## 15.4 Verification criteria

| Control | Method |
|---|---|
| CB-01 | Inspection: a single named baseline unit exists and enumerates all required element classes. |
| CB-02 | Analysis: scan the baseline for floating references. **Adversarial test**: a "latest"/unpinned element is a defect regardless of current resolved value. |
| CB-03 | Demonstration: redeploy a prior baseline as a unit on a clean target; verify configured behavior reproduces within specified variance, with no manual reassembly. |
| CB-04 | Inspection of the change register (A2) for atomicity and authorization; test that element-wise live mutation outside the mechanism is either blocked or flagged and reconciled. |
| CB-05 | **Adversarial test**: mutate one live element out-of-band, then run the baseline-verification check; verify the drift is detected rather than silently tolerated. |
| CB-06 | Test: sample runtime evidence records (→ §10) and resolve each referenced baseline identifier to full content. |
| CB-07 | Inspection: release process defines entry + promotion gate; test that promotion without a recorded evaluation-gate pass (→ §11) is refused. |
| CB-08 | Demonstration: fresh deployment from the baseline carries active policy/enforcement with no host-local manual step (cross-check §7 PE-06). |
| CB-09 | Analysis: for retained evidence, every referenced baseline still resolves; retention windows for baseline vs. evidence are consistent. |

## 15.5 Evidence requirements

Baseline transitions are themselves evidenced events, captured into the §10 architecture. The operating harness **shall** capture, for every transition: **when** the baseline changed, **from which** baseline **to which** (both identifiers), **authorized by whom** (the accountable authority, → §9), and **the evaluation-gate result** that admitted the promotion (→ §11). This is sufficient to answer post-hoc: *which baseline was active at time T, how did it get there, and who accepted it.* These records live in the §10 store (→ §10) and follow its retention rule, with the added §15 constraint that a baseline referenced by any retained evidence remains resolvable for the life of that evidence (CB-09).

## 15.6 Informative examples

*Non-normative.* The reference architecture this standard draws on — an MxM (multi-mode meta-harness) deployment — treats its configuration surfaces (identity/instruction files, tool and policy definitions, memory schemas, evaluation suites) as a version-controlled unit whose release is a bracketed lifecycle event rather than continuous live edit, which is the pattern CB-01, CB-04, and CB-07 generalize. A fully worked, deployment-grounded realization of this clause is not yet drafted; it is a candidate for the `model-construct/` set alongside the §10 realization cited above.
