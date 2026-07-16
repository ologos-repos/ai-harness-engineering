# Model Construct — grok-console — §16 Substrate Portability

This is grok-console's *consuming* side. The canonical mechanism it consumes is written up separately at [`model-construct/mxh-p/`](../mxh-p/) — read that first; this entry is the adoption evidence that the kernel is actually used by an independent harness, not merely published.

## Mapping

| AHES clause concern | Concrete artifact | How it's satisfied |
|---|---|---|
| Identity/policy/memory survive substitution | `construct/means/mxh_p/orientation.py` (vendored) — builds an "orientation packet": an MxM-shaped envelope (`mission`, `mind`, `morals`, `methods`, `memory`, `means`) injected at every delegated worker/role spawn | The packet is *the portability payload* — whatever spawns a worker (any model, any CLI) receives the same governing structure in the same schema shape, so the governing content is substrate-independent by construction, not by convention |
| Versioned, explicit dependency (not soft-coupling) | `construct/means/mxh_p/VENDOR.md` — records upstream repo, exact ported version (`0.1.0`), exactly which surface was ported (`orientation.py` only — CLI, hermetic adapter, and pge_gate explicitly *not* ported), and a copy-based refresh procedure | Portability is pinned and auditable: `VENDOR.md` states precisely what's vendored, which prevents the common failure mode where "we use the shared pattern" quietly drifts from what the shared pattern actually is |
| No accidental coupling | *"Do not add a runtime `PYTHONPATH` into a sibling `~/repos/mxh-p` (Morals O10 / P10)"* | The vendoring discipline is explicitly cross-referenced to grok-console's own morals.md scope constraints — portability tooling doesn't get to bypass the harness's own self-containment rule; it's copied in and owned, not imported live |
| Bounded, specified behavioral change on substitution | Not fully realized at this scope — see Gaps | — |

## What's different from other exemplars

No other exemplar cited in this standard demonstrates §16 with an actual separate, versioned, installable package. thinx's portability claim (implicit in the standard's own MxM framing) is architectural — "the same construct shape could be ported" — but is not backed by a shipped artifact that a second harness actually depends on. `mxh-p` is: it exists as its own repository, ships a schema, and grok-console's `VENDOR.md` is dated evidence of a real consumption event, not a hypothetical.

## Gaps

- Only `orientation.py` is vendored (per `VENDOR.md`); mxh-p's fuller portability surface (hermetic multi-worker runtime, the P/G/E claim-gate, federation) is explicitly out of scope for this consumer. §16's "bounded, specified behavioral change" property is therefore only demonstrated for the orientation-packet slice, not for the kernel's full claimed surface — an honest partial realization, not a complete one.
- No evaluation was found (in this pass) that measures *what changes* when the underlying model/CLI is substituted while the orientation packet stays constant — the mechanism for carrying identity/policy/memory across substitution exists, but a behavioral-delta measurement (the actual conformance test §16 implies) was not located.
