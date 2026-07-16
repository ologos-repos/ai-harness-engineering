# Model Construct — grok-console — §10 Evidence Engineering

## Mapping

| AHES clause concern | Pattern observed | How it's satisfied |
|---|---|---|
| Fixed, versioned record shape | Evidence records validate against a closed schema, itself explicitly versioned | Evidence is not free-text logging; every record must conform to a defined shape, and the shape's own version is tracked so a future change is a detectable break, not a silent drift |
| Attributable, orderable records | Records carry a collision-resistant identifier encoding creation time, a session identifier, and a monotonically increasing per-session sequence number | Two properties evidence needs for post-hoc reconstruction that a bare timestamp doesn't guarantee alone: unique identity and total order within a session, independent of clock skew |
| Closed vocabulary of event types | Event types are drawn from a fixed, enumerated set covering session lifecycle, approval decisions, and command-level outcomes | A fixed enum means every consumer (assessor, dashboard, incident review) can enumerate the full event space from the schema alone — no need to reverse-engineer what event types exist from log samples |
| Binding to authority/policy layer | Every evidence record states which authority class the underlying action fell under, using the same class vocabulary as the §7 enforcement layer | Evidence records don't just say *what* happened, they say *under what authority* — directly enabling a "was this action within its permitted class" check from the evidence alone |
| Append-only, structurally validated writer | Records are validated against the schema at write time, using a minimal-dependency writer | Validation happens at write time, not read time — a malformed record cannot enter the store, rather than being caught (or missed) later by a downstream consumer |
| Runtime location and lifecycle | Evidence is generated at runtime and kept out of version control, distinct from the versioned configuration that governs the harness | Evidence is explicitly separated from configuration baseline — consistent with AHES §15 distinguishing what's versioned-as-config from what's generated-at-runtime |

## What's different from other exemplars

thinx's evidence realization (cited inline in §7.6) is a single-purpose append-only override log — sufficient for its one enforcement-layer's audit need, but not a general evidence schema covering session lifecycle or queueing beyond overrides. grok-console's schema is the more complete §10 exemplar: it's a general evidence architecture the *whole* harness writes into, not one control's audit trail. The authority-class binding is also a concrete answer to a question Part 3's incident-separability test (§3.4) poses abstractly — whether a postmortem can classify a failure by locus from captured evidence alone — since every record already states which authority class it fell under.

## Gaps

- The schema defines the record shape and a validating writer; it does not on its own guarantee *completeness* (that every harness action actually produces a record) — that's a §12 (operational assurance) concern the schema doesn't and shouldn't try to close.
- No retention-period or disposal policy was found attached to this evidence stream in this pass — relevant to AHES Part 1's "evidence retention and disposal" lifecycle insertion, still a skeleton in this standard.
