# Model Construct — thinx-codex

**Subject:** `ologos-repos/thinx-codex` (private, canonical development repository — not one of its deployment snapshots or host-profile repos) — a Codex-native governed agent workspace and console, sibling to thinx-Claude, expressing governance through the same MxM (multi-mode meta-harness) construct shape.

**Substrate:** OpenAI Codex — a distinct model vendor from thinx's Anthropic substrate, sharing thinx's MxM construct philosophy but not its model provider. The mechanism documented here is itself substrate-independent regardless: it operates on *authored governance artifacts*, not on model behavior at runtime.

**Why this exemplar.** Every other entry in this directory (grok-console, mxh-p) demonstrates a *runtime* enforcement mechanism — a policy gate, an evidence writer, a portability envelope injected at spawn. thinx-codex is the only exemplar in the standard that demonstrates **automated, CI-enforced structural conformance checking**: a family of gate scripts, run on every change through continuous integration, that read the harness's own authored strategy/requirements/architecture/verification artifacts and *fail the build* when their structural integrity rules are violated — a fail-closed control that blocks a non-conforming change from merging, not a policy document describing what conformance would mean.

That is exactly the gap Part 5 identifies. AHES §5.5 specifies an assessment procedure — scoping, applicability matrix, requirement selection, evidence sampling, disposition — but has, until now, no worked example of *automated tooling* that could realize any of it. thinx-codex's gate machinery is a real, running instance of the pattern: structural rules over a traceable requirement graph, evaluated mechanically, with a hard/advisory split that mirrors AHES's own L-level and `shall`/`should` structure. Its primary contribution is therefore to **Part 5 (Conformance & assessment)**, not to any single Part 2 domain.

## Primary contribution: Part 5, not a Part 2 domain

| AHES surface | What thinx-codex demonstrates |
|---|---|
| **Part 5 §5.5 assessment procedure** (primary) | Requirement selection and disposition realized as executable, CI-wired checks over an authored trace graph — the first worked example of automated tooling that could carry §5.5's procedure rather than describe it in prose. See [`part5-conformance.md`](part5-conformance.md). |
| **Part 5 §5.2 level structure** | A fail-closed / fail-open rule split that is a direct architectural precedent for the L1-documented / L2-enforced / L3-verifiable ladder and the `shall` / `should` distinction of Part 0.2 |
| Part 0.2 traceability (`AHES-<domain>-<nn>`) | A `<KIND>-<AREA>-<NNNN>` trace-ID convention over a fixed, small vocabulary of artifact kinds — an independent instance of the exact ID discipline AHES assumes for its own requirements |
| §5 Workflow / §11 Evaluation (secondary, not mapped) | A per-concern gate family (one gate per governance layer) plus a self-referential meta-gate that verifies every gate is actually wired and documented — noted as adjacent, not worked in this pass |

## Access note

`thinx-codex` is a private repository, treated with the same disclosure discipline as the other entries here (see the directory [`README.md`](../README.md), "A note on disclosure"). The mappings describe architectural patterns and design decisions — the *kind* of mechanism and why it is a meaningfully different data point — without reproducing implementation specifics: no verbatim rule text, no exact schema field names, no internal rule-ID labels, and no exact script filenames beyond naming the governance concern each gate covers. Ask the repo owner before citing anything more specific.

## Domains not attempted

The sixteen Part 2 domains are not mapped in this pass. thinx-codex shares near-identical construct shape with thinx-Claude, so a Part 2 write-up would largely restate the existing thinx-based §4/§7/§10 examples rather than test the standard against a different design — which is precisely why the exemplar was reserved for Part 5, where it *is* a genuinely new data point. The secondary §5/§11 adjacency in the table above is named, not claimed.
