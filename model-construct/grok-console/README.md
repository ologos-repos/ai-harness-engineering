# Model Construct — grok-console

**Subject:** `ologos-repos/grok-console` (private) — **ologos-grok**, Ologos Corp's governed instance of xAI's Grok Build CLI, wrapped in a local-first super-admin web UI (Keycloak OIDC, GitHub OAuth, job-queue streaming, session persistence).

**Substrate:** xAI Grok, via the Grok Build CLI — independent of the Anthropic-substrate exemplars (thinx, thinx-codex) cited elsewhere in this standard. Governance is expressed through the same MxM construct shape (`construct/mission.md`, `mind.md`, `morals.md`, `methods.md`, `memory.md`, `means/`) but every enforcement mechanism is a distinct implementation.

**Why this exemplar.** AHES's controls are only credible as substrate-agnostic if they hold on a harness that wasn't designed with AHES (or thinx's own patterns) in mind. grok-console independently arrived at: a deny-glob safety catalog instead of a hook dispatcher, a schema-first evidence architecture, explicit per-agent-role contracts, and — most significantly — a *vendored, versioned, generic portability package* (`ologos-repos/mxh-p`) rather than a bespoke governance-porting effort. That last point does real work for §16: portability stops being an aspiration once a real package exists that multiple independent harnesses `pip install`.

## Domains realized

| AHES domain | Source path(s) | Summary |
|---|---|---|
| §4 Agent engineering | `construct/means/agents/{coder,evaluator,generator,planner,reviewer,verifier}/AGENT.md` | Six agent roles, each a contract: access level, scope boundary, done-criteria, escalation rule |
| §7 Policy enforcement | `construct/means/safety/{irreversible_rules.json,approval_classes.yaml,enforce.py}` | Deny-glob catalog fed into the CLI's native `--deny`, with exit-code contract (0/1/2) and a separate CONFIRM-gate script for mid-turn high-blast commands the CLI can't deny natively |
| §10 Evidence engineering | `construct/means/observability/{evidence-record.schema.json,session-record.schema.json,record.py}` | JSON-Schema-defined, append-only evidence records with a fixed `event_type` enum and `authority_class` binding to the §7 approval-class vocabulary |
| §16 Substrate portability | `construct/means/mxh_p/` (vendored from `ologos-repos/mxh-p`) | A generic "orientation packet" — an MxM-shaped envelope (mission/mind/morals/methods/memory/means) injected at every delegated worker/role spawn — vendored by explicit version pin, not soft-linked |

## Access note

`grok-console` is a private repository. The mappings below paraphrase structure (schema shapes, file roles, control flow) rather than reproduce full proprietary source; enough is quoted to make each mapping independently checkable by someone with repo access.

## Domains not attempted

The remaining twelve AHES domains (§1–3, §5–6, §8–9, §11–15) were not mapped in this pass — either because this review didn't go deep enough into the relevant source paths to ground an honest claim, or because the source repo's realization there isn't materially different from the thinx-based examples already in the standard. Not a claim that grok-console lacks them.
