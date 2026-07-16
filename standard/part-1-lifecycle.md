# AHES Part 1 — Lifecycle Processes

**Status: Draft v0.1 — spine fixed; harness-specific process insertions drafted.**

## 1.1 Lifecycle spine

The harness is a system-of-interest per **ISO/IEC/IEEE 15288**, applied recursively: the harness contains models, software, data, humans, infrastructure, and procedures as system elements. The software-intensive control plane follows **ISO/IEC/IEEE 12207**. **ISO/IEC 5338** supplies the AI-specific process adaptations for the model-dependent elements.

AHES does not restate those processes. This part specifies only the *harness-specific insertions* — processes the spine does not name and a harness cannot conform without. Requirement IDs in this part use the `AHES-LC-<nn>` prefix.

## 1.2 Harness-specific process insertions

The eight insertions below are summarized in the table, then specified individually in §1.2.1–§1.2.8. Each subsection names the trigger, the artifact produced or updated, the lifecycle process it attaches to, and the normative statements that make the insertion enforceable.

| Insertion | Attaches to (15288/12207 process) | Concern |
|---|---|---|
| Prompt and instruction change control | Configuration Management | Instructions are behavior-bearing configuration, not documentation; changes are versioned, reviewed, and evaluated before release (→ §2 Context engineering, §15 Configuration baseline) |
| Model substitution qualification | Verification / Validation / Transition | A model swap is a system change: qualification against the §16 Substrate portability bound and Part 3 allocation before cutover |
| Tool onboarding and retirement | Integration / Configuration Management | Registering a capability = expanding the action surface: side-effect classification, permission binding, policy review (→ §3 Tool engineering, §7 Policy enforcement) |
| Memory schema migration | Maintenance | Durable memory outlives sessions and models; migrations preserve provenance and isolation properties (→ §6 Memory engineering) |
| Policy baseline release | Configuration Management / Quality Assurance | Policy document + enforcement inventory released as one consistent unit, with the §7 self-test as release gate |
| Evidence retention and disposal | Information Management / Disposal | Evidence has a defined retention life tied to accountability requirements, and a defined disposal that does not orphan open conformance claims |
| Harness decommissioning | Disposal | Retirement of a harness disposes of credentials, memory, and evidence deliberately — not by abandonment |
| Evaluation-regime change control | Configuration Management / Quality Assurance | The evaluation suite is the gate every other insertion above relies on (→ AHES-LC-02, LC-04); a threshold, test case, or adversarial-corpus change is itself a baseline change requiring the same rigor as what it gates |

### 1.2.1 Prompt and instruction change control

**Trigger.** Any change to behavior-bearing instruction content: system prompts, standing agent instructions, the reasoning/identity substrate a harness loads at session start (the MxM reference architecture treats this as versioned configuration, not prose), tool-schema descriptions that steer model behavior, or the templates that assemble them.

**Artifact.** A versioned instruction set under change control, with a change record naming the author, the reviewing authority, the diff, and the evaluation result that cleared it (→ §2 Context engineering for composition, §15 Configuration baseline for the version anchor).

**Lifecycle attachment.** Configuration Management (12207 / 15288). Instruction content is configuration items, managed with the same rigor as source code, not treated as documentation exempt from review.

- **AHES-LC-01** — Behavior-bearing instruction content **shall** be held under version control as configuration items. A change to instruction content **shall** be traceable to an author, a reviewer distinct from the author where the change is not self-service low-risk, and a configuration-baseline version (→ §15).
- **AHES-LC-02** — An instruction change that alters agent behavior **shall** be evaluated against the §11 Evaluation regime before release to a production baseline. A change released without evaluation **should** be recorded as a deviation with rationale, and **shall not** be claimed as conformant at L2 or above.

### 1.2.2 Model substitution qualification

**Trigger.** Substitution of the underlying model or model version behind any harness surface — provider change, version upgrade, fallback-tier reconfiguration, or routing change that alters which model serves a given class of invocation.

**Artifact.** A qualification record: the substitution scope, the portability bound the new substrate is required to hold within, the evaluation results demonstrating the bound holds, and the cutover/rollback decision (→ §16 Substrate portability for the bound and its conformance test, → Part 3 for the quality-characteristic allocation that must still hold post-swap, → §11 Evaluation for the regime run).

