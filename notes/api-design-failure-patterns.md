# API Design Failure Patterns

APIs are not secured at the request level — they are secured at the **contract level**.

Most API breaches occur because the API contract itself encodes unsafe trust assumptions. Once those assumptions are approved and deployed, every downstream system inherits the risk.

This document captures recurring API design failures observed in real enterprise systems.

---

## Where API Security Fails by Design

API failures are rarely caused by missing authentication. They are caused by **authorization ambiguity baked into the contract**.

Common root causes include:

- Contracts that describe *capability* without describing *permission*
- Endpoints that accept identifiers without enforcing ownership
- Schemas that expose fields never intended to be client-controlled
- Assumptions that “internal” APIs will never be abused
- Reliance on gateways to enforce logic they cannot see

Once published, an API contract becomes a source of truth that tools, developers, and services blindly trust.

---

## Common API Design Anti-Patterns

### 1. Overly Broad Endpoints

Single endpoints that handle multiple roles or use cases:

- Generic update endpoints (`PATCH /resource`)
- Shared schemas across admin and user workflows
- Endpoints that mutate sensitive state without role separation

**Risk:** Privileged functionality becomes reachable through legitimate paths.

---

### 2. Missing Object Ownership Checks

Contracts that accept object identifiers without encoding ownership expectations:

- `/users/{id}`
- `/orders/{orderId}`
- `/accounts/{accountId}`

The contract assumes:
- Authentication implies authorization
- The caller “should” own the object

**Risk:** BOLA vulnerabilities scale silently across systems.

---

### 3. Field-Level Trust Violations

Schemas that allow clients to submit or modify internal fields:

- Prices
- Discounts
- Roles
- Status flags
- Internal configuration values

These fields often exist for internal workflows but are exposed through shared schemas.

**Risk:** Mass assignment and privilege escalation without breaking auth.

---

### 4. Blind Trust in Upstream Services

Downstream services assume that upstream services have already enforced authorization:

- “The gateway handled it”
- “This service is internal”
- “The token was validated earlier”

**Risk:** Trust propagates without verification, creating system-wide blast radius.

---

### 5. “Internal API” as a Security Boundary

Labeling an API as internal without enforcing it technically:

- No mutual authentication
- No network enforcement
- No service identity validation
- No per-service authorization

**Risk:** Internal APIs become the highest-impact attack surface.

---

## Why API Gateways Don’t Solve Everything

API gateways are effective at:
- Authentication
- Rate limiting
- Schema validation
- Traffic control

They cannot:
- Enforce object ownership
- Understand business intent
- Apply field-level authorization
- Prevent logical privilege escalation

Authorization must exist **inside** the service that owns the data.

---

## Realistic Enterprise Failure Scenario

An organization exposes an OpenAPI contract for order management.

The contract includes:
- A shared `Order` schema
- A `PATCH /orders/{id}` endpoint
- Authenticated access enforced at the gateway

The schema includes fields for:
- `price`
- `discount`
- `status`

These fields are intended for internal workflows.

Because the contract allows them:
- The gateway permits them
- The service accepts them
- Attackers manipulate them

The failure was not in implementation.

The failure was approving a contract that encoded unsafe assumptions.

---

## AppSec Review Takeaway

If an API contract allows it, someone will eventually exploit it.

Effective AppSec review means:
- Treating contracts as **attack surfaces**
- Reviewing schemas as **permission boundaries**
- Assuming zero trust between services
- Designing APIs for **least capability**, not convenience

This class of failure feels like IDOR — but it is more dangerous because it operates at **system scale**.
