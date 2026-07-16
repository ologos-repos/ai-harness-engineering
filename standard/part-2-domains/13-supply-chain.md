# AHES Part 2 §13 — Supply-Chain Control

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 13.1 Purpose

Supply-chain control governs external dependencies: models, prompts, tools, data sources, packages, connectors, and external services — their provenance, integrity, update policy, and blast radius on compromise. Unengineered, the failure class is **transitive trust**: a dependency change becomes a harness behavior change with no gate in between. The domain governs the boundary an artifact crosses on the way *in* — whether a component can be trusted before it is trusted. The moment an admitted-but-compromised component executes, its transitive trust becomes a live privilege-boundary problem; that handoff is the subject of §14, and the two domains share the containment mechanism (→ §14, → §7).

A dependency class is any external input that can change harness behavior at runtime. It is broader than software packages: a model is a dependency (→ §1), an externally-authored prompt or tool schema is a dependency, a retrieval corpus or connector endpoint is a dependency. Controls that pin and verify packages but leave models, prompts, and connectors ungoverned leave the largest-blast-radius dependencies outside the gate.

## 13.2 Required artifacts

- **AHES-SC-A1** — A **dependency inventory** enumerating every external dependency reachable at runtime, across all dependency classes (models, prompts/instruction packs, tool schemas, data sources, packages, connectors, external services). Each entry records: class, source, publisher/provenance, pinned version or content identity, integrity reference (hash/signature), the harness component that consumes it, and its granted privilege scope. Owner: the harness maintainer. Change control: the inventory is versioned with the configuration baseline (→ §15); an addition or version change is a baseline change, not an ambient edit.
- **AHES-SC-A2** — A **trust-and-admission policy** stating, per dependency class, the criteria a candidate must meet to be admitted (provenance requirements, integrity requirements, license/export constraints where applicable), who may authorize an admission, and the default disposition of an unadmitted dependency (deny). Owner: the harness maintainer under the principal's authority (→ §9). Change control: reviewed admissions, recorded per AHES-SC-04.
- **AHES-SC-A3** — An **update policy** defining, per dependency class, how a version or content change is admitted: re-verification requirements, pinning discipline, whether auto-update is permitted, and the review cadence for outstanding updates. Owner: the harness maintainer. Change control: versioned with A2.

## 13.3 Technical controls

