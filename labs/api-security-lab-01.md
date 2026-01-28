# API Security Lab 01 — Authorization Failures in API-Driven Systems

## Lab 1 — Exploiting an API Endpoint Using Documentation

### What object or data was exposed?
An internal API documentation endpoint (`/api`) was exposed.  
The documentation was interactive and allowed execution of privileged actions, including deleting other users.

### What authorization check was missing?
There was no authorization check controlling access to the API documentation or the destructive operations it exposed (e.g., DELETE user).  
The system assumed that anyone who could reach `/api` was allowed to interact with administrative functionality.

### Why traditional session logic didn’t apply
The request was authenticated as a valid user, but **authorization was never evaluated for the action itself**.  
Session presence ≠ permission to perform administrative operations.  
The API trusted authentication without enforcing role or capability checks.

### How this scales badly in microservices
In microservice environments, services often trust requests coming from “known” paths or documented endpoints.  
If documentation endpoints or internal APIs are exposed, **any authenticated client can trigger privileged downstream actions** without service-level authorization.

### What control would actually prevent this
- Authorization enforcement on every sensitive API action
- Restricting access to API documentation endpoints
- Role- or capability-based checks enforced server-side per operation


---

## Lab 2 — Finding and Exploiting an Unused API Endpoint

### What object or data was exposed?
A hidden API endpoint (`PATCH /api/products/{id}/price`) that allowed modification of product pricing.

### What authorization check was missing?
There was no authorization check ensuring that only privileged users or services could update product prices.  
The system trusted the HTTP method and endpoint usage instead of validating the caller’s permissions.

### Why traditional session logic didn’t apply
The request originated from an authenticated user session, but **the API never evaluated whether the user was allowed to perform price updates**.  
The API implicitly trusted that “whoever can call this endpoint is allowed to use it.”

### How this scales badly in microservices
Microservices often expose internal endpoints intended for admin tools or background services.  
When these endpoints are reachable and lack service-level authorization, **any authenticated client can perform sensitive state changes** across the system.

### What control would actually prevent this
- Service-level authorization checks for sensitive actions
- Clear separation between public and internal/admin APIs
- Capability-based authorization rather than method-based trust


---

## Lab 3 — Exploiting a Mass Assignment Vulnerability

### What object or data was exposed?
Hidden object fields (`chosen_discount`) that were not intended to be set by the client but were still processed by the API.

### What authorization check was missing?
The API did not enforce which object properties a client was allowed to modify.  
It blindly mapped client-supplied JSON fields into internal order objects.

### Why traditional session logic didn’t apply
The user was authenticated, but **authorization was never evaluated at the field level**.  
The API assumed that any provided fields were safe because the request came from a valid session.

### How this scales badly in microservices
In distributed systems, services frequently accept JSON objects from other services.  
If schemas are reused or loosely validated, **hidden or sensitive fields can be injected across service boundaries**, leading to privilege escalation and financial abuse.

### What control would actually prevent this
- Explicit allow-lists for writable fields
- Separate input DTOs from internal domain models
- Server-side enforcement of business rules independent of client input


---

## Cross-Lab Takeaways

- APIs amplify authorization failures because **every request is independent**
- Authentication alone is never sufficient
- Trust must be enforced at:
  - Endpoint level
  - Object level
  - Field level
- In microservices, **trust propagation without verification becomes an attack surface**

This class of failure feels like IDOR — but is more dangerous because it operates at system scale.
