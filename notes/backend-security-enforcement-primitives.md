# Backend Security Enforcement Primitives

## Purpose

This document defines the core mechanisms backend systems use to enforce security.

Most application vulnerabilities are not caused by missing security tools or controls.
They are caused by missing or incomplete enforcement of these primitives.

Understanding these primitives allows AppSec to:
- Review designs before code exists
- Predict entire classes of vulnerabilities
- Explain risk in system-level terms
- Reason about security without relying on exploit patterns

---

## Core Principle

Security is not intent.
Security is enforcement.

Anything not explicitly enforced by the backend is an assumption.
Assumptions fail.

---

## The Six Enforcement Primitives

Every secure backend system enforces rules using some combination of the following primitives.

---

## 1. State Validation

**What it enforces:**  
Whether the system is in the correct state for an action to occur.

Examples:
- An order must be `PAID` before it can be `SHIPPED`
- A user must be `VERIFIED` before performing sensitive actions
- A session must be `ACTIVE` before requests are accepted

Key idea:
> Actions are only valid in specific states.

Common failure:
- The backend assumes state based on request flow instead of validating it
- Steps can be skipped, replayed, or reordered

Reviewer question:
- What state must the system be in before this action is allowed?
- Where is that state checked server-side?

---

## 2. Invariant Enforcement

**What it enforces:**  
Rules that must always remain true, regardless of user behavior.

Examples:
- Account balances cannot go negative
- Discounts can only be applied once
- Funds cannot be created or destroyed
- Ownership relationships cannot be violated

Key idea:
> Valid input should never produce invalid business outcomes.

Common failure:
- Systems validate input but fail to enforce business meaning
- Invariants exist only as assumptions or documentation

Reviewer question:
- What must always be true after this action executes?
- Where is that invariant enforced?

---

## 3. Authorization at Execution Time

**What it enforces:**  
Whether a specific user is allowed to perform a specific action on a specific resource at that moment.

This includes:
- Ownership
- Role
- Context
- State
- Timing

Key idea:
> Authorization must be evaluated at the moment of execution, not inferred earlier.

Common failure:
- Authorization is checked at entry points but not revalidated
- Access decisions rely on request structure or client-controlled identifiers

Reviewer question:
- What exactly proves this user is allowed to perform this action right now?
- Is that proof derived server-side?

---

## 4. Trust Boundary Enforcement

**What it enforces:**  
Where data crosses from untrusted to trusted domains.

Backends must assume:
- Clients lie
- Requests are replayed
- Parameters are modified
- Order is manipulated

Key idea:
> Any value crossing a trust boundary must be revalidated or recomputed.

Common failure:
- Trusting client-calculated values
- Trusting hidden fields or UI-controlled parameters
- Treating frontend logic as security logic

Reviewer question:
- Where does this data originate?
- What prevents a client from controlling it?

---

## 5. Idempotency and Replay Control

**What it enforces:**  
Whether repeating a request produces the same outcome or causes harm.

Critical for:
- Payments
- Transfers
- Approvals
- Coupons
- One-time actions

Key idea:
> Repeating a request should not change business outcome unless explicitly intended.

Common failure:
- One-time actions can be replayed
- Tokens remain valid across states
- Requests lack uniqueness or expiration

Reviewer question:
- What happens if this request is sent twice?
- What prevents replay?

---

## 6. Final Authorization Gate

**What it enforces:**  
A last validation step immediately before execution.

At execution time, the backend should re-check:
- State
- Authorization
- Invariants
- Ownership
- Replay conditions

Key idea:
> Execution is where intent becomes irreversible impact.

Common failure:
- Checks occur earlier but not at execution
- Time-of-check vs time-of-use gaps
- Race-condition-like logic flaws

Reviewer question:
- What is the final gate before this action executes?
- What is revalidated there?

---

## How Vulnerabilities Map to Missing Primitives

Most vulnerability classes are symptoms of missing enforcement:

- IDOR → missing authorization at execution time
- Workflow abuse → missing state validation
- Infinite money / discounts → missing invariants
- Replay attacks → missing idempotency
- Logic flaws → reliance on assumptions instead of enforcement

---

## Reviewer Mindset

Backend security is not about blocking attackers.
It is about making invalid business outcomes impossible.

If a valid request can produce an invalid result, the system is insecure by design.

---

## Core Takeaway

Input validation rejects bad data.  
Authentication establishes identity.  
Authorization controls access.

**Backend security is enforced by state, invariants, and execution-time validation.**

Everything else is decoration.
