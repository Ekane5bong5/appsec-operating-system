# Business Logic Lab — Workflow Abuse

## Objective

Observe how applications can be exploited through normal, valid functionality when business assumptions are not enforced server-side.

---

## Intended Workflow (Observed Pattern)

Across all labs, the intended purchasing workflow followed this pattern:

1. User adds items to a cart
2. Application calculates total cost
3. Discounts or credits are applied according to business rules
4. User confirms purchase
5. Server validates payment and completes the order

This workflow assumes users follow the expected sequence and do not manipulate intermediate steps.

---

## Core Assumptions Made by the Applications

- Item quantities will always be positive and sensible
- Discounts or coupons will only be applied in permitted ways
- Order confirmation will only occur after successful payment
- Client-side flow reflects server-side enforcement

These assumptions were implicit and not enforced as invariants on the server.

---

## How the Assumptions Were Violated

- Valid requests were sent out of the expected order
- Legitimate features were reused or alternated in unintended ways
- Workflow steps were skipped or replayed without restriction
- Server-side logic relied on client-supplied state to determine completion

No malformed input, brute force, or technical exploit was required.

---

## Why No Technical Control Failed

- Authentication functioned correctly
- Authorization checks passed
- Requests were syntactically valid
- Input types were accepted as expected

The applications behaved exactly as implemented.  
The failure occurred at the **business logic and workflow design level**, not the technical control layer.

---

## Required Design-Level Fixes

- Enforce server-side state machines for critical workflows
- Bind actions to explicit, validated state transitions
- Treat business rules as invariants, not assumptions
- Ensure order execution is tied to verified payment state
- Prevent reuse, alternation, or replay of workflow steps

Fixing these issues requires **design changes**, not additional validation or stronger authentication.

---

## Key Takeaway

> Business logic vulnerabilities occur when valid functionality produces invalid outcomes because business intent is not enforced server-side.
