# Model Construct — grok-console — §16 Substrate Portability

This is grok-console's *consuming* side. The canonical mechanism it consumes is written up separately at [`model-construct/mxh-p/`](../mxh-p/) — read that first; this entry is the adoption evidence that the kernel is actually used by an independent harness, not merely published.

## Mapping

| AHES clause concern | Pattern observed | How it's satisfied |
|---|---|---|
| Identity/policy/memory survive substitution | The harness depends on a shared "orientation packet" builder: a governance-shaped envelope (covering mission, reasoning posture, constraints, tradecraft, memory, and execution-substrate concerns) injected at every delegated worker/role spawn | The packet is *the portability payload* — whatever spawns a worker (any model, any CLI) receives the same governing structure in the same shape, so the governing content is substrate-independent by construction, not by convention |
| Versioned, explicit dependency (not soft-coupling) | The kernel is consumed as a pinned, versioned dependency with a documented refresh procedure, and the consuming harness records exactly which surface of the kernel was adopted (and which parts were deliberately not adopted) | Portability is pinned and auditable: the dependency relationship states precisely what's consumed, which prevents the common failure mode where "we use the shared pattern" quietly drifts from what the shared pattern actually is |
| No accidental coupling | The consuming harness's own scope-boundary policy explicitly prohibits live coupling to the kernel's source location (e.g., no live import path into a sibling checkout) | Portability tooling doesn't get to bypass the harness's own self-containment rule; it's brought in and owned, not imported live |
| Bounded, specified behavioral change on substitution | Not fully realized at this scope — see Gaps | — |

## What's different from other exemplars

No other exemplar cited in this standard demonstrates §16 with an actual separate, versioned, installable package. thinx's portability claim (implicit in the standard's own MxM framing) is architectural — "the same construct shape could be ported" — but is not backed by a shipped artifact that a second harness actually depends on. The portability kernel referenced here is: it exists as its own repository, ships a defined schema, and the consuming harness's dependency record is dated evidence of a real consumption event, not a hypothetical.

## Gaps

- Only one surface of the kernel is consumed (the orientation-packet builder); the kernel's fuller portability surface (multi-worker runtime coordination, work-claim verification, federation across teams) is explicitly out of scope for this consumer. §16's "bounded, specified behavioral change" property is therefore only demonstrated for the orientation-packet slice, not for the kernel's full claimed surface — an honest partial realization, not a complete one.
- No evaluation was found (in this pass) that measures *what changes* when the underlying model/CLI is substituted while the orientation packet stays constant — the mechanism for carrying identity/policy/memory across substitution exists, but a behavioral-delta measurement (the actual conformance test §16 implies) was not located.
