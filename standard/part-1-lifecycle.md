# AHES Part 1 — Lifecycle Processes

**Status: Skeleton — spine fixed; harness-specific process insertions to be drafted.**

## 1.1 Lifecycle spine

The harness is a system-of-interest per **ISO/IEC/IEEE 15288**, applied recursively: the harness contains models, software, data, humans, infrastructure, and procedures as system elements. The software-intensive control plane follows **ISO/IEC/IEEE 12207**. **ISO/IEC 5338** supplies the AI-specific process adaptations for the model-dependent elements.

AHES does not restate those processes. This part specifies only the *harness-specific insertions* — processes the spine does not name and a harness cannot conform without.

## 1.2 Harness-specific process insertions (to be drafted)

| Insertion | Attaches to (15288/12207 process) | Concern |
|---|---|---|
| Prompt and instruction change control | Configuration Management | Instructions are behavior-bearing configuration, not documentation; changes are versioned, reviewed, and evaluated before release (→ Part 2 §2, §15) |
| Model substitution qualification | Verification / Validation / Transition | A model swap is a system change: qualification against the Part 2 §16 portability bound and Part 3 allocation before cutover |
| Tool onboarding and retirement | Integration / Configuration Management | Registering a capability = expanding the action surface: side-effect classification, permission binding, policy review (→ §3, §7) |
| Memory schema migration | Maintenance | Durable memory outlives sessions and models; migrations preserve provenance and isolation properties (→ §6) |
| Policy baseline release | Configuration Management / Quality Assurance | Policy document + enforcement inventory released as one consistent unit, with the §7 self-test as release gate |
| Evidence retention and disposal | Information Management / Disposal | Evidence has a defined retention life tied to accountability requirements, and a defined disposal that does not orphan open conformance claims |
| Harness decommissioning | Disposal | Retirement of a harness disposes of credentials, memory, and evidence deliberately — not by abandonment |

## 1.3 Recursion note

Agents spawned by the harness are system elements with their own micro-lifecycles (creation, mandate, termination — Part 2 §4). The lifecycle processes in this part apply to the *harness*; agent lifecycles are a Part 2 engineering domain, not a Part 1 process.
