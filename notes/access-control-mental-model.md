# Access Control Mental Model — Authority Is Always Contextual

## Purpose
This document captures the core mental model for reasoning about authorization failures in real-world systems.  

## Core Principle
Authorization decides **who can do what to which object under which conditions**.

This decision must be:
- explicit
- server-side
- deny-by-default
- evaluated on every request

Being authenticated does **not** imply being authorized.

---

## Authentication vs Authorization
**Authentication**
- Establishes identity
- Proves *who* the user is
- Typically performed once per session

**Authorization**
- Determines permission
- Proves *what the user is allowed to do*
- Must be evaluated continuously

Most authorization vulnerabilities occur **after login**, when identity is incorrectly treated as authority.

---

## The Authorization Invariant
Every protected request must re-prove:
- **Who** is making the request
- **What** action is being requested
- **Which object** is targeted (specific instance, not type)
- **Which conditions** apply (role, ownership, tenant, time, location, state)

Authorization is contextual and continuous, not static.

---

## Least Privilege Is a Design Decision
Least privilege must be defined during architecture and design, not retrofitted later.

Effective least-privilege design requires:
- enumerating user types
- identifying protected resources
- mapping allowed operations per user–resource combination

Without early planning, systems drift into privilege creep, where users accumulate excessive access that becomes difficult to revoke safely.

---

## Deny by Default
An authorization system must always reach a decision.

If no rule explicitly permits access, access must be denied.  
Unmatched cases are not neutral — they are dangerous.

Framework defaults should not be trusted implicitly. Explicit configuration is safer than relying on evolving third-party behavior.

---

## Per-Request Enforcement
Authorization must be validated on **every request**, regardless of:
- request origin (AJAX, server-side, background job)
- perceived sensitivity
- prior checks earlier in the flow

Partial coverage is failure.  
An attacker only needs one missed check.

Centralized enforcement mechanisms reduce the risk of accidental omissions.

---

## Frameworks Reduce Effort, Not Responsibility
Authorization frameworks provide primitives, not guarantees.

Common failure patterns include:
- assuming defaults are sufficient
- misconfiguring complex frameworks
- trusting prebuilt logic that does not match application requirements

Authorization requirements must be defined first.  
Frameworks are evaluated and customized second.

---

## RBAC vs ABAC vs ReBAC
**RBAC**
- Simple to start
- Poor at object-level and multi-tenant control
- Suffers from role explosion at scale

**ABAC**
- Uses attributes of users, objects, and environment
- Supports contextual, fine-grained decisions
- Aligns better with least privilege

**ReBAC**
- Grants access based on relationships between users and resources
- Effective for ownership and horizontal access control

For modern applications, ABAC and ReBAC scale better than RBAC alone.

---

## IDOR and User-Controlled Identifiers
Exposing internal object identifiers introduces risk if ownership and scope are not enforced.

Changing an identifier should result in:
- an authorization failure
- not “object not found”
- not successful access

Access to one object of a type does **not** imply access to all objects of that type.

This failure mode leads to IDOR and horizontal privilege escalation.

---

## Static Resources Require Authorization
Static resources (files, exports, cloud storage objects) are still resources.

Protection should be based on:
- data classification
- user attributes
- contextual conditions

Whenever possible, static resources should be protected using the same authorization mechanisms as dynamic functionality.

---

## Where Authorization Must Be Enforced
Client-side checks improve user experience but are not security controls.

Authorization must be enforced:
- server-side
- in middleware or gateways
- in serverless functions

Any control enforced only on the client must be treated as nonexistent.

---

## Failing Securely
Authorization failures are normal and must be handled deliberately.

Failures should:
- terminate execution cleanly
- avoid partial state changes
- return appropriate status codes
- avoid leaking sensitive information

Centralized failure handling reduces inconsistency and bypass risk.

---

## Logging and Detection
Authorization without logging is blind enforcement.

Effective logging:
- detects abuse
- supports incident response
- enables auditing

Logs must balance completeness with data sensitivity and be time-synchronized across systems.

---

## Testing Authorization Logic
Unit and integration tests help detect:
- missing deny-by-default behavior
- incorrect failure handling
- broken ABAC or relationship logic

Automated tests catch simple errors early but do not replace manual security testing.

---

## Final Takeaway
Authorization failures are often subtle, contextual, and high-impact.

Security depends less on tools and more on consistently applying the authorization invariant across the system.
