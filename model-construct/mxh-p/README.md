# Model Construct — mxh-p (the canonical §16 mechanism)

**Subject:** `ologos-repos/mxh-p` (private) — a reference implementation of a governance-synthesis pattern from the AIDE canon, shipped as an installable package with a CLI entrypoint.

**Why this exists, separately from grok-console's entry.** [`model-construct/grok-console/16-substrate-portability.md`](../grok-console/16-substrate-portability.md) documents one *consumer* of this kernel. This entry documents the kernel *itself* — the thing being ported, not one instance of porting it. AHES §16 asks whether identity/policy/memory can survive substrate substitution; this kernel is evidence the answer is "yes, mechanically, via a published, versioned schema" rather than "in principle, if you're disciplined."

**Scope discipline, briefly.** The package is deliberately narrow: it ships the orientation/governance-envelope layer and a work-verification ledger, and explicitly does not absorb an adjacent multi-worker coordination runtime that lives in a separate repository. A portability kernel that resists scope creep into adjacent concerns is more likely to stay portable — worth noting as a pattern in its own right.

## Domain realized: §16 Substrate Portability

| AHES clause concern | Pattern observed | How it's satisfied |
|---|---|---|
| A portable, schema-defined governing payload | The kernel defines a strict schema for the governing envelope injected at every spawn, with required top-level sections corresponding to each MxM construct surface (mission, reasoning posture, constraints, tradecraft, memory, execution substrate) | This is, functionally, **AHES's own domain vocabulary expressed as an enforced schema** — independent cross-check that the construct vocabulary this standard assumes is already load-bearing in shipped tooling, not a documentation-only convention |
| Explicit authority scoping carried across spawns | A spawned worker's authority is represented as an explicit, expirable, attributed grant object *inside the portable payload itself*, rather than assumed from ambient context | This is the mechanism-level answer to §9's principal-and-authority-model concern (whose authority an action was taken under), expressed as data rather than convention |
| Epistemic discipline carried across spawns | The payload carries a required labeling contract for claims (e.g., distinguishing computed, inferred, and uncertain content) into every spawned context | The packet doesn't just carry permissions — it carries a required reasoning-discipline contract, which is a portability property most governance-porting efforts don't attempt (they port *rules*, not *reasoning discipline*) |
| Distribution + version discipline | Ordinary package-engineering practice (versioning, install, a documented conformance mapping back to its own upstream pattern) applied to a governance artifact | This is precisely what makes portability testable rather than asserted |

## What's different from other exemplars

Every other exemplar in this standard (thinx, thinx-codex, grok-console's own construct) is a **deployment** — a harness doing its own governance. This kernel is **infrastructure for governance-porting itself** — a package whose entire purpose is to be the thing that travels. It's the clearest available evidence that AHES's §16 requirement (survival of identity/policy/memory across substitution, "with bounded, specified behavioral change") describes a buildable artifact class, not merely a design aspiration.

## Gaps

- The package's own documented scope is explicit and narrow — the governing-envelope layer and a work-verification ledger, with a fuller multi-worker coordination runtime, cross-team federation, and a larger project-lifecycle layer all named as out of scope for the current version. It demonstrates portability for the *orientation* slice of a harness, not the full sixteen-domain surface this standard specifies.
- This review did not inspect the work-verification ledger schema or the package's own conformance-checklist tooling in depth — a fuller write-up mapping that ledger onto AHES §5 (workflow) and §11 (evaluation) is a natural follow-up, not attempted here to avoid an ungrounded claim.
