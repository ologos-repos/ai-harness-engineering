# Annex A — Reference-Implementation Conformance Studies (Informative)

**Status: v0.1. Informative — this annex illustrates the standard; it does not add or modify requirements. Normative content lives in Parts 0–5.**

## A.1 Purpose

Parts 2–5 specify requirements abstractly. This annex indexes the worked realizations against real deployments that ground those requirements in actual architecture — the source material lives in [`model-construct/`](../model-construct/); this annex is the document-level summary a reader of the standard proper would expect to find, per the informative-annex convention referenced at §5.8.

Four of the five entries are private repositories; they describe architectural patterns and design decisions — what kind of mechanism satisfies a clause, and why it's a meaningfully different data point — without reproducing implementation specifics. See [`model-construct/README.md`](../model-construct/README.md)'s disclosure note for the standing discipline this annex and its source material both hold to. The fifth entry, the AHES Reference Harness, is different in kind: a synthetic composite, not a citation of a real system — see A.2 and its own README for what that means.

## A.2 Exemplar index

| Exemplar | Substrate | AHES coverage | Primary contribution |
|---|---|---|---|
| [AHES Reference Harness](../model-construct/ahes-reference-harness/) | N/A — synthetic composite, not a real deployment | All 16 domains | The only complete, single-design coverage of the standard: patterns generalized from the four exemplars below plus ordinary distributed-systems/security practice, deliberately abstracted so no source system's IP is identifiable |
| [thinx](../model-construct/thinx/) | Anthropic Claude Code | §1–3, §6–15 (13 of 16 Part 2 domains) | Broadest single-*real*-deployment realization — also the standard's own author's harness, so its self-citation bias is named explicitly rather than hidden (see A.4) |
| [grok-console](../model-construct/grok-console/) | xAI Grok Build CLI | §4, §7 (alt. realization), §10, §16 | The strongest available substrate-independence proof: a governed harness built with no reference to this standard or to thinx's own patterns, on an entirely different model vendor |
| [mxh-p](../model-construct/mxh-p/) | N/A — a claim-verification kernel, not a deployment | §11 (primary — a schema-level gate against unverified completion claims), §16 (secondary — portability follows from how it's packaged) | Evidence that harness-engineerable claim-verification rigor is a buildable artifact, not a design aspiration — corrected 2026-07-16 from an earlier characterization that led with portability and understated the anti-hallucination design intent |
| [thinx-codex](../model-construct/thinx-codex/) | OpenAI Codex (a third, distinct model vendor from thinx's Anthropic and grok-console's xAI substrates; shares thinx's MxM construct philosophy, not its model provider) | Part 5 (conformance/assessment procedure) | The only *real-deployment* exemplar demonstrating *automated, CI-enforced* structural conformance checking, as distinct from every other exemplar's *runtime* enforcement mechanisms — a live precedent for what an AHES L2/L3 assessment tool could look like |

## A.3 Coverage map

Reading the index by domain rather than by exemplar — which Part 2 domains have at least one worked realization, and which don't yet:

- **Realized by at least one real-deployment exemplar:** §1, §2, §3, §4, §6, §7, §8, §9, §10, §11, §12, §13, §14, §16
- **Realized only as a non-normative pointer, not yet a worked real-deployment example:** §15 Configuration baseline — thinx's own entry states a fully worked realization of this clause is not yet drafted; don't count this as equivalent to the fifteen domains above.
- **Realized only by the synthetic composite, not yet by any real-deployment exemplar:** §5 Workflow engineering — see A.4's limitation on this specifically.

§7 Policy enforcement is the most-worked domain among the real exemplars, carrying two independent architectural realizations (thinx's wrap-and-intercept dispatcher pattern and grok-console's configure-the-native-gate pattern) — deliberately, since a requirement satisfiable only one way is a weaker requirement than one demonstrated two structurally opposite ways. The reference harness's own §7 design (see its `architecture.md` §3) uses both together rather than picking one.

## A.4 Limitations of this annex

- **No exemplar has been externally reviewed.** Every mapping in `model-construct/` was authored by the same party that authored the requirements it maps to. That is a real limitation on the evidentiary weight this annex can claim — it demonstrates that the requirements are *satisfiable*, not that they are *sufficient*, and independent replication is the natural next step, not yet done.
- **The AHES Reference Harness has not been built.** It's a design, not an implementation — no real system has been constructed to this composite and run end to end. It shows the requirements can be assembled into one coherent architecture; it does not demonstrate that architecture actually works under real operating conditions. Weight it as a completeness sketch, not as evidence of the same evidentiary kind the four real exemplars below it carry.
- **Of the four real exemplars, three are running deployments and one is not.** thinx, grok-console, and thinx-codex are governed harnesses actually operating in production. mxh-p is a package/kernel — real and shipped, but not itself a deployment; its evidentiary weight is "this mechanism is buildable and consumed by a real deployment (grok-console)," not "this mechanism is observed running end to end on its own." Don't read "four real exemplars" as "four deployments."
- **thinx is over-represented relative to its independence** among the real-deployment exemplars. It's the broadest single-deployment realization precisely because it's the easiest exemplar for this standard's author to describe accurately — see [`model-construct/thinx/README.md`](../model-construct/thinx/README.md) for that caveat stated at the source. Weight thinx's breadth against grok-console's independence, not in place of it.
- **Coverage is uneven and partial by design** among the real-deployment exemplars, per each entry's own stated gaps — no entry claims to fully discharge the domain it addresses; partial, honest mappings are the annex's actual standard for inclusion (see [`model-construct/template.md`](../model-construct/template.md)).
- **ThinxS (Go) and nemo-harness** remain named candidate subjects (§5.8) not yet attempted as real-deployment exemplars. A Go-substrate exemplar in particular would test §16's portability claim against a runtime, not just a model, boundary — distinct from every current real exemplar, which varies the model but not the harness's implementation language. It would also be the first real-deployment evidence for §5 Workflow engineering, which currently rests only on the synthetic composite.
