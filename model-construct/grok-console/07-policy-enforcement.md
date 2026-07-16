# Model Construct — grok-console — §7 Policy Enforcement (alternate realization)

This is a **second data point** for the domain already worked in [`standard/part-2-domains/07-policy-enforcement.md`](../../standard/part-2-domains/07-policy-enforcement.md) §7.6, which uses thinx as its informative example. Read that clause first — this entry maps the same controls onto a structurally different mechanism.

## Mapping

| AHES-PE requirement | thinx realization (existing §7.6) | grok-console realization |
|---|---|---|
| PE-A1 policy document | `morals.md` — hard-stops / soft-stops / trust scope | `construct/morals.md` — Prohibitions (P1–P18) / Obligations / Permissions, with an explicit **conflict-priority rule**: *"Prohibitions > Obligations > Permissions. When two obligations conflict, the one with greater irreversibility risk wins — pause and alert the human rather than resolving silently."* |
| PE-A2 enforcement-point inventory | `.claude/hooks/preflight.py` dispatcher, function-per-check | `construct/means/safety/irreversible_rules.json` — a **declarative pattern catalog** (regex/glob patterns + `cli_deny_globs`), not dispatcher code |
| PE-02 structural hard-stop | `PreToolUse` hook intercepts before execution | The catalog's `cli_deny_globs` are loaded into the Grok CLI's own **native `--deny` flag** (`app/config.py` → CLI invocation) — enforcement lives in the CLI's own permission engine, not a wrapping hook. A structurally different mechanism achieving the same PE-02 property: the model's own reasoning cannot execute a denied pattern regardless of what it argues for. |
| PE-04 override, attributable | `# CHIEF-ARCHITECT-OVERRIDE:` marker + append-only log | Deliberately **absent by design** for the hard-deny tier: *"Override path is human-mediated — not a flag in this module."* For the CONFIRM tier (§ below), authorization is a `# MXM-CONFIRM: reason=…; approved_by=…` marker — structurally similar to thinx's override marker, but scoped to a different enforcement class |
| — (three-tier extension not named in the base AHES-PE set) | thinx: binary allow / hard-stop, with soft-stop as a judgment-layer convention | grok-console: **explicit three-way exit-code contract** for `enforce.py` — `0` allow, `1` confirm, `2` deny. CONFIRM is a first-class enforcement tier, not merely "flag and proceed" prose — commands in this class are *not* CLI-deniable mid-turn (the CLI's native deny is turn-boundary, not mid-turn), so a separate `confirm_gate.py` script is the structural checkpoint |
| Evidence binding (→ §10) | Audit log at `~/.claude/chief-architect-override.log` | `--evidence --session-id <id>` flag on `enforce.py`/`confirm_gate.py` writes into the §10 evidence JSONL, with `authority_class` in the evidence schema keyed to `approval_classes.yaml` |

## What's different from other exemplars

The load-bearing difference: thinx's enforcement point is **harness-authored code** (`preflight.py`) that intercepts tool calls before the model's chosen CLI ever sees them. grok-console's is a **catalog consumed by the model's own CLI's native permission system** — the harness doesn't intercept, it configures the substrate's built-in gate. Both discharge AHES-PE-02 (structural, not model-reasoning-dependent), but they demonstrate the requirement is achievable through two architecturally opposite paths: wrap-and-intercept vs. configure-the-native-gate. A standard whose PE-02 wording only fits one of these patterns would be over-fit to thinx; this exemplar is the check that it isn't.

grok-console also names a control neither the base clause nor thinx's realization states explicitly: a **documented conflict-priority rule** for competing obligations (irreversibility-risk tiebreak, pause-and-alert as the default on unresolved conflict). This is arguably a gap in the current `standard/part-2-domains/07-policy-enforcement.md` requirement set — worth a follow-up requirement (`AHES-PE-10`?) once this exemplar's pattern is reviewed against others.

## Gaps

- The CONFIRM tier's mid-turn limitation is disclosed by the source repo itself as an open item (`GAP-GC-0002`, per `means/safety/README.md`): *"CONFIRM mid-turn block still open."* Cited here rather than inferred — the source is honest that PE-02-equivalent structural enforcement is not yet complete for this tier.
- `approval_classes.yaml`'s `host_admin` posture documents that human approval *should* include `reason`, `blast_radius`, `rollback`, `approved_by` fields, but notes this is "policy; not yet a mechanical marker gate" — i.e., PE-04's attributability requirement is currently a documented convention for this specific class, not yet structurally enforced the way the deny tier is.
