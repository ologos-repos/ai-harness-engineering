# Model Construct — grok-console — §10 Evidence Engineering

## Mapping

| AHES clause concern | Concrete artifact | How it's satisfied |
|---|---|---|
| Fixed, versioned record shape | `construct/means/observability/evidence-record.schema.json` — JSON Schema, `additionalProperties: false`, `schema_version: {"type": "integer", "const": 1}` | Evidence is not free-text logging; every record validates against a closed schema, and the schema itself is versioned so a future shape change is a detectable break, not a silent drift |
| Attributable, orderable records | Required fields `evidence_id` (pattern `^ev-[0-9]{8}T[0-9]{6}Z-[a-f0-9]{8}$`), `session_id`, `sequence` (monotonic, `minimum: 1`), `timestamp` | ID encodes creation time + a random suffix (collision-resistant); `sequence` gives total order within a session independent of clock skew — two properties evidence needs for post-hoc reconstruction that a bare timestamp doesn't guarantee alone |
| Closed vocabulary of event types | `event_type` enum: `session.started/completed/failed`, `approval.decided`, `command.observed/denied/confirm_blocked/confirm_approved`, `queue.enqueued/drained`, `noop.recorded` | A fixed enum means every consumer (assessor, dashboard, incident review) enumerates the full event space from the schema alone — no need to reverse-engineer what event types exist from log samples |
| Binding to authority/policy layer | `authority_class` field, enum aligned to `means/safety/approval_classes.yaml` | Evidence records don't just say *what* happened, they say *under what authority class* — directly enabling the §4/§9-style question "was this action taken within its permitted class" from the evidence alone, without cross-referencing a separate log |
| Append-only, structurally validated writer | `record.py` — stdlib-only writer with structural validation before write | Validation happens at write time, not read time — a malformed record cannot enter the store, rather than being caught (or missed) later by a downstream consumer |
| Runtime location and lifecycle | `data/evidence/events.jsonl` (gitignored — runtime state, not source) | Evidence is explicitly separated from version-controlled configuration: JSONL append log lives in runtime data, not the repo — consistent with AHES-CB (§15) distinguishing configuration baseline (versioned) from evidence (runtime-generated) |

## What's different from other exemplars

thinx's evidence realization (cited inline in §7.6) is a single-purpose append-only override log — sufficient for its one enforcement-layer's audit need, but not a general evidence schema covering session lifecycle, queueing, or confirm/deny events beyond overrides. grok-console's schema is the more complete §10 exemplar: it's a general evidence architecture the *whole* harness writes into (session start/end, every safety-catalog decision, queue state), not one control's audit trail. The `authority_class` binding is also a concrete answer to a question Part 3's incident-separability test (§3.4) poses abstractly — "can a postmortem classify a failure as M/H/S/O from captured evidence alone" — since every record already states which authority class it fell under.

## Gaps

- The schema defines the record shape and a validating writer; it does not on its own guarantee *completeness* (that every harness action actually calls `record.py`) — that's a §12 (operational assurance) concern the schema doesn't and shouldn't try to close.
- `data/evidence/events.jsonl` being gitignored means evidence retention/backup is an operational concern outside this schema's scope — no retention-period or disposal policy was found attached to this artifact in this pass (relevant to AHES Part 1 §1.2's "evidence retention and disposal" lifecycle insertion, still a skeleton in this standard).
