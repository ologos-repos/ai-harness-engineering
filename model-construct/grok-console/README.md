# Model Construct — grok-console

**Subject:** `ologos-repos/grok-console` (private) — **ologos-grok**, Ologos Corp's governed instance of xAI's Grok Build CLI, wrapped in a local-first super-admin web UI.

**Substrate:** xAI Grok, via the Grok Build CLI — independent of the Anthropic-substrate exemplars (thinx, thinx-codex) cited elsewhere in this standard. Governance is expressed through the same MxM construct shape (mission / mind / morals / methods / memory / means) as the other exemplars, but every enforcement mechanism is a distinct implementation.

**Why this exemplar.** AHES's controls are only credible as substrate-agnostic if they hold on a harness that wasn't designed with AHES (or thinx's own patterns) in mind. grok-console independently arrived at: a deny-pattern safety catalog instead of a hook dispatcher, a schema-first evidence architecture, explicit per-agent-role contracts, and — most significantly — a *versioned, generic portability package* rather than a bespoke governance-porting effort. That last point does real work for §16: portability stops being an aspiration once a real package exists that more than one independent harness depends on.

## Domains realized

| AHES domain | What's demonstrated |
|---|---|
| §4 Agent engineering | Several agent roles, each a standalone contract naming identity, permitted access level, scope boundary, and completion/escalation criteria |
| §7 Policy enforcement | A declarative deny-pattern catalog consumed by the model CLI's own native permission engine, with a three-tier outcome (allow / confirm / deny) rather than a binary allow/hard-stop split |
| §10 Evidence engineering | Schema-validated, append-only evidence records with a closed vocabulary of event types and a binding to the harness's own authority-class taxonomy |
| §16 Substrate portability | A generic, versioned "orientation packet" — a governance-shaped envelope injected at every delegated worker/role spawn — consumed by explicit version pin, not soft-linked |

## Access note

`grok-console` is a private repository. The mappings below describe architectural patterns and design decisions — not exact file paths, field names, or verbatim policy text. Enough is described to make each mapping independently evaluable by someone with repo access, without reproducing the repo's implementation.

## Domains not attempted

The remaining twelve AHES domains (§1–3, §5–6, §8–9, §11–15) were not mapped in this pass — either because this review didn't go deep enough into the relevant source to ground an honest claim, or because the source repo's realization there isn't materially different from the thinx-based examples already in the standard. Not a claim that grok-console lacks them.
