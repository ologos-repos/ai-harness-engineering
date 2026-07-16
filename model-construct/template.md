# Model Construct — Realization Entry Template

Copy this structure for a new exemplar. One file per AHES domain realized; do not attempt domains you have not actually inspected in the source repository — an absent mapping is honest, a fabricated one is not.

---

## Entry `README.md`

- **Subject.** Repository, deployment context, model/CLI substrate.
- **Why this exemplar.** What it adds that existing exemplars don't (different substrate, different technical mechanism, different scale).
- **Domains realized.** Table: AHES domain → source path(s) → one-line summary.
- **Access.** Public/private; if private, note that citations here are paraphrase + structure, not verbatim reproduction of proprietary content, unless explicitly cleared.

## Per-domain file `<NN>-<domain-slug>.md`

### Mapping

Table: AHES requirement ID (where the standard already has one) or clause reference → concrete artifact (file path, schema field, script behavior) → how it satisfies (or partially satisfies, or diverges from) the clause.

### What's different from other exemplars

If this domain is already realized elsewhere in `model-construct/` or inline in `standard/part-2-domains/`, say explicitly what's the same and what differs. A second exemplar earns its place by adding a genuinely different data point, not by restating the first.

### Gaps

Where the source repo's realization is partial, aspirational-but-not-yet-built, or diverges from what the clause requires. Cite the source's own gap-tracking if it has one (e.g., a `GAP-*` or `TODO` marker) rather than inferring gaps from silence.
