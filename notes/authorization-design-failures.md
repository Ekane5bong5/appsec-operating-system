# Authorization Design Failures — Reviewer Lens

## Purpose
This document captures how authorization fails at the **design level**, not just in implementation.

It is written from the perspective of an AppSec engineer reviewing system architecture and access control models before approval.

---

## Where Access Control Should Live
Authorization must be enforced **server-side at the point where the protected resource or privileged action is executed**.

Specifically:
- Object access must be authorized at **object retrieval time**
- User-scoped data must be resolved from the **authenticated session**, not request parameters
- Privileged functionality must enforce **function-level authorization inside the backend**

Authorization must live with:
- the resource
- the action
- the execution path

It must not live in:
- frontend logic
- routing rules
- URL structure
- headers
- client-supplied identifiers

---

## Common Authorization Design Anti-Patterns

### Frontend-Only Access Control
Designs that rely on:
- hidden buttons
- disabled UI elements
- blocked routes

fail because frontend controls are **not security boundaries**.

Any request that reaches the backend must be treated as untrusted and independently authorized.

---

### Implicit Trust in Object Identifiers
Systems that treat possession of an object identifier as permission to access the object are fundamentally broken.

Examples:
- numeric IDs
- UUIDs
- filenames
- account numbers

This leads directly to IDOR.

Object existence must never be confused with authorization.

---

### Role-Only Enforcement
Role checks alone are insufficient for real systems.

Problems with role-only models:
- roles do not express ownership
- roles do not encode context
- roles do not scale cleanly in multi-tenant systems

Authorization decisions often require:
- identity
- object ownership
- tenant boundaries
- state
- time or environment

Roles may be an input, but they cannot be the entire decision.

---

## Why IDOR Is a Design Flaw, Not a Bug
IDOR vulnerabilities do not occur because developers “forgot a check.”

They occur because the **design assumes** that:
- object identifiers are safe to trust
- access patterns imply permission
- authenticated users are broadly trusted

Once an architecture allows user-controlled object references without explicit ownership validation, IDOR is inevitable.

IDOR is the result of **missing authorization invariants**, not missing conditionals.

---

## “Looks Secure but Isn’t” — Example
A common design that appears secure:

- User authenticates successfully
- Backend checks role (e.g., `USER`)
- API accepts `userId` or `resourceId` as a request parameter
- Data is returned if the role check passes

Why this fails:
- the role does not prove ownership
- the identifier is client-controlled
- authorization scope is never validated

This design passes superficial reviews but fails under adversarial thinking.

---

## Reviewer Takeaway
When reviewing authorization design, the key question is not:

> “Is there an access control check?”

The key question is:

> “What exactly proves that this user is allowed to perform this action on this specific resource right now?”

If the answer relies on:
- request structure
- client input
- frontend controls
- or assumptions about user behavior

the design should be rejected.
