# API Security Mental Model: APIs Are Not “Just Endpoints”

## Core Thesis

API security failures are not isolated endpoint bugs.
They are systemic trust failures across service boundaries.

In API-driven and microservice architectures, authorization mistakes scale silently and propagate downstream.

---

## Why APIs Amplify Authorization Failures

- APIs expose raw objects and actions directly, without a UI layer to constrain behavior.
- Authorization must be enforced explicitly in code, not implied by navigation or workflows.
- A single missing authorization check affects all clients immediately.
- APIs rely on client-supplied identifiers (IDs, parameters, headers), increasing trust risk.
- Endpoint access often exists by design; violations occur at object or action level.

---

## Why “Stateless” Does Not Mean “Simple”

- Stateless APIs require authentication and authorization on every request.
- Lack of session context pushes identity and permissions into tokens and headers.
- Systems often assume upstream checks were sufficient, creating gaps downstream.
- Authorization logic becomes fragmented across services and layers.
- As systems scale, consistency of enforcement degrades.

---

## How Trust Propagates Across Services

- One service authenticates an external user at the edge (gateway or proxy).
- Identity context is propagated to downstream services.
- Downstream services often trust upstream decisions without re-validating intent.
- Trust increases with each hop, while enforcement decreases.
- A single compromised or misconfigured service can violate system-wide authorization.

---

## OWASP API Security Risks as Trust Failures

### Broken Object Level Authorization (BOLA)
- User is allowed to call the endpoint.
- System fails to verify ownership or permission on the specific object.
- Root cause: trusting client-controlled object identifiers.

### Broken Function Level Authorization (BFLA)
- Endpoint exists and is reachable.
- System fails to enforce role or privilege for the action.
- Root cause: trusting endpoint access instead of enforcing intent.

### Excessive Data Exposure
- Backend returns full data models.
- Client is trusted to hide sensitive fields.
- Root cause: trusting the client to enforce confidentiality.

### Mass Assignment
- Client input is automatically mapped to internal objects.
- Sensitive or internal fields are modifiable by the client.
- Root cause: trusting input structure instead of explicitly allowing fields.

### Improper Assets Management
- APIs exist without clear ownership, documentation, or protection.
- Older or internal APIs remain reachable.
- Root cause: trusting obscurity instead of inventory and enforcement.

---

## Microservices-Specific Risk

- Internal services assume callers are trusted because they are “internal.”
- Network position is mistaken for identity.
- External user tokens are reused internally.
- Authorization decisions are centralized, then implicitly trusted everywhere.
- A single identity leak or bypass becomes a system-wide compromise.

---

## Identity and Service-to-Service Trust

- mTLS establishes service identity, not user authorization.
- Token-based systems often over-trust token presence.
- Identity propagation without re-signing or scoping is dangerous.
- Internal identity should be distinct from external identity.
- Authorization must be enforced independently at each service boundary.

---

## Why Logging and Correlation Matter

- Distributed systems hide trust failures without visibility.
- Correlation IDs are required to trace authorization decisions across services.
- Without structured logs, attacks resemble normal traffic.
- Logging enables detection of systemic trust breakdowns, not just bugs.

---

## Core AppSec Takeaways

- API security failures are trust failures, not endpoint failures.
- Every service boundary is a security boundary.
- Authorization must be explicit, enforced, and verifiable at every hop.
- “Authenticated” does not mean “authorized.”
- Defense in depth is mandatory in API and microservice architectures.