**Lifecycle attachment.** Verification, Validation, and Transition (15288). A model swap is a system change, not a configuration tweak, because model behavior is a load-bearing system element.

- **AHES-LC-03** — A model substitution **shall** be qualified before cutover against the substrate-portability bound specified for the deployment (→ §16), demonstrating that harness identity, policy, and memory behavior remain within the declared bound.
- **AHES-LC-04** — A model substitution **shall** re-verify the Part 3 quality-characteristic allocation affected by the swap, so that any regression attributable to the model — rather than the harness — is identified before the substituted model serves production traffic. Cutover **should** retain a rollback path to the prior qualified model until the substituted model has operated within bound for a defined observation window.

### 1.2.3 Tool onboarding and retirement

**Trigger.** Registration of a new tool or capability into the harness action surface, a change to an existing tool's side-effect profile or permissions, or retirement of a tool from the surface.

**Artifact.** An updated tool registry entry carrying the tool's side-effect classification, its permission binding, and the policy-enforcement review that cleared it; on retirement, a record that the capability and its residual permissions were removed, not merely hidden (→ §3 Tool engineering for the schema and classification, → §7 Policy enforcement for the enforcement-point review).

**Lifecycle attachment.** Integration and Configuration Management (15288 / 12207). Adding a tool expands what the harness can do to the world, so onboarding is an integration event with a policy gate, not a config append.

- **AHES-LC-05** — A tool **shall not** enter the production action surface without a recorded side-effect classification and permission binding (→ §3), and a policy-enforcement review confirming that its highest-consequence action classes are covered by the appropriate enforcement points (→ §7).
- **AHES-LC-06** — Tool retirement **shall** remove the capability and revoke its associated permissions and credentials from the active configuration baseline; a tool marked deprecated but still invocable **shall** remain subject to its full onboarding controls until actually removed.

### 1.2.4 Memory schema migration

**Trigger.** A change to the structure, scope, or retention semantics of durable memory: schema alteration, store migration, consolidation of stores, or a change to the isolation boundaries between sessions, agents, or principals.

**Artifact.** A migration record demonstrating that provenance and isolation properties are preserved across the migration, plus the reversibility or archival disposition of the pre-migration state (→ §6 Memory engineering for the provenance and isolation properties migrations must preserve).

**Lifecycle attachment.** Maintenance (15288 / 12207). Durable memory outlives the sessions and models that wrote it, so schema evolution is a maintenance process with continuity obligations, not a one-time build step.

- **AHES-LC-07** — A memory schema migration **shall** preserve the provenance and cross-session/cross-principal isolation properties required by §6; a migration that cannot demonstrate preservation of these properties **shall** be treated as a defect and **shall not** be released.
- **AHES-LC-08** — A migration that alters memory retention or expiration semantics **shall** be reconciled with the evidence retention rule (§1.2.6) so that records under an active retention obligation are not silently expired by a schema change.

### 1.2.5 Policy baseline release

**Trigger.** Any change to the policy document, the enforcement-point inventory, the trusted-default scope, or the escalation triggers that convert trusted actions into flagged ones.

**Artifact.** A single released policy baseline: the policy document and its enforcement-point inventory versioned and released together as one consistent unit, with the §7 document-to-inventory self-test result recorded as the release gate (→ §7 Policy enforcement, → §15 Configuration baseline).

**Lifecycle attachment.** Configuration Management and Quality Assurance (12207). The policy document and the enforcement layer are two views of one control set; releasing them out of step is the divergence the §7 self-test exists to catch.

- **AHES-LC-09** — The policy document and its enforcement-point inventory **shall** be released as a single versioned baseline. A release **shall not** proceed unless the §7 document-to-inventory consistency self-test passes: every hard stop in the document has an enforcement point and every enforcement point is disclosed in the document.
- **AHES-LC-10** — A change to a non-overridable hard-stop class (→ §7) **shall** be released only through this policy-baseline process under explicit change control, and **shall not** be effected by an operational override.

### 1.2.6 Evidence retention and disposal

**Trigger.** Establishment or change of an evidence retention period, a scheduled disposal event, or a disposal request touching records that may be under an accountability obligation.

**Artifact.** A retention schedule binding each evidence class to a retention life derived from its accountability requirement, and a disposal record showing that disposed evidence was not under an open obligation at the time of disposal (→ §10 Evidence engineering for the evidence architecture, → §15 Configuration baseline for the baseline-linked retention rule).

