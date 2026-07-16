# AHES Reference Harness — Architecture

Synthetic composite; see [`README.md`](README.md) for what that means and the IP-safety discipline this document holds to.

## 1. Layer structure

The reference harness has five structural layers, each corresponding to a cluster of AHES domains. Layers are named by function, not by any source system's branding.

| Layer | Function | AHES domains |
|---|---|---|
| **Substrate interface** | Talks to one or more models; abstracts routing, versioning, fallback | §1 |
| **Context assembly** | Builds each model invocation's input; enforces provenance and isolation | §2, §3 |
| **Execution** | Runs agents and multi-step work; holds state across steps | §4, §5, §6 |
| **Governance** | Decides and enforces what's permitted; records what happened; verifies claims | §7, §8, §9, §10, §11, §12 |
| **Supply and configuration** | Manages dependencies, credentials, and the versioned bundle that defines "this deployment" | §13, §14, §15, §16 |

The Governance layer is deliberately not "above" or "outside" the others — every layer calls into it before taking a consequential action, and every layer's output flows through it into the evidence record. This is the standard's own point (Part 2 §7's purpose statement): policy that only lives as advisory context in the Context assembly layer is not enforcement, it's a suggestion the model can misweigh.

## 2. Request lifecycle

A single request — a user or upstream system asking the harness to do something — walks through the layers in this order. Each step names which domain's requirements it discharges.

1. **Session start.** The Governance layer's policy document and enforcement-point inventory are loaded and cross-checked for consistency (→ AHES-PE-07); a self-test confirms the structural enforcement layer is actually wired in, not merely configured (→ AHES-PE-06). The active configuration baseline is resolved and recorded (→ AHES-CB-01 et seq.). A principal-authority context is established for the session — who or what is directing this session, at what altitude, under what grant (→ AHES-PA-01 et seq.).

2. **Context assembly.** The harness builds the model's input: retrieved content, prior conversation state, tool schemas, and standing instructions. Every element carries a provenance tag (→ AHES-CE-01 et seq.) distinguishing trusted (harness-authored) content from untrusted (retrieved, tool-returned, or user-supplied) content — the load-bearing distinction for injection resistance (→ Part 4 R1). Governance-critical content (the policy summary, identity, and standing constraints the model needs to reason correctly) is pinned: reloaded from canonical source at this step regardless of what the prior context window held, so a compaction or truncation event earlier in a long session cannot silently drop it (→ AHES-CE and the Part 4 R14 residual it closes).

3. **Model invocation.** The substrate interface dispatches to the currently-qualified model behind this surface (→ AHES-MM-01 et seq.). The model's output — proposed tool calls, a response, or both — returns tagged with the model identity and version that produced it (→ AHES-EE evidence binding).

4. **Precondition check.** Before any tool call executes, its target and parameters are validated against the tool's declared preconditions: does the referenced target actually exist, does it match its declared type, does the caller (this agent, under this principal) hold the required access level (→ AHES-TE-01 et seq., AHES-AE requirements on scope). This is where a model's confident-but-wrong reference to a nonexistent or mistyped target is caught structurally rather than trusted (→ Part 4 R4).

5. **Policy gate.** The resolved action (not the model's surface phrasing of it) is evaluated against the enforcement-point inventory (→ AHES-PE-02, AHES-PE-03). Three outcomes: **allow** (proceeds), **deny** (hard-stop; the model's reasoning cannot override this regardless of how it argues for the action — → AHES-PE-02), or **confirm** (a flagged action that requires an accountable human decision before proceeding — → AHES-HA requirements on approval classes). Where two obligations genuinely conflict with no stated priority, the default is to pause for human input rather than resolve silently (→ AHES-PE-10). A denial states its rationale and the legitimate alternative path (→ AHES-PE-05).

6. **Execution.** The action runs. If it's one step in a longer multi-step unit of work, its result is checkpointed before the next step begins, so a failure partway through is resumable rather than requiring a full restart, and so partially-applied side effects have a defined compensating path rather than being left inconsistent (→ AHES-WE-01 et seq. — described here in the ordinary distributed-systems sense of checkpoint/resume and compensating actions; see the IP-safety note in `README.md` regarding this domain specifically).

7. **Evidence capture.** Every step above — the context assembled, the model invoked, the precondition result, the policy decision, the execution outcome — is written to an append-only, schema-validated evidence store, each record carrying the configuration baseline it occurred under and the authority class it fell under (→ AHES-EE-01 et seq.). This is not a side effect of the other steps; it is itself a requirement each step above discharges, not an afterthought bolted on at the end.

8. **Completion claim.** The unit of work is not marked done because an agent asserts it's done. A completion claim validates only if the harness can show it went through an actual execution chain — the work was planned, the work was generated, and the work was independently checked — with a distinct, evidenced artifact from each stage. A claim representing only that files exist, without evidence the chain that produced them actually ran, does not validate (→ AHES-EV-01 and the claim-verification pattern documented at the standard's own abstraction level in this standard's §11 clause and in `model-construct/mxh-p/`). This is the harness's structural answer to unverified self-report as a hallucination vector, not a request for the model to "be more careful."

9. **Session end / baseline drift check.** The operational-assurance layer compares this session's behavior against the qualified baseline it was evaluated at (→ AHES-OA-01 et seq.); a divergence is a signal, not silently absorbed.

## 3. Two structural policy mechanisms, used together

This standard's own §7 clause documents two independently-sufficient ways to discharge AHES-PE-02 (structural, not model-reasoning-dependent, hard-stop enforcement): a **wrapping interceptor** that mediates every tool call before the substrate ever sees it, and **native-gate configuration**, where the harness configures the model's own execution substrate's built-in permission system rather than sitting in front of it. The reference harness uses both, redundantly, where the substrate supports it: native-gate configuration as the first line (cheaper, substrate-enforced, cannot be bypassed by anything the harness itself does wrong), with a wrapping interceptor as the second line for the substrate's residual — actions the native gate's expressiveness can't cover, or substrates that don't offer one at all. Neither mechanism is treated as sufficient alone; the standard's own point (§7.6) is that a requirement satisfiable only one way is weaker than one demonstrated two structurally independent ways, and a reference design that actually wants the strongest available guarantee composes both rather than picking a favorite.

## 4. What's deliberately absent

No specific vendor, model provider, or programming language is named anywhere in this design — the point of a composite reference is that it should be realizable against any substrate meeting AHES §1's model-mediation requirements. No claim is made that this exact five-layer split is the *only* valid architecture; Part 2 specifies domains, not layer topology, and a conforming harness can organize its code however it wants as long as the domain requirements are discharged. This document names one coherent way to do it, not the way.
