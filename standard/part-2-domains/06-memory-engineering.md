# AHES Part 2 §6 — Memory Engineering

**Status: Draft v0.1 — worked clause (normative shape complete; requirement set subject to review)**

## 6.1 Purpose

Memory engineering governs what persists across sessions: scope, schema, provenance, expiration, staleness handling, and cross-session/cross-principal isolation. Unengineered, the failure classes are **stale recall treated as current fact**, **secrets accreting in durable stores**, and **cross-context leakage** — one principal's or one session's memory surfacing where it does not belong. Memory is not a transcript log and not a scratchpad; it is an engineered store whose every record carries enough provenance to be trusted, dated, scoped, and — when it has aged past its warrant — distrusted. The distinguishing property of this domain is that its consumer is a reasoning engine that will treat a recalled record as an assertion about the present unless the harness marks it otherwise; memory that cannot be dated or scoped becomes indistinguishable from ground truth at the point of recall.

## 6.2 Required artifacts

- **AHES-ME-A1** — A **memory schema** defining, for each memory store the harness maintains: the record fields, the scope of the store (session-local vs. cross-session durable; per-principal vs. shared), and the provenance fields every record carries (source, author identity, write timestamp). Owned by the harness maintainer; versioned under the configuration baseline (→ §15) so a record's schema at time of write is reconstructible.
- **AHES-ME-A2** — A **retention and expiration policy** stating, per store, how long records live, how staleness is signaled, and how expired or superseded records are removed or marked. Owned by the harness maintainer; change-controlled.
- **AHES-ME-A3** — An **isolation model** declaring the boundaries between principals, between sessions, and between memory stores of differing trust, and the mechanism that enforces each boundary. Owned jointly with the security architecture owner (→ §14) and the principal-authority owner (→ §9); changes reviewed against both.

## 6.3 Technical controls

- **AHES-ME-01** — Every durable memory record **shall** carry provenance: its source, the identity under which it was written (→ §9), and a write timestamp. A record whose origin and age cannot be established **shall not** be admitted to a durable store.
- **AHES-ME-02** — Writes to a durable memory store **shall** be validated against the memory schema (ME-A1); a write that does not conform **shall** be rejected rather than silently coerced, so that the store's shape remains an engineered invariant rather than an emergent one.
- **AHES-ME-03** — Recalled memory **shall** be presented to the model as dated, attributed recall — not as current ground truth — so that the judgment layer (→ §7) can weigh a record's age against present state. Where a recalled record conflicts with directly observable current state, the harness's convention **shall** be that current state wins; this convention **shall** be stated, not left implicit.
- **AHES-ME-04** — Durable records **shall** be subject to the retention and expiration policy (ME-A2): each store has a defined lifetime or supersession mechanism, and expired or superseded records **shall** be removed or explicitly marked stale rather than persisting indefinitely as apparent fact.
- **AHES-ME-05** — The harness **shall** structurally resist writing secret material (credentials, tokens, private keys, raw inline secret assignments) into durable memory. Secrets belong in credential stores; memory **shall** hold pointers, not copies. Where structural interception is not yet in place, this **shall** be enforced as a flagged (soft-stop) write (→ §7) with the gap documented.
- **AHES-ME-06** — Memory stores **shall** enforce the isolation model (ME-A3): a record written under one principal **shall not** surface to a session acting under a different principal, and a session-local store **shall not** leak into a durable cross-session store without an explicit, attributable promotion step. Cross-principal and cross-session isolation are structural boundaries, not conventions the model is trusted to observe (→ §14).
- **AHES-ME-07** — Promotion of a record from a transient scope to a durable scope **shall** be an explicit, attributable operation, not an automatic consequence of a record having been mentioned. The distinction between "seen this session" and "committed to durable memory" **shall** be represented in the store.
- **AHES-ME-08** — The harness **should** distill rather than accumulate: durable memory intended for frequent recall **should** be bounded and curated, with raw unbounded history held in a separate, rarely-recalled store, so that the recall surface does not degrade into an unsearchable transcript. Where a single unbounded store is used, the rationale **shall** be documented.
- **AHES-ME-09** — The load discipline for pinned, always-current memory (identity, policy, standing constraints) **shall not** depend on recall retrieval: content that must be present every session **shall** be loaded structurally at session start and **shall not** be subject to the same expiration or compaction as ordinary recall (→ §2 context pinning, → §7 judgment-layer loading).

## 6.4 Verification criteria

| Control | Method |
|---|---|
| ME-01 | Inspection of the schema for required provenance fields; test that a record lacking origin or timestamp is refused at write. |
| ME-02 | Test: attempt a schema-nonconforming write; verify rejection rather than coercion. Analysis of the validation path. |
| ME-03 | **Adversarial test**: seed a store with a record known to be stale, then present the model a task where current state differs; verify the harness marks the recall as dated and that current state is preferred. Inspection that the "current wins" convention is stated. |
| ME-04 | Test: age or supersede a record past its policy lifetime; verify removal or stale-marking. Inspection of the retention policy. |
| ME-05 | **Adversarial test**: attempt to write credential-shaped content to a durable store (directly and via indirection); verify structural block or flagged interception; inspect documentation of any gap. |
| ME-06 | **Adversarial test**: write under principal A, attempt recall under principal B and across a session boundary; verify no cross-principal or cross-session leakage. Analysis of the isolation mechanism (→ §14). |
| ME-07 | Test: mention a record transiently and confirm it is not durably present absent an explicit promotion step; inspect that promotion is attributable. |
| ME-08 | Inspection of store partitioning (curated recall vs. raw history) or of the documented single-store rationale. |
| ME-09 | Demonstration: pinned memory loads at session start independent of recall retrieval and survives a context boundary (→ §2). |

## 6.5 Evidence requirements

The operating harness **shall** capture, for durable memory activity: every promotion of a record to durable scope (record identity, author identity, timestamp), every expiration or stale-marking, and every rejected write (schema-nonconforming or secret-shaped). This is sufficient to answer, post-hoc: *what was committed to durable memory, under whose authority, when, what was expired, and what was refused*. Memory-mutation evidence flows into the §10 evidence architecture; retention follows the configuration-baseline retention rule (→ §15).

## 6.6 Informative examples

**thinx (Claude Code deployment, 2026).** A layered memory realization: a bounded, curated durable layer intended for frequent recall (ME-A2, ME-08) sits over a separate, rarely-read raw-transcript store, so the recall surface stays searchable; provenance-bearing distilled notes carry their own dating and origin (ME-01); a stated convention holds that directly observed current state overrides recalled fact when the two conflict (ME-03), which is the memory-side complement of the judgment layer weighing stale recall in §7; a soft-stop discipline keeps secret material out of durable memory, with pointers rather than copies (ME-05); and the identity/policy surfaces that must be present every session load structurally at session start rather than through recall retrieval (ME-09). The underlying reference architecture is MxM (multi-mode meta-harness).

**General pattern.** ME-03's adversarial framing follows from the domain's threat model: the memory consumer is a reasoning engine that will read a recalled record as a present-tense claim unless the harness dates and attributes it, so staleness handling that depends on the model spontaneously noticing an age gap is judgment-layer, not structural, and cannot discharge the requirement on its own.
