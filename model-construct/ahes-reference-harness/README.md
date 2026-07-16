# Model Construct — AHES Reference Harness (synthetic composite)

**Status: v0.1. This is not a citation of any real deployment — read this note before reading anything else in this directory.**

## What this is

Every other entry in `model-construct/` maps a real, private deployment onto AHES clauses. This entry is different in kind: it is a **synthesized, composite reference design**, organized around **MxM** — the standard's own public reference architecture (see the top-level README's Provenance section) — and assembled by generalizing architectural *patterns* observed across this standard's other exemplars (thinx, grok-console, mxh-p, thinx-codex) plus ordinary distributed-systems and security-engineering practice, with no implementation detail specific to any one *deployment's realization* of MxM reproduced. Nothing here names a real file path, schema field, script, or private-repo-specific term from any source system. Where a mechanism is described, it is described the way a textbook or a standard's own reference architecture would describe it — generically enough to be implemented independently by someone who has never seen any of the source systems.

**MxM itself is not the thing being protected here — it's already public.** The construct (Mission/Mind/Morals/Memory/Methods/Means, each grounded in a distinct philosophical domain) is published: a public repository, a published article, and this standard's own cited theoretical basis. Using MxM's real vocabulary to structure this design is what makes it recognizably *ours* rather than generic ISO-standard prose — see [`architecture.md`](architecture.md) §1. The line this entry actually holds is narrower and specific: not *any particular deployment's implementation* of MxM, and not `ologos-repos/Hermit` at all.

**Why build this instead of just citing more real deployments.** The other four exemplars are each partial — thinx covers the most domains but is the standard's own author's harness (a named bias); grok-console, mxh-p, and thinx-codex each cover a handful. None of them, individually, shows what a harness claiming broad AHES conformance actually looks like end to end, as one coherent system. A synthetic composite can do that safely: because it isn't a description of any real system, it can be as complete and as specific as pedagogical value warrants without becoming a disclosure question.

## IP-safety discipline (binding for this entry)

- **MxM's own construct (Mission/Mind/Morals/Memory/Methods/Means) is public and used freely.** It is this standard's own documented theoretical basis, not a trade secret — see the distinction drawn above.
- **No implementation specifics from any private deployment.** No file paths, exact schema field names, script names, internal rule-ID labels, or product/package names from thinx, grok-console, mxh-p, or thinx-codex's own private repos.
- **No research into, or description of, `ologos-repos/Hermit`.** Hermit (a swarm/multi-worker coordination package referenced only obliquely elsewhere in this standard's research) was deliberately not read for this entry. Anywhere this design touches multi-worker coordination or workflow orchestration (§5), the description uses only generic, textbook distributed-systems vocabulary (work queues, idempotency keys, compensating actions, checkpoints) — not anything specific to Hermit's actual design, which the authors of this entry do not have detailed knowledge of and did not seek out.
- **Deployment-specific branded pattern names stay generic; MxM's own surface names do not.** Where a *private deployment* has a specific branded pattern (e.g., a named claim-ledger discipline, a named coordination pattern), this entry describes the *underlying mechanism class* in plain engineering language and, where useful, names the real exemplar that demonstrates it (e.g., pointing to `model-construct/mxh-p/` for the claim-verification mechanism) rather than inventing or alluding to the private system's own branding.
- **Every mechanism traces to an AHES requirement, not to a deployment.** The domain-realization tables cite `AHES-<domain>-<nn>` requirement IDs — the standard's own normative text, which is not proprietary to anyone — rather than "this is how [deployment] does it."

## Domain coverage

Unlike the other exemplars, this entry aims at genuine breadth: all sixteen Part 2 domains, because that's the only way to show a *complete* conformance picture in one place.

| § | Domain | Covered |
|---|---|---|
| 1 | Model mediation | Yes |
| 2 | Context engineering | Yes |
| 3 | Tool engineering | Yes |
| 4 | Agent engineering | Yes |
| 5 | Workflow engineering | Yes (generic distributed-systems vocabulary only — see IP-safety note above) |
| 6 | Memory engineering | Yes |
| 7 | Policy enforcement | Yes — synthesizes both structural mechanisms this standard's own §7 clause documents (wrap-and-intercept, configure-the-native-gate) as complementary, not competing |
| 8 | Human authority | Yes |
| 9 | Principal & authority model | Yes |
| 10 | Evidence engineering | Yes |
| 11 | Evaluation | Yes |
| 12 | Operational assurance | Yes |
| 13 | Supply-chain control | Yes |
| 14 | Security architecture | Yes |
| 15 | Configuration baseline | Yes |
| 16 | Substrate portability | Yes |

## Contents

- [`architecture.md`](architecture.md) — the composite design: layer structure and a request-lifecycle walkthrough
- [`domain-realizations.md`](domain-realizations.md) — per-domain mapping, all sixteen, each mechanism traced to specific AHES requirement IDs

## What this is not

Not a claim that any real harness (including thinx) actually implements all of this. Not a minimum bar — a harness can conform at L1 or L2 for a subset of domains and be a perfectly legitimate AHES deployment (per Part 5's applicability-matrix and inapplicability-recording rules). This is a ceiling sketch, not a floor: what broad, deliberate conformance could look like, assembled from proven pattern classes, generalized past the point of being anyone's IP.
