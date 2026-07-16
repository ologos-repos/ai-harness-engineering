# Model Construct — Worked Realizations

**Status: v0.1 — one primary exemplar (grok-console), one kernel reference (mxh-p). Redacted to abstraction level 2026-07-16 — see note below.**

Part 2 of AHES specifies domains abstractly. This directory grounds them: each entry maps a real, running harness's architecture onto specific AHES clauses. This is the "informative examples" material referenced throughout Part 2 §N.6, given room to be complete rather than a paragraph.

**Why this exists.** A standard that can only cite its own author's harness (thinx) as evidence is not yet demonstrating substrate-generality — the property Part 2 §16 requires as a named conformance target. Each entry here is chosen specifically to extend or contrast with the existing thinx-based examples in the standard, not duplicate them.

**A note on disclosure.** The exemplars are private repositories. Entries describe architectural patterns and design decisions at a level useful for evaluating the standard — what kind of mechanism satisfies a clause, and why it's a meaningfully different data point from other exemplars — without reproducing implementation specifics (file paths, exact schema field names, verbatim policy text, internal ID/labeling schemes). Ask the repo owner before citing anything more specific than what's here.

## Exemplar selection

Two candidates were on the table: **thinx-codex** (a Codex-native sibling to thinx-Claude, sharing near-identical MxM construct shape and philosophy) and **grok-console** (a governed console for xAI's Grok Build CLI, architecturally independent). thinx-codex was set aside for this pass — its construct is close enough to thinx's own that a write-up would mostly restate the existing §7 example rather than test the standard against a genuinely different design. **grok-console was chosen** because:

- Different model substrate entirely (xAI Grok, not Anthropic Claude/Codex) — the strongest available proof that AHES's technical controls are substrate-agnostic, not accidentally shaped around one vendor's harness idioms.
- Its policy-enforcement realization is structurally different from thinx's (a deny-pattern catalog consumed by the CLI's own native permission system, rather than a wrapping hook) — a genuine second data point for §7, not a restatement.
- It depends on a **separate, generic portability package**, which makes §16 (substrate portability) concrete rather than aspirational: there is an actual shared, versioned package, with a schema-defined governing payload, that more than one independent harness consumes.
- It cleanly separates several agent roles by individual contract (identity, access level, scope boundary, done-criteria), giving §4 a realization that isn't just "the harness has subagents."

**thinx-codex** (the canonical repository, not a deployment snapshot of it) runs a fail-closed conformance gate in continuous integration, checking authored strategy/requirements/architecture/verification artifacts against a small set of structural integrity rules (no orphaned architecture, no unallocated requirement, referential integrity, acyclic dependencies, and similar), alongside sibling gates for its memory, execution-substrate, and tradecraft layers. This traceability discipline is close kin to AHES's own requirement-ID scheme, and the fail-closed/fail-open rule split is a candidate pattern for Part 5's level structure. It looks like the strongest candidate for a **future Part 5 (conformance) exemplar** — flagged as follow-up rather than written up now, since this pass did not go deep enough into the gate mechanics to ground a clause mapping honestly, and any deeper write-up should stay at this same abstraction level.

## Contents

| Entry | AHES domains realized | What it demonstrates |
|---|---|---|
| [`grok-console/`](grok-console/) | §4 Agent engineering, §7 Policy enforcement (alt. realization), §10 Evidence engineering, §16 Substrate portability | A complete, independently-designed governed harness on a different model substrate |
| [`mxh-p/`](mxh-p/) | §16 Substrate portability (canonical mechanism) | A generic, reusable portability kernel — not one deployment's ad hoc solution, but a package multiple harnesses depend on |

## How to add an exemplar

Use [`template.md`](template.md). A realization entry is honest about what it *doesn't* cover — partial mappings (three domains realized, thirteen not attempted) are more useful than a padded claim across all sixteen. It is equally honest about staying at the architectural-pattern level when the source is private — describe the mechanism, not the implementation.
