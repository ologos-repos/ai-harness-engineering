# AHES Part 2 — Clause Template

Every domain clause in Part 2 follows this shape. The fixed shape is what makes Part 2 a standard rather than a survey: two conforming harnesses are comparable clause-by-clause, and an assessor knows where to look for each kind of claim.

---

## §N `<Domain name>`

### N.1 Purpose

One paragraph: what this domain governs, and the failure class that exists when it is unengineered.

### N.2 Required artifacts

The documents/data a conforming harness **shall** maintain for this domain (e.g., tool registry, policy document, memory schema). Each artifact names an owner and a change-control mechanism.

### N.3 Technical controls

Numbered requirements (`AHES-<abbrev>-<nn>`), each using ISO verbal forms (shall/should/may). Controls are *structural where possible* — enforced by the harness independent of model reasoning — and *procedural only where structural enforcement is infeasible*, with the infeasibility documented.

### N.4 Verification criteria

For each control: how conformance is checked — inspection, analysis, demonstration, or test. Every `shall` has at least one verification criterion. Adversarial verification (attempting to bypass the control) is required for controls whose threat model includes the model itself.

### N.5 Evidence requirements

What the operating harness **shall** capture at runtime so that conformance is auditable after the fact, and where that evidence lives relative to the Part 2 §10 evidence architecture.

### N.6 Informative examples

Non-normative. Worked realizations from reference implementations, cited with version/date.
