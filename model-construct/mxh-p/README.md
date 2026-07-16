# Model Construct — mxh-p (the canonical §16 mechanism)

**Subject:** `ologos-repos/mxh-p` (private) — **MxH-P/core**, a Pattern B+ reference implementation of the AIDE canon's MxH-P synthesis pattern (ADR-EA-0028). Ships as an installable Python package (`pip install -e ".[dev]"`, CLI entrypoint `mxh-p`).

**Why this exists, separately from grok-console's entry.** [`model-construct/grok-console/16-substrate-portability.md`](../grok-console/16-substrate-portability.md) documents one *consumer* of this kernel. This entry documents the kernel *itself* — the thing being ported, not one instance of porting it. AHES §16 asks whether identity/policy/memory can survive substrate substitution; mxh-p is evidence the answer is "yes, mechanically, via a published schema" rather than "in principle, if you're disciplined."

**MxM · Hermetic · P/G/E, briefly, per the package's own framing:** *"MxM orients and constrains · Hermetic schedules and coordinates · P/G/E proves substantial work was planned, built, and skeptically accepted."* mxh-p ships only the MxM-orientation and P/G/E-ledger slices (v0.1 scope); the Hermetic multi-worker runtime lives in a separate repo (`ologos-repos/Hermit`) and is explicitly not absorbed here — a scope discipline worth noting in its own right (a portability kernel that resists scope creep into adjacent concerns is more likely to stay portable).

## Domain realized: §16 Substrate Portability

| AHES clause concern | Concrete artifact | How it's satisfied |
|---|---|---|
| A portable, schema-defined governing payload | `schemas/orientation.packet.schema.json` — `$schema: draft/2020-12`, required top-level: `schema_version`, `mission`, `mind`, `morals`, `methods`, `memory`, `means` | This is, functionally, **AHES's own domain vocabulary expressed as an enforced JSON Schema** — `mission`/`mind`/`morals`/`methods`/`memory`/`means` are exactly the MxM construct surfaces, now with `additionalProperties: false` closure and per-surface required sub-fields (e.g. `mission.scope_class` constrained to an enum: `in_home`, `authorized_surface`, `reference_only`). Independent cross-check that the construct vocabulary this standard assumes is already load-bearing in shipped tooling, not a documentation-only convention |
| Explicit authority scoping carried across spawns | `mission.authority_grant` (nullable object: `surface`, `expires`, `by`) | A spawned worker's authority is not ambient — it's an explicit, expirable, attributed grant object *inside the portable packet itself*. This is the mechanism-level answer to §9's Principal & authority model concern (whose authority an action was taken under), expressed as data rather than convention |
| Epistemic discipline carried across spawns | `mind.epistemic_labels` enum: `computed`, `inferred`, `uncertain`; `mind.ground_before_assert` (required) | The packet doesn't just carry permissions — it carries a required epistemic-labeling contract into every spawned context, which is a portability property most governance-porting efforts don't attempt (they port *rules*, not *reasoning discipline*) |
| Distribution + version discipline | `pyproject.toml` package, `docs/canon-mapping.md` (Pattern B+ conformance manifest), semantic-versioned ports (grok-console pins `0.1.0`) | Ordinary package-engineering discipline (versioning, install, conformance mapping to its own upstream canon pattern) applied to a governance artifact — which is precisely what makes portability testable rather than asserted |

## What's different from other exemplars

Every other exemplar in this standard (thinx, thinx-codex, grok-console's own construct/) is a **deployment** — a harness doing its own governance. mxh-p is **infrastructure for governance-porting itself** — a package whose entire purpose is to be the thing that travels. It's the clearest available evidence that AHES's §16 requirement (survival of identity/policy/memory across substitution, "with bounded, specified behavioral change") describes a buildable artifact class, not merely a design aspiration.

## Gaps

- v0.1 scope is explicit and narrow: orientation-packet schema + builder, a P/G/E ledger + fail-closed package gate, role profiles, a minimal DENY/CONFIRM catalog seed, a construct-template skeleton. The Hermetic multi-worker runtime, federation/multi-prime, "full Eidolon PLM," and absorbing Hermetic/prime homes are named out-of-scope for this version — mxh-p demonstrates portability for the *orientation* slice of a harness, not the full sixteen-domain surface this standard specifies.
- This review did not inspect `pge.ledger.schema.json`, the `conform core` checklist, or `docs/canon-mapping.md`'s Pattern B+ criteria in depth — a fuller write-up mapping the P/G/E ledger onto AHES §5 (workflow) and §11 (evaluation) is a natural follow-up, not attempted here to avoid an ungrounded claim.