- **AHES-SC-01** — Only dependencies present in the inventory (AHES-SC-A1) **shall** be reachable at runtime. An undeclared dependency resolving at runtime is a conformance defect; the resolution path **should** fail closed rather than silently admit the unknown component.
- **AHES-SC-02** — Each dependency **shall** be pinned to a specific version or content identity, and its integrity **shall** be verified against the inventory's recorded reference (hash or signature) *before* it is loaded, executed, or incorporated into context. A pin without integrity verification records intent, not trust.
- **AHES-SC-03** — Provenance **shall** be recorded for every dependency: origin, publisher, and acquisition channel. A dependency whose provenance cannot be established **shall** be treated as untrusted and admitted, if at all, only through the explicit admission path (AHES-SC-04), never by default.
- **AHES-SC-04** — Admission of a new dependency, and admission of a version/content change to an existing one, **shall** pass through a defined admission gate before first use. The gate **shall** be structural where the dependency class permits (e.g., lockfile-pinned packages, allowlisted endpoints); where structural admission is infeasible (e.g., a human judgment on a model provider's trustworthiness), the gate is procedural and the infeasibility **shall** be documented in AHES-SC-A2.
- **AHES-SC-05** — Models **shall** be governed as a dependency class: model identity, version, and provider provenance are subject to AHES-SC-01 through AHES-SC-04, and a model substitution is an admission event, not a silent routing change (→ §1). A harness that pins packages but floats model versions has left its highest-consequence dependency ungoverned.
- **AHES-SC-06** — Externally-authored instruction content — prompts, system-prompt fragments, tool schemas, agent definitions acquired from outside the harness — **shall** be treated as untrusted input until admitted, on the same footing as retrieved or tool-returned content (→ §2, → §7). Admission verifies provenance and integrity; it does not confer the standing authority of harness-native instructions.
- **AHES-SC-07** — Each dependency's runtime privilege **shall** be bounded to the minimum its function requires, so that compromise of a single dependency does not inherit ambient harness authority. Blast radius on compromise **shall** be a recorded property of each inventory entry, and containment of that radius is enforced through the security-architecture boundary (→ §14) using the same pre-execution mechanism as policy hard-stops (→ §7).
- **AHES-SC-08** — Side-effecting dependencies **shall not** silently auto-update. A version change **shall** be re-verified per AHES-SC-02, recorded as a baseline change (→ §15), and, for dependencies whose update policy (AHES-SC-A3) designates review, admitted per AHES-SC-04 before the new version is reachable.
- **AHES-SC-09** — Connectors and external services **shall** resolve only to allowlisted endpoints, and their returned content **shall** be treated as untrusted input subject to the same downstream handling as any other retrieved content (→ §2, → §7). An endpoint not on the allowlist is an unadmitted dependency under AHES-SC-01.

## 13.4 Verification criteria

| Control | Method |
|---|---|
| SC-01 | **Adversarial test**: introduce an undeclared dependency (unpinned package, unlisted endpoint, unregistered tool) and demonstrate the resolution path fails closed rather than admitting it. Analysis of the runtime dependency graph against the inventory. |
| SC-02 | Test: tamper with a pinned artifact's content and demonstrate integrity verification rejects it before load/execution. Inspection of the pin+hash records. |
| SC-03 | Inspection: every inventory entry carries origin, publisher, acquisition channel; analysis flags entries with unestablished provenance. |
| SC-04 | Demonstration: admitting a new dependency exercises the gate; **adversarial test** that first use of an unadmitted dependency is refused. Inspection of documented infeasibility rationale for procedural gates. |
| SC-05 | Test: attempt a model substitution outside the admission path and demonstrate it is gated (→ §1). Inspection that model entries appear in the inventory with version and provenance. |
| SC-06 | **Adversarial test**: supply an externally-authored prompt/tool schema and verify it is admitted as untrusted content, not granted harness-native instruction standing (→ §7). |
| SC-07 | Analysis: each inventory entry has a bounded, recorded privilege scope; demonstration that a compromised (fault-injected) dependency cannot exercise authority beyond its scope (containment verified under §14). |
| SC-08 | Test: attempt a silent version bump of a side-effecting dependency and demonstrate re-verification + baseline recording is required (→ §15). Inspection of update-policy adherence against the review cadence. |
| SC-09 | Test with a non-allowlisted endpoint: demonstrate refusal. **Adversarial test**: connector response carrying injection content is handled as untrusted (→ §2). |

## 13.5 Evidence requirements

The operating harness **shall** capture: each dependency load/resolution event (class, identity, version/content hash, timestamp); the result of each integrity verification (pass/fail, with the failing reference on failure); every admission decision (dependency, authorizing authority, disposition, timestamp); and every version/content change with its before/after identities. This is sufficient to answer, post-hoc: *what dependency was active at the time of an action, at what version, verified how, admitted by whom*. Evidence flows into the §10 evidence architecture; the version/identity record is the same fact the configuration baseline retains (→ §15), captured here at load time rather than reconstructed after.

## 13.6 Informative examples

**Vendor-in over reference (pattern).** One reference deployment reduces transitive trust by policy: rather than depending on an external repository's live content, useful logic is copied in and owned locally, so an upstream change cannot become a harness behavior change without passing through the local baseline (AHES-SC-01, AHES-SC-08). The tradeoff — carrying the maintenance of vendored code — is accepted deliberately as the cost of an in-gate dependency graph. The same deployment governs models as a dependency class rather than a routing detail, so a model change is an admission event (AHES-SC-05) rather than a silent substitution.

**General pattern.** SC-05 and SC-06 exist because the highest-blast-radius dependencies in an AI harness are not packages but the model and the instruction content it is given — precisely the inputs a package-oriented supply-chain program tends to leave outside the gate. A conformant harness widens the definition of "dependency" to include everything that can change model behavior at runtime.