**Lifecycle attachment.** Information Management and Disposal (15288). Evidence is a managed information asset with a defined life, not an unbounded append log.

- **AHES-LC-11** — Each evidence class **shall** have a defined retention life tied to its accountability requirement, recorded in a retention schedule. Evidence **shall not** be disposed of while it is within an active retention obligation or supports an open conformance claim (→ Part 5).
- **AHES-LC-12** — Evidence disposal **shall** be a deliberate, recorded event; silent expiry, orphaning, or loss of evidence that a conformance claim depends on is a conformance defect, not a neutral outcome.

### 1.2.7 Harness decommissioning

**Trigger.** Retirement of a harness deployment from service, whether by replacement, consolidation, or shutdown.

**Artifact.** A decommissioning record accounting for the deliberate disposition of the harness's credentials, durable memory, evidence, and any third-party access it held (→ §14 Security architecture for credential boundaries, → §6 Memory engineering, → §10 Evidence engineering).

**Lifecycle attachment.** Disposal (15288 / 12207). A retired harness that is abandoned rather than decommissioned leaves live credentials, readable memory, and orphaned evidence behind.

- **AHES-LC-13** — Decommissioning a harness **shall** revoke or transfer its credentials and third-party access, and **shall** dispose of or archive its durable memory and evidence according to the retention schedule (§1.2.6) — not by abandonment. Evidence still under an active retention obligation at decommissioning **shall** be transferred to a custodian, not destroyed.

### 1.2.8 Evaluation-regime change control

**Trigger.** Any change to the §11 evaluation regime itself: a pass/fail threshold, an added or removed test case, a change to the adversarial-test corpus, or a change to what counts as the qualification baseline (→ §11 EV-A1/EV-A2).

**Artifact.** A versioned evaluation-regime baseline with a change record naming the author, the reviewing authority, the diff, and the rationale — structurally identical to §1.2.1's instruction-change-control artifact, because the evaluation regime is exactly as behavior-bearing as the instructions it gates (→ §11 Evaluation for the regime itself, → §15 Configuration baseline for the version anchor, since AHES's own configuration-baseline definition in Part 0 already names "evaluations" as a first-class baseline element).

**Lifecycle attachment.** Configuration Management and Quality Assurance (12207). This insertion exists because every other insertion in this part ultimately answers to the evaluation regime: AHES-LC-02 gates instruction changes against it, AHES-LC-04 re-verifies allocation against it after a model substitution, and Part 5's AHES-CF-01 through CF-09 treat "evaluations" as a baseline element whose change triggers re-assessment. A gate that can itself be loosened or narrowed with no change control is a gate in name only — the regime needs the same rigor as what it gates, not less.

- **AHES-LC-14** — A change to the evaluation regime **shall** be held under version control as a configuration item, traceable to an author and a reviewer distinct from the author, with the rationale recorded (→ §15). A threshold loosened, a test case removed, or an adversarial case dropped from the corpus **shall** be visible in that record — the change control exists specifically so a weakening of the gate cannot happen silently.
- **AHES-LC-15** — A change to the evaluation regime **shall not** be self-qualified by that same regime: the reviewer required by AHES-LC-14 **shall** independently assess whether the change narrows coverage in a way that would let a previously-caught regression pass, since the regime cannot be trusted to catch a defect in itself. A regime change that narrows coverage **shall** be justified in the change record, not merely logged.

## 1.3 Recursion note

Agents spawned by the harness are system elements with their own micro-lifecycles (creation, mandate, termination — §4 Agent engineering). The lifecycle processes in this part apply to the *harness*; agent lifecycles are a Part 2 engineering domain, not a Part 1 process.

## 1.4 Applicability of process insertions by conformance level

Not every insertion binds at every conformance level. Part 5 scopes which apply where: broadly, the L1 (Documented) claim requires that these processes *exist and are recorded* — a change-control practice, a retention schedule, a decommissioning procedure — while L2 (Enforced) requires that they are *operating and producing the artifacts and evidence* named above, and L3 (Verifiable) requires that a third party can *independently confirm* the process ran for a given change (e.g., that a specific model substitution was qualified per §1.2.2 before cutover, from the evidence rather than from attestation). This is a forward pointer only; the binding per-level selection rule is specified in Part 5 §5.4, and is expressed structurally against each domain's clause sections rather than by re-enumerating these insertions here.
