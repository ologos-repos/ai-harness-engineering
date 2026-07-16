# Model Construct — Worked Realizations

**Status: v0.1 — one primary exemplar (grok-console), one kernel reference (mxh-p).**

Part 2 of AHES specifies domains abstractly. This directory grounds them: each entry maps a real, running harness's actual files, schemas, and enforcement code onto specific AHES clauses. This is the "informative examples" material referenced throughout Part 2 §N.6, given room to be complete rather than a paragraph.

**Why this exists.** A standard that can only cite its own author's harness (thinx) as evidence is not yet demonstrating substrate-generality — the property Part 2 §16 requires as a named conformance target. Each entry here is chosen specifically to extend or contrast with the existing thinx-based examples in the standard, not duplicate them.

## Exemplar selection

Two candidates were on the table: **thinx-codex** (a Codex-native sibling to thinx-Claude, sharing near-identical MxM construct shape and philosophy) and **grok-console** (a governed console for xAI's Grok Build CLI, architecturally independent). thinx-codex was set aside for this pass — its construct is close enough to thinx's own that a write-up would mostly restate the existing §7 example rather than test the standard against a genuinely different design. **grok-console was chosen** because:

- Different model substrate entirely (xAI Grok, not Anthropic Claude/Codex) — the strongest available proof that AHES's technical controls are substrate-agnostic, not accidentally shaped around one vendor's harness idioms.
- Its policy-enforcement realization is structurally different from thinx's (a deny-glob catalog fed into the CLI's native `--deny` flag, versus a `PreToolUse` dispatcher hook) — a genuine second data point for §7, not a restatement.
- It vendors a **separate, generic portability kernel** (`ologos-repos/mxh-p`), which makes §16 (substrate portability) concrete rather than aspirational: there is an actual shared package, with an actual JSON Schema, that multiple independent harnesses consume.
- Its `construct/means/agents/` directory cleanly separates six agent roles by contract (`AGENT.md` per role, each naming access level and scope), giving §4 a realization that isn't just "the harness has subagents."

**thinx-codex-03** (a sibling MBSE-formal repo with fail-closed conformance gates — `conformance_gate.py`, `memory_gate.py`, `means_gate.py`, `methods_gate.py` run in CI) was noticed during this review and looks like the strongest candidate for a **future Part 5 (conformance) exemplar** — its requirement/architecture/verification traceability convention (`<KIND>-<AREA>-<NNNN>` IDs, fail-closed vs. fail-open rule split) is close kin to AHES's own `AHES-<domain>-<nn>` scheme. Flagged as follow-up rather than written up now, since this pass did not go deep enough into its actual gate scripts to ground a clause mapping honestly.

## Contents

| Entry | AHES domains realized | What it demonstrates |
|---|---|---|
| [`grok-console/`](grok-console/) | §4 Agent engineering, §7 Policy enforcement (alt. realization), §10 Evidence engineering, §16 Substrate portability | A complete, independently-designed governed harness on a different model substrate |
| [`mxh-p/`](mxh-p/) | §16 Substrate portability (canonical mechanism) | A generic, reusable portability kernel — not one deployment's ad hoc solution, but a package multiple harnesses depend on |

## How to add an exemplar

Use [`template.md`](template.md). A realization entry is honest about what it *doesn't* cover — partial mappings (three domains realized, thirteen not attempted) are more useful than a padded claim across all sixteen.
