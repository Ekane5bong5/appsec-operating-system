# Business Logic Mental Model  
**Logic ≠ Security**

## What a business logic flaw actually is

- A business logic flaw occurs when **valid actions produce invalid outcomes**
- The system behaves correctly according to its code, but incorrectly according to business intent
- Authentication, authorization, and input validation may all be functioning properly
- The vulnerability lives in **assumptions, state, sequence, and missing invariants**, not in malformed input

---

## Why an application can be “secure” and still exploitable

- Security requirements are often vague and do not constrain business behavior
- Business rules are implied rather than enforced technically
- The server trusts that users follow the intended workflow
- Client-side controls create false confidence about what the server actually enforces
- Security controls protect *access*, not *logic*

---

## Why automated tools fail here

- Scanners detect known patterns, not business intent
- There is no universal payload for logic abuse
- Tools cannot infer rules like “this should only happen once” or “this step must precede that one”
- Without understanding what *should* happen, tools can only verify that requests are technically valid

---

## Why QA often misses logic flaws

- QA tests expected behavior, not adversarial behavior
- Test cases validate requirements, not misuse paths
- Logic abuse often involves “weird but valid” interactions
- Client-side constraints hide server-side enforcement gaps

> QA is doing their job — the system is simply not designed to defend against intent.

---

## Where business logic vulnerabilities are introduced

- Product inception through flawed or unstated assumptions
- Design phases where invariants are not defined
- Feature expansion layered onto existing logic
- Threat models that focus only on technical attacks
- Lack of abuse-case thinking during design reviews

> By the time AppSec sees a logic flaw, it is usually already a feature.

---

## Example: Valid requests, invalid outcome

**Feature**  
A bank application allows users to transfer funds between accounts through a multi-step workflow.

**Assumption**  
Users will only request sensible transfers and client-side controls prevent invalid values.

**Gap**  
The server does not enforce a core business invariant about how value should move.

**Abuse**  
A user submits a valid but unexpected transfer request while remaining fully authenticated and authorized.

**Outcome**  
All requests are technically valid, yet the system executes a transfer that violates business intent and causes financial loss.

---

## Core takeaway

> **Input validation bugs reject bad data.  
> Business logic bugs mishandle valid data.**
