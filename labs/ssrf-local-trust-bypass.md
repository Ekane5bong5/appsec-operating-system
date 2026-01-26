# SSRF — Local Trust and Authorization Bypass

## Context
The application provides a stock check feature that fetches data from another service on behalf of the user. This functionality requires the server to make an outbound request based on user-supplied input.

The application also exposes an administrative interface intended to be accessible only from the local server environment.

---

## Input That Influenced the Server-Side Request
- User-controlled input within the stock check functionality
- The input was used directly by the application to construct a server-side request
- While the user did not fully control the request format, they influenced the effective destination of the request

---

## Why the Server Trusted This Input
- The application assumed that requests originating from the local server environment were inherently trusted
- The administrative interface relied on network location (`localhost`) as an authorization control
- No explicit authentication or authorization checks were enforced for requests coming from the server itself

This created a trust relationship where origin was treated as identity.

---

## Trust Boundary That Was Crossed
- Public user input was used to initiate a server-side request
- The request crossed from the public-facing application into an internal, trusted context
- The boundary crossed was:
  - Public → internal network
  - Application → internal administrative service

Once this boundary was crossed, intent was no longer re-validated.

---

## Deterministic Proof the Request Originated Server-Side
- The administrative interface was inaccessible directly from the browser
- The same administrative endpoint became accessible when requested by the server
- This demonstrates that the request was made from a trusted server context rather than the client

The ability to perform privileged administrative actions confirmed server-side request execution.

---

## Why This Is Higher Impact Than Client-Side Issues
- The server operates from a trusted network position unavailable to external users
- Internal services treated the request as authenticated based solely on origin
- The server inherited privileges that bypassed all user-facing authentication and authorization controls

This transforms a single input validation issue into a full authorization bypass.

---

## Root Cause (Beyond SSRF)
- Authorization decisions were based on network location rather than explicit identity
- Internal administrative functionality did not enforce authentication independently
- SSRF acted as the delivery mechanism, but the core failure was an authorization model flaw

---

## Primary Control That Would Actually Prevent This
- Enforce explicit authentication and authorization on administrative interfaces, regardless of request origin

---

## Secondary Defense-in-Depth Controls
- Restrict server egress to only required destinations
- Avoid using user-controlled input to influence server-side request destinations
- Apply outbound request policies at the network layer

---

## One-Sentence Risk Statement
An attacker can cause the application server to access internal administrative functionality, bypassing authentication by exploiting trust in locally originating requests.
