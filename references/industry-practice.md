# Industry Practice and Prior Art — "Harness Engineering" Discourse

**Status: v0.1, compiled 2026-07-16. Informative — this document positions AHES relative to existing practitioner literature; it adds no normative content.**

## Why this document exists

AHES's own README states that no *published standard* specifies the engineering of the AI harness as a discipline. That claim is about formal standards bodies (ISO, IEC, IEEE, NIST) and remains accurate. It is a different claim from "no one has written about this" — a substantial, active body of **practitioner and industry literature** already uses the term "harness engineering" in a sense close to AHES's own, predating AHES by months. This document names that literature, attributes it properly, and shows where its vocabulary and AHES's sixteen domains converge — which is evidence *for* AHES's domain taxonomy (independent parties arriving at similar categories), not a competing claim to have invented the space.

## Source protocol note

Per this standard's own reasoning canon (source protocol: primary-source priority, citation integrity, confidence signaling), every source below is labeled **HIGH** (primary text fetched and read directly), **MEDIUM** (corroborated across multiple independent secondary characterizations but primary text not accessed — paywalled or bot-gated), or **UNCERTAIN** (a single secondary characterization, author attribution not independently confirmed). Nothing below is cited above the confidence it was actually verified at.

## Sources

| # | Source | Author(s) | Publication | Date | Confidence |
|---|---|---|---|---|---|
| 1 | "Harness Engineering for Coding Agents" | Birgitta Böckeler | Martin Fowler's blog | 2026-04-02 | **HIGH** |
| 2 | "The Anatomy of an Agent Harness" | Vivek Trivedy | LangChain blog | 2026-03-10 | **HIGH** |
| 3 | "Agent Harness Engineering" | Addy Osmani | addyosmani.com | 2026-04-19 | **HIGH** |
| 4 | "Effective Harnesses for Long-Running Agents" | Justin Young | Anthropic Engineering | 2025-11-26 | **HIGH** |
| 5 | "Harness Design for Long-Running Application Development" | Prithvi Rajasekaran | Anthropic Labs | 2026-03-24 | **HIGH** |
| 6 | "Harness, Scaffold, and the AI Agent Terms Worth Getting Right" | Sergio Paniego, Aritra Roy Gosthipaty | Hugging Face blog | 2026-05-25 | **HIGH** |
| 7 | `awesome-harness-engineering` (curated resource list) | ai-boost (GitHub org) | GitHub | ongoing | **HIGH** (structure fetched directly) |
| 8 | "Harness engineering: leveraging Codex in an agent-first world" | OpenAI (no individual byline found) | openai.com | ~2026-02 | **MEDIUM** — direct fetch returned HTTP 403 (bot-gated); characterization corroborated across multiple independent secondary sources (InfoQ, several practitioner blogs quoting it directly) that substantially agree with each other |
| 9 | "Agent Harness Engineering: A Survey" (proposes an "ETCLOVG" seven-layer taxonomy; 110+ papers, 23 systems surveyed) | Attribution uncertain — one secondary source names "Picrew et al." but this is **not independently confirmed** and may be a search-summarization artifact | Submitted to TMLR per one secondary source; hosted on OpenReview | 2026 | **UNCERTAIN** — direct fetch blocked by bot-verification wall; exists (confirmed via two independent companion GitHub repos citing it), taxonomy name and scope corroborated, but author list and exact venue status not primary-verified. Do not cite the author name elsewhere until verified directly. |
| 10 | "AI Harness Engineering: A Runtime Substrate for Foundation-Model Software Agents" (arXiv:2605.13357) — uses AHES's near-exact phrase, ~2 months before this repo existed | Author attribution surfaced in search but **not primary-verified** (abstract page not fetched directly) | arXiv preprint | ~2026-05 | **UNCERTAIN** — the arXiv ID and title were returned by search; the abstract page was not fetched directly, so authors and exact framing are unconfirmed. Named here as the closest prior use of the *phrase* "AI harness engineering," relevant to naming/priority; a full read is open work. It is (per its title) a runtime-substrate/architecture paper, not a normative standard — consistent with all other sources here. |

