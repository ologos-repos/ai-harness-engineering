# AHES Part 4 — Risk-to-Control Mapping

**Status: Skeleton — risk register framed; per-risk control traces to be drafted.**

## 4.1 Purpose

ISO/IEC 23894 and the NIST AI RMF structure AI risk but prescribe no technical architecture to mitigate it. This part is the traceability bridge: each harness-relevant risk maps to the Part 2 controls that mitigate it, so a risk assessment can cite enforceable requirements rather than narrative intent. The mapping is bidirectional — every Part 2 `shall` traces to at least one risk, and every register risk traces to controls or to an explicitly accepted residual.

## 4.2 Harness risk register (framing draft)

| Risk | Primary Part 2 domains | AI RMF function |
|---|---|---|
| Prompt injection (direct and via retrieved/tool content) | §2, §3, §7 | Manage |
| Excessive agency / unbounded delegation | §4, §8, §9 | Govern, Manage |
| Approval bypass | §7, §8 | Govern |
| Hallucinated actions (fabricated targets, invented parameters) | §3, §7, §11 | Measure, Manage |
| Uncontrolled propagation (agent-spawned work exceeding mandate) | §4, §5 | Manage |
| Data disclosure (credentials or sensitive data into context/memory/logs) | §2, §6, §14 | Manage |
| Unreliable retrieval treated as ground truth | §2, §10 | Measure |
| Memory poisoning / stale-memory action | §6 | Manage |
| Model drift and silent regression | §1, §11, §12 | Measure |
| Model selection/substitution risk | §1, §16 | Map, Manage |
| Supply-chain compromise (models, prompts, tools, packages, connectors) | §13 | Map, Manage |
| Insufficient evidence (unreconstructible incidents) | §10, §15 | Govern |
| Identity confusion (agent acting under wrong/no principal authority) | §9, §14 | Govern |
| Context-boundary loss of governing instructions (compaction, truncation) | §2, §7 | Manage |

## 4.3 To be drafted

Per-risk entries with: risk statement, affected quality characteristics (Part 3), mitigating requirement IDs, verification pointers (Part 2 §N.4), residual statement, and acceptance authority (→ §8/§9).
