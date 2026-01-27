# HTTP Desynchronization Risk Patterns  
## Infrastructure-Aware AppSec Reviewer Lens

### Purpose
This document captures how HTTP request desynchronization risk enters modern architectures, why it is often missed, and how it leads to real security impact. The goal is to identify risk during architecture and design review, not during exploitation.

---

## Where Desync Risk Enters Architectures

HTTP desynchronization risk is introduced anywhere multiple systems independently parse and forward HTTP requests.

In modern architectures, this typically includes:
- CDNs
- Reverse proxies
- Load balancers
- Ingress proxies (e.g., NGINX, Envoy)
- Backend application servers

Each of these components must decide where an HTTP request ends before forwarding traffic downstream. When multiple such components exist in a request path, request boundary decisions are made more than once.

Desynchronization risk exists when these components do not use identical rules to determine request boundaries.

---

## Common Dangerous Assumptions

Several assumptions cause teams to overlook request smuggling risk:

- “Managed infrastructure handles this safely.”
  Managed proxies still parse and rebuild HTTP requests. They do not remove ambiguity; they add parsers.

- “HTTP/2 protects us from request smuggling.”
  Many systems use HTTP/2 at the edge but downgrade to HTTP/1.1 internally, reintroducing ambiguity during translation.

- “The backend can trust requests forwarded by the front end.”
  Backends often assume front-end validation has already occurred and may skip additional checks.

---

## Why Layered Systems Increase Risk

Layered systems increase risk because each layer:
- Parses HTTP independently
- May normalize, rewrite, or re-encode requests
- May support different protocol features

Every additional HTTP-parsing layer introduces another interpretation of where a request begins and ends. The more layers involved, the higher the chance that at least two components will disagree.

Request smuggling is therefore an architectural risk, not an application bug.

---

## How Request Smuggling Leads to Impact

When request boundaries are misaligned, leftover bytes from one request can be interpreted as part of the next request on the same connection.

This can lead to:
- **Authentication bypass**: the backend processes a request that bypassed front-end access controls.
- **Request hijacking**: part of one user’s request is prepended to another user’s request.
- **Cache poisoning or cache deception**: responses are cached under the wrong request key, affecting other users.

These impacts occur without exploiting application logic directly; they result from infrastructure-level disagreement.

---

## Realistic Enterprise Scenario

A common enterprise setup:

Client → CDN → Ingress Proxy → Application Server

- The CDN terminates HTTP/2 and forwards requests to the ingress proxy.
- The ingress proxy parses HTTP/1.1 requests and forwards them to the backend.
- The backend assumes all requests were validated upstream.

If the CDN, ingress proxy, and backend interpret request boundaries differently, a malformed request can cause data from one request to bleed into another, even though all components are “secure” in isolation.

---

## AppSec Takeaway

Any system that parses, rewrites, or forwards HTTP requests is a request-boundary authority. Desynchronization risk exists wherever multiple such systems are chained together.

Request smuggling should be evaluated during architecture and design review, not treated solely as a testing-time vulnerability.
