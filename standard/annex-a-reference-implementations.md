# Annex A — Reference-Implementation Conformance Studies (Informative)

**Status: v0.1. Informative — this annex illustrates the standard; it does not add or modify requirements. Normative content lives in Parts 0–5.**

## A.1 Purpose

Parts 2–5 specify requirements abstractly. This annex indexes the worked realizations against real deployments that ground those requirements in actual architecture — the source material lives in [`model-construct/`](../model-construct/); this annex is the document-level summary a reader of the standard proper would expect to find, per the informative-annex convention referenced at §5.8.

Every exemplar here is a private repository. Entries describe architectural patterns and design decisions — what kind of mechanism satisfies a clause, and why it's a meaningfully different data point — without reproducing implementation specifics. See [`model-construct/README.md`](../model-construct/README.md)'s disclosure note for the standing discipline this annex and its source material both hold to.

## A.2 Exemplar index

| Exemplar | Substrate | AHES coverage | Primary contribution |
|---|---|---|---|
| [thinx](../model-construct/thinx/) | Anthropic Claude Code | §1–3, §6–15 (13 of 16 Part 2 domains) | Broadest single-deployment realization — also the standard's own author's harness, so its self-citation bias is named explicitly rather than hidden (see A.4) |
| [grok-console](../model-construct/grok-console/) | xAI Grok Build CLI | §4, §7 (alt. realization), §10, §16 | The strongest available substrate-independence proof: a governed harness built with no reference to this standard or to thinx's own patterns, on an entirely different model vendor |
| [mxh-p](../model-construct/mxh-p/) | N/A — a claim-verification kernel, not a deployment | §11 (primary — a schema-level gate against unverified completion claims), §16 (secondary — portability follows from how it's packaged) | Evidence that harness-engineerable claim-verification rigor is a buildable artifact, not a design aspiration — corrected 2026-07-16 from an earlier characterization that led with portability and understated the anti-hallucination design intent |
| [thinx-codex](../model-construct/thinx-codex/) | Anthropic Claude Code (Codex-native variant) | Part 5 (conformance/assessment procedure) | The only exemplar demonstrating *automated, CI-enforced* structural conformance checking, as distinct from every other exemplar's *runtime* enforcement mechanisms — a live precedent for what an AHES L2/L3 assessment tool could look like |

## A.3 Coverage map

Reading the index by domain rather than by exemplar — which Part 2 domains have at least one worked realization, and which don't yet:

- **Realized (by at least one exemplar):** §1, §2, §3, §4, §6, §7, §8, §9, §10, §11, §12, §13, §14, §15, §16
- **Not yet realized by any exemplar:** §5 Workflow engineering

§7 Policy enforcement is the most-worked domain, carrying two independent architectural realizations (thinx's wrap-and-intercept dispatcher pattern and grok-console's configure-the-native-gate pattern) — deliberately, since a requirement satisfiable only one way is a weaker requirement than one demonstrated two structurally opposite ways.

## A.4 Limitations of this annex

- **No exemplar has been externally reviewed.** Every mapping in `model-construct/` was authored by the same party that authored the requirements it maps to. That is a real limitation on the evidentiary weight this annex can claim — it demonstrates that the requirements are *satisfiable*, not that they are *sufficient*, and independent replication is the natural next step, not yet done.
- **thinx is over-represented relative to its independence.** It's the broadest single-deployment realization precisely because it's the easiest exemplar for this standard's author to describe accurately — see [`model-construct/thinx/README.md`](../model-construct/thinx/README.md) for that caveat stated at the source. Weight thinx's breadth against grok-console's independence, not in place of it.
- **Coverage is uneven and partial by design**, per each entry's own stated gaps — no entry claims to fully discharge the domain it addresses; partial, honest mappings are the annex's actual standard for inclusion (see [`model-construct/template.md`](../model-construct/template.md)).
- **ThinxS (Go) and nemo-harness** remain named candidate subjects (§5.8) not yet attempted. A Go-substrate exemplar in particular would test §16's portability claim against a runtime, not just a model, boundary — distinct from every current exemplar, which varies the model but not the harness's implementation language.
