# Logic Threat Modeling — Reviewer Lens

## Core Principle

If a rule exists only in documentation or UI behavior, it does not exist.

Documentation describes intent.
UI controls guide normal users.
Only server-side enforcement creates reality.

Any rule that is not enforced by the backend can be bypassed by valid requests that violate business intent.

---

## Thinking in State, Not Screens

Applications do not run on pages or buttons.
They run on state.

Every meaningful action assumes the system is in a specific state.
If that state is not explicitly checked, the action may be possible out of order.

Reviewer question:
**What state must the system be in before this action is allowed?**

If the answer is implied instead of enforced, the workflow is vulnerable.

---

## Business Invariants

A business invariant is a rule that must always hold true, regardless of user behavior.

Examples:
- A transaction cannot execute without authorization
- A discount can only be applied once
- An order total cannot be negative
- Funds cannot be created or duplicated

Reviewer question:
**What business invariant is enforced here?**

If the invariant cannot be clearly stated, it is likely not enforced.
If it is only implied, it is already broken.

---

## Sequence Enforcement

Many workflows rely on steps occurring in a specific order:
create → validate → approve → execute

If the system does not enforce this order server-side, sequence becomes an assumption instead of a control.

Reviewer question:
**What would a valid request look like if the sequence were broken?**

If a well-formed, authenticated request could succeed without completing prior steps, the workflow is fragile.

---

## Common Failure Pattern

Teams often assume:
- Users will follow the intended flow
- UI restrictions mirror backend rules
- Steps will not be skipped or replayed
- Requests will arrive in the correct order

These assumptions must be made explicit and enforced.
Otherwise, valid requests can produce invalid business outcomes.

---

## Reviewer Takeaway

Logic flaws are not caused by missing security controls.
They are caused by missing enforcement of business rules.

Input validation rejects bad data.
Business logic security prevents valid data from producing invalid outcomes.

Design reviews should focus on state, invariants, and enforcement — not intent.
