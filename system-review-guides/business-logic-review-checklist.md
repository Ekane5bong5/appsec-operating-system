# Business Logic Review Checklist

## Purpose

This checklist is used to identify business logic and workflow vulnerabilities during:
- Design reviews
- Threat modeling
- Architecture discussions
- Code reviews (high-level)

It focuses on **how systems fail when assumptions are not enforced**.

---

## Workflow & Sequence

- Can steps in this workflow be skipped?
- Can steps be repeated?
- Can steps be executed out of order?
- What prevents a later step from being triggered early?
- Is the intended sequence enforced server-side or inferred from UI flow?

If sequence matters, it must be enforced explicitly.

---

## State & Transitions

- What states does this feature move through?
- Are all allowed state transitions explicitly defined?
- Are invalid state transitions explicitly blocked?
- Is state derived from server-side data or client input?
- What proves the system is in the correct state before execution?

If state is assumed, it is not secure.

---

## Business Invariants

- What must always be true for this feature?
- Can valid requests ever produce invalid business outcomes?
- Are limits (quantity, balance, discounts, attempts) enforced server-side?
- Are “one-time” actions actually enforced as one-time?
- What prevents values from going negative, duplicating, or compounding?

If an invariant cannot be clearly stated, it is likely not enforced.

---

## Assumptions & Expected Behavior

- What assumptions does this design make about user behavior?
- Are those assumptions documented or implicit?
- Does the backend rely on users following the happy path?
- Are UI controls being treated as security controls?
- What happens when a user behaves incorrectly but legitimately?

Assumptions are not controls.

---

## Idempotency & Replay

- What happens if this request is sent twice?
- Can tokens, approvals, or actions be reused?
- Are actions bound to unique identifiers or state?
- Is there an expiration or single-use guarantee?
- Does replay change business outcome?

If replay changes outcome, replay must be prevented.

---

## Execution-Time Validation

- What is the final gate before this action executes?
- Are state, authorization, and invariants revalidated at execution time?
- Could timing or race conditions alter the outcome?
- Is there any time-of-check vs time-of-use gap?

Execution is where intent becomes irreversible impact.

---

## Abuse-Oriented Questions (Final Pass)

- What is the worst valid thing a user could do here?
- What breaks if a user behaves “incorrectly” but within protocol?
- What would a valid request look like if the workflow were abused?
- Would this still work if requests arrived in an unexpected order?

If you can imagine it, assume it will happen.

---

## Reviewer Takeaway

Business logic vulnerabilities occur when:
- Valid functionality
- Produces invalid outcomes
- Because business rules were assumed, not enforced

This checklist is designed to surface those failures before they ship.
