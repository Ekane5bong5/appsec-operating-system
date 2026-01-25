# SSRF — Internal Lateral Movement via Trusted Services

## Context
The application includes a stock check feature that allows the server to fetch data from a specified location. This functionality causes the application to make server-side requests based on user-influenced input.

The internal environment contains additional backend services that are not intended to be exposed to end users and rely on implicit trust within the internal network.

---

## Input That Influenced the Server-Side Request
- User-influenced input within the stock check functionality
- The input allowed the application to direct server-side requests to different internal destinations
- The application acted as a proxy between the user and internal services

---

## Why the Server Trusted This Input
- The application was permitted to communicate freely within the internal network
- Internal services assumed that requests originating from the application tier were legitimate
- No request-level authentication or authorization was enforced between internal services

Trust was based on network position rather than verified service identity.

---

## Trust Boundary That Was Crossed
- Public user input initiated a server-side request
- The request crossed from the application into internal backend services
- The boundary crossed was:
  - Application → internal service
  - External user → internal network (indirectly via the application)

Once crossed, internal services no longer evaluated user intent.

---

## Deterministic Proof of Internal Lateral Movement
- Internal administrative functionality was inaccessible directly from the client
- The same functionality became accessible when requests were routed through the application
- Successful execution of privileged actions confirmed that internal services trusted the application implicitly

This demonstrates the application acting as a lateral movement pivot.

---

## Why This Is Higher Impact Than Client-Side Issues
- The application functions as a trusted intermediary inside the network
- Internal services often expose broader functionality than public-facing endpoints
- SSRF enables movement across internal trust zones without breaching perimeter defenses

This shifts the threat from input abuse to internal system compromise.

---

## Root Cause (Beyond SSRF)
- Internal services relied on network location as a substitute for authorization
- No service-to-service authentication was enforced
- User context was not preserved or revalidated across internal requests

SSRF exposed a systemic failure in internal access control design.

---

## Primary Control That Would Actually Prevent This
- Enforce explicit service-to-service authentication and authorization for all internal requests

---

## Secondary Defense-in-Depth Controls
- Limit application egress to only required internal services
- Require internal services to validate caller identity, not just request origin
- Reduce implicit trust between internal components

---

## One-Sentence Risk Statement
An attacker can leverage the application as a pivot to access and manipulate internal backend services by exploiting implicit trust between internal systems.
