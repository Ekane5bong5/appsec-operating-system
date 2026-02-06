# Access Control Review Checklist

## Purpose
This checklist is used to review access control design and implementation from an AppSec perspective.

It is optimized to detect object-level, identity-scoping, and function-level authorization failures early in design and code review.

---

## Authorization Scope & Invariants
- Is authorization evaluated **per object**, not just per role?
- Is there a clear definition of:
  - who can perform which actions
  - on which specific resources
  - under which conditions?
- Is authorization treated as contextual and continuous, not a one-time check?

---

## Object Ownership & IDOR Risk
- Are object identifiers user-controlled?
- If an identifier is changed, does the system:
  - explicitly verify ownership?
  - or implicitly trust the identifier?
- What happens if a user guesses or enumerates another object ID?
- Is object existence ever confused with authorization?
- Is the system enforcing the invariant:
"The authenticated user must equal the object owner for this operation"?


---

## Identity & Session Binding
- Is user context derived from the authenticated session or token?
- Are client-supplied identity parameters (`userId`, `accountId`, etc.) ignored or strictly validated?
- Can a user act on behalf of another user by modifying request parameters?
  
**Trust Assumption Propagation**

Does the system assume that:
- an authenticated user may access any referenced object?
- a valid session implies ownership of requested resources?
- the ability to reference an object implies permission to access it?

Red flag:
Client input or session presence is treated as proof of authorization.

---

## Function-Level Authorization
- Are privileged functions (admin actions, approvals, deletions) protected server-side?
- Does the backend enforce authorization independently of:
  - frontend routing
  - hidden UI elements
  - proxy or gateway rules?
- Would the function still be protected if accessed directly?

---

## Enforcement Location
- Does authorization occur at the point where the resource is accessed or the action is executed?
- Is authorization enforced server-side on every request?
- Is any access decision delegated to the client, request structure, or routing metadata?
  
**##Authorization Ordering (Critical)**

Is authorization enforced before the object is retrieved or processed?
Does the system ever load an object first and then decide whether access is allowed?
Are redirects, UI restrictions, or response suppression used instead of access denial?

Red flag:
Authorization decisions made after object retrieval or execution.


---

## Failure Handling & Observability
- What happens when authorization fails?
  - Is execution terminated cleanly?
  - Are partial actions prevented?
- Are authorization failures logged?
- Can authorization abuse be detected and investigated?

---

## Final Reviewer Question
- What exactly proves that this user is allowed to perform this action on this specific resource right now?

If the answer relies on:
- request structure
- client input
- assumed roles
- frontend behavior
- or upstream routing

the design should be rejected.