Also identified but not researched in depth for this pass (named in source #7's "Foundations" category as canonical, alongside sources above): additional IBM and Google essays on harness/scaffolding engineering, and Google's Agent Development Kit (ADK) 2.0 as a shipping harness-infrastructure product. Flagged for a future pass, not characterized here to avoid citing what wasn't actually read.

## Naming and prior use of the term

The phrase "harness engineering" is generic software-engineering vocabulary ("test harness" predates all of this by decades) and is used broadly and un-proprietarily across every source above. Two adjacent name-space facts, recorded for completeness:

- **Harness Inc. (harness.io)** holds the registered mark **HARNESS®** for its CI/CD product line, and bans third-party marks that use HARNESS as the salient element with a minor modifier (their examples: "MyHARNESS," "HARNESS Tools," "HARNESS Software"). "AI Harness Engineering Standard" does not fit that pattern — "harness" is a descriptive term inside a long generic standards-style name, not a branded element — so the collision risk is low. (Their policy separately bars registering a *domain* containing their marks; irrelevant to a repository name, but worth remembering if a dedicated domain is ever wanted.) Harness Inc. does **not** claim origination of "harness engineering" as a discipline term; their marks are product names.
- **Source #10 above** (arXiv:2605.13357) is the closest located prior use of the specific phrase "AI harness engineering," predating this repository. It is a runtime-substrate paper, not a competing standard.

Neither fact indicates a naming problem for AHES, but both are recorded so the naming decision rests on evidence rather than assumption. The distinctive scope claim below (the meta-harness level) is a stronger differentiator than the name in any case.

## What each source contributes

**Böckeler (source 1)** proposes the cleanest control-theoretic vocabulary in this literature: **guides** (feedforward — steer behavior *before* action) versus **sensors** (feedback — observe *after* action, enable self-correction), further split into **computational** (deterministic, fast: tests, linters, type checkers) versus **inferential** (semantic, slower: LLM judges, review agents) execution. She also names **harnessability** — a codebase's own amenability to being harnessed, a function of its language/architecture/framework properties — and a continuous **steering loop** where humans iterate controls based on recurring failure patterns.

**Trivedy (source 2)** proposes a five-layer taxonomy: Context & Storage, Execution & Tools, Orchestration & Logic, Middleware & Control, Knowledge & Observation.

**Osmani (source 3)** contributes the **ratchet principle** — "every line in a good AGENTS.md should be traceable back to a specific thing that went wrong," i.e., constraints exist only because failures justified them, not preemptively — and names **model-harness co-evolution** (harnesses don't shrink as models improve, they shift: obsolete scaffolding is removed, new scaffolding appears for newly-unlocked failure modes) and **Harness-as-a-Service (HaaS)**, the industry trend of building on pre-factored harness frameworks (Claude Agent SDK, Codex SDK) rather than raw model APIs.

**Young (source 4)**, from Anthropic, documents a concrete pattern: an **initializer agent** (sets up environment once) paired with a **coding agent** (incremental progress across sessions), governed by a JSON feature list with binary pass/fail status and git-based state handoff between sessions.

**Rajasekaran (source 5)**, also from Anthropic, documents a **generator-evaluator architecture** — explicitly modeled on GANs, separating the agent doing work from the agent judging it, because self-evaluation is biased toward praising one's own output — plus **context resets vs. compaction** (a clean slate beats summarization for combating what he calls "context anxiety") and **sprint contracts**: negotiated, specific success criteria fixed before implementation begins.

**Paniego & Gosthipaty (source 6)** draw a narrower distinction than most other sources: **scaffolding** is the behavior-*defining* infrastructure (prompts, tool descriptions, context management); **harness** is the execution *layer* that runs the loop (calls the model, handles tool calls, decides when to stop). They explicitly note the field's terminology is still fluid and lacks universally accepted definitions.

**`awesome-harness-engineering` (source 7)** organizes the field into twelve design-primitive categories: Agent Loop, Planning & Task Decomposition, Context Delivery & Compaction, Tool Design, Skills & MCP, Permissions & Authorization, Memory & State, Task Runners & Orchestration, Verification & CI Integration, Observability & Tracing, Debugging & DX, Human-in-the-Loop.

**OpenAI (source 8, MEDIUM confidence)** is, per the corroborated secondary record, the originating high-profile use of "harness engineering" in this exact sense (~February 2026), describing a real project where every line of a shipped codebase — application logic, tests, CI, docs, observability, tooling — was written by their Codex agent under the operating principle "humans steer, agents execute." Reported concepts include strict layered-architecture enforcement with custom linters, and browser/runtime observability integration with concrete performance thresholds.

**The ETCLOVG survey (source 9, UNCERTAIN confidence)** reportedly proposes a seven-layer taxonomy — Execution, Tooling, Context, Lifecycle, Observability, Verification, Governance — over 110+ papers and 23 analyzed systems. If the author/venue attribution is later confirmed, this would be the closest existing work to AHES's own ambition (an exhaustive, named-category treatment of the harness as engineering discipline) and deserves a full direct read, not just this provisional note.

## Convergence with AHES's own domains

None of the above proposes anything resembling AHES's normative apparatus — required artifacts, numbered `shall` controls, verification criteria, conformance levels. All of it is descriptive/prescriptive practitioner writing, not a specification an assessor could check a harness against. That gap is exactly AHES's contribution. But the *domain boundaries* these sources independently drew line up closely with AHES's sixteen domains — worth naming as convergent evidence, not coincidence:

| Practitioner concept (source) | Closest AHES domain(s) |
|---|---|
| Guides/feedforward (1); Middleware & Control layer (2) | §7 Policy enforcement, §2 Context engineering |
| Sensors/feedback, computational vs. inferential verification (1) | §11 Evaluation |
| Context & Storage layer (2); context resets vs. compaction (5) | §2 Context engineering, §6 Memory engineering |
| Execution & Tools layer (2); Tool Design (7) | §1 Model mediation, §3 Tool engineering |
| Orchestration & Logic layer (2); Task Runners & Orchestration (7) | §4 Agent engineering, §5 Workflow engineering |
| Knowledge & Observation layer (2); Observability & Tracing (7) | §10 Evidence engineering, §12 Operational assurance |
| Permissions & Authorization (7) | §7 Policy enforcement, §8 Human authority, §9 Principal & authority model |
| Memory & State (7) | §6 Memory engineering |
| Verification & CI Integration (7); sprint contracts, feature-list pass/fail (4, 5) | §11 Evaluation |
| Human-in-the-Loop (7) | §8 Human authority |
| Skills & MCP (7); Harness-as-a-Service (3) | §3 Tool engineering, §16 Substrate portability |
| Ratchet principle (3); OA-09's incident-feeds-evaluation loop (already in AHES) | §12 Operational assurance, Part 1 §1.2.8 (evaluation-regime change control) |
| Model-harness co-evolution (3) | §1 Model mediation, Part 1 §1.2.2 (model substitution qualification) |
| ETCLOVG's "Governance" layer (9, uncertain) | §7, §8, §9, §14 collectively |

**The strongest single convergence point:** Rajasekaran's generator-evaluator architecture (5) and Osmani's planner/generator/evaluator split (3) — both independent of each other and of AHES — describe substantially the same pattern already documented in this standard via the `mxh-p` exemplar (`model-construct/mxh-p/`) and normatively required at `AHES-AE-09` (a distinct verification role checks completed work rather than the generating role self-certifying) and `AHES-EV-01`. Three independent parties (Anthropic, Osmani, and the private exemplar this standard cites) converged on separating "did the work" from "checked the work" as a load-bearing pattern. That is real corroborating evidence the requirement is well-founded, not merely AHES's own preference.

## What AHES actually adds

Two things distinguish AHES from all of the literature above, and they are different in kind.

**1. The normative apparatus.** Reading across all ten sources: this literature is uniformly essayistic — patterns, principles, and taxonomies presented as guidance, with no mechanism for an independent party to check whether a given harness actually conforms to any of it. None defines required artifacts, numbered testable requirements, verification methods per requirement, or conformance levels. AHES's first contribution is not the domain taxonomy (which converges with this literature, per the table above) — it is the normative apparatus laid over that taxonomy: `shall`/`should`/`may` requirements, a fixed verification-criteria discipline, and a three-level conformance structure an assessor could actually apply.

**2. The meta-harness scope.** Every source above addresses building a harness directly over a raw model API — the "Agent = Model + Harness" framing (sources 3, 6) is explicit about this. That is a *single-level* harness: scaffolding wrapping a model. AHES's requirements are written to hold for that case, but the case AHES is most distinctively for is the **meta-harness** (→ Part 0 §0.1, §0.3): a harness whose governed substrate is *itself already a harness* — a coding agent / assistant CLI (Claude Code, OpenAI Codex, a Grok CLI) that already wraps a model with its own context, tools, and agent loop. Governing that is a two-level engineering problem the single-level literature does not address: authority must be delegated *through* the CLI's own governance rather than imposed on a bare model; evidence must compose across two loops, not one; policy must layer over a substrate that already has its own policy surface. Every real-deployment exemplar in this standard's Annex A is a meta-harness, not a single-level harness — which is why the standard's own reference architecture is named a multi-mode **meta**-harness (MxM), with the "meta" load-bearing. The practitioner discourse and AHES are not competing for the same territory: they are mostly one level apart.

This document exists so that both distinctions are made honestly — the normative-apparatus claim and the meta-harness-scope claim — with the practitioner discourse fully credited for the single-level territory it maps well, rather than left unacknowledged or implicitly claimed.
