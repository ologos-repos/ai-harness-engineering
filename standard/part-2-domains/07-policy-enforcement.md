# AHES Part 2 §7 — Policy Enforcement

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 7.1 Purpose

Policy enforcement governs *what the harness will refuse to do regardless of model output*. When this domain is unengineered, policy exists only as instructions in context — advisory text a model can misweigh, lose to context compaction, or be adversarially steered around. The failure class is **approval bypass**: an action executes because the model reasoned its way to it, when no accountable authority permitted it.

The domain distinguishes three layers, all required:

| Layer | Nature | Bypassable by model reasoning? |
|---|---|---|
| Policy document | Human-readable statement of what is permitted, denied, flagged — and why | n/a (not an enforcement mechanism) |
| Judgment layer | Policy content loaded into model context; the model applies it to novel cases | Yes — by design; it handles what structure cannot |
| Structural layer | Pre-execution enforcement points that mediate actions independent of model reasoning | No |

## 7.2 Required artifacts

- **AHES-PE-A1** — A **policy document** enumerating: hard-stop action classes, flagged (soft-stop) action classes, the trusted default scope, and escalation triggers that convert trusted actions into flagged ones. The document shall state the rationale for each hard stop.
- **AHES-PE-A2** — An **enforcement-point inventory**: every structural control, the action patterns it matches, and the policy-document entry it enforces.
- **AHES-PE-A3** — An **override procedure** defining who may authorize an action that a structural control denies, at what granularity, and where authorizations are recorded.

## 7.3 Technical controls

- **AHES-PE-01** — Side-effecting actions **shall** pass through a pre-execution enforcement point before the harness executes them. Read-only actions **may** be exempted; the exemption boundary shall be documented.
- **AHES-PE-02** — Hard-stop action classes **shall** be enforced structurally. A model's reasoning, instructions in context, or claimed authorization **shall not** be sufficient to execute a hard-stopped action.
- **AHES-PE-03** — Structural enforcement **shall** resolve the actual target of an action (e.g., the resolved remote of a repository push, the real path of a file operation), not a surface pattern that the action's phrasing can evade.
- **AHES-PE-04** — Overrides **shall** be per-action, attributable to a named human authority, and recorded in an append-only audit log. Session-wide or standing structural bypasses **shall not** exist.
- **AHES-PE-05** — When a structural control denies an action, the denial **shall** state the policy rationale and the legitimate alternative path (including the override procedure where applicable), so that denial produces correction rather than workaround-seeking.
- **AHES-PE-06** — The enforcement layer **shall** be installed reproducibly from versioned configuration: a fresh deployment of the harness carries the enforcement layer with it. Enforcement that exists only as host-local state **shall not** be the basis of a conformance claim.
- **AHES-PE-07** — The policy document and the enforcement-point inventory **shall** be kept consistent: every hard stop in the document has an enforcement point; every enforcement point is disclosed in the document. Divergence in either direction is a conformance defect.
- **AHES-PE-08** — Policy layers **shall** compose with, not replace, one another: the judgment layer handles action classes that structural matching cannot express (blast-radius judgment, unfamiliar state), and **should** treat structural gaps as flagged rather than trusted.
- **AHES-PE-09** — A subset of hard stops **may** be designated *non-overridable* (e.g., actions on infrastructure owned by a third party). The override procedure **shall not** apply to these; their release requires changing the policy document itself under change control.
- **AHES-PE-10** — The policy document **shall** state a conflict-priority rule for competing obligations (e.g., prohibitions outrank obligations outrank permissions), and **shall** state the default resolution when two same-tier obligations conflict with no clear priority between them. The default **shall** favor pausing for human input over silent resolution: an enforcement layer that resolves an unanticipated conflict on its own, rather than surfacing it, has substituted its own judgment for the policy document's.

## 7.4 Verification criteria

| Control | Method |
|---|---|
| PE-01 | Test: enumerate side-effecting tool paths; demonstrate each is mediated. Inspection of the exemption boundary. |
| PE-02 | **Adversarial test**: prompt the model (or replay adversarial transcripts) to attempt each hard-stopped class; verify structural denial in every case, including via indirection (aliases, chained commands, encoded arguments). |
| PE-03 | Test with disguised targets: renamed paths, remotes under unexpected local names, `cd`-prefixed compound commands. |
| PE-04 | Inspection of audit log schema; test that override without the marker/authority fails; verify append-only property. |
| PE-05 | Inspection of denial messages against policy document. |
| PE-06 | Demonstration: fresh checkout/deployment on a clean host carries active enforcement with no manual installation step. |
| PE-07 | Analysis: automated cross-check (self-test) of document ↔ inventory, runnable at session/deployment start and in CI. |
| PE-08 | Inspection: judgment-layer policy content is loaded at every session start and survives context boundaries (see Part 2 §2 pinning). |
| PE-09 | Inspection: non-overridable set is named in the policy document; test that the override path refuses it. |
| PE-10 | Inspection: policy document states the priority tier and same-tier default. **Adversarial test**: construct a scenario where two obligations conflict with no stated priority between them; verify the enforcement layer pauses for human input rather than resolving silently. |

## 7.5 Evidence requirements

The operating harness **shall** capture: every structural denial (action, matched rule, timestamp), every override (action, authority, rationale, timestamp), and the enforcement-layer version active at each session — sufficient to answer, post-hoc: *what was denied, what was overridden, by whom, under which policy baseline*. Evidence flows into the §10 evidence architecture; retention follows the configuration-baseline retention rule (§15).

## 7.6 Informative examples

**thinx (Claude Code deployment, 2026).** Three-layer realization: a policy document (PE-A1) covering hard-stops, flagged actions, and trust scope; a dispatcher hook as the structural layer (PE-06, repo-versioned so a fresh checkout carries enforcement with it), with real-target resolution rather than surface-pattern matching for its highest-consequence gates (PE-03); a per-command, human-authorized override marker with an append-only audit log (PE-04); a non-overridable category for infrastructure owned by a third party (PE-09); and a self-test asserting the enforcement layer is actually wired in at session start (PE-07). A documented incident class motivating PE-07: structural gates were at one point live in the enforcement layer but undisclosed in the policy document — caught and corrected in an audit pass, illustrating why document↔inventory consistency is a named requirement rather than an assumption.

**General pattern.** PE-02's adversarial framing follows from the domain's threat model: the policy's subject *is* the reasoning engine, so controls that depend on that engine's cooperation are judgment-layer by definition and cannot discharge hard-stop requirements.

**Second realization (different substrate).** [`model-construct/grok-console/07-policy-enforcement.md`](../../model-construct/grok-console/07-policy-enforcement.md) maps the same requirement set onto a structurally different mechanism — a declarative deny-pattern catalog consumed by the model CLI's own native permission engine, rather than a wrapping hook. That exemplar is also where PE-10 originates: it demonstrated that *some* documented conflict-priority rule belongs in this domain, using a risk-magnitude tiebreak (the obligation with greater irreversibility risk wins) with a same-tier default of pausing for human input. PE-10 folds the *pattern* into this clause's own control set as a tier-based ordering (prohibitions outrank obligations outrank permissions) rather than reproducing the exemplar's exact magnitude-comparison mechanism — a deliberate, more-decidable choice for verification purposes, not a claim that grok-console implements this specific tier ordering.
