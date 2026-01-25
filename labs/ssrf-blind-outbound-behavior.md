# SSRF — Blind Outbound Server Behavior

## Context
The application uses analytics functionality that processes the `Referer` header when a product page is loaded. As part of this process, the server makes an outbound request to the URL specified in the header for tracking or analysis purposes.

This behavior is intended to support business analytics, not to fetch user-visible content.

---

## Input That Influenced the Server-Side Request
- User-controlled value within the `Referer` HTTP header
- The header value was treated as a valid URL by the server
- The application used this value to initiate a server-side outbound request

The user did not receive any direct response from the outbound request.

---

## Why the Server Trusted This Input
- The application assumed the `Referer` header would contain a legitimate URL
- The header was processed automatically as part of analytics collection
- No validation or destination policy was enforced before making the outbound request

Trust was placed in the presence of the header rather than the intent behind it.

---

## Trust Boundary That Was Crossed
- User-controlled request metadata influenced server behavior
- The server initiated an outbound request to an external destination
- The boundary crossed was:
  - Application → external service (attacker-controlled)

No internal resources were accessed, but server capability was still exposed.

---

## Deterministic Proof the Request Occurred (Out-of-Band)
- No in-band response data confirmed the outbound request
- An out-of-band interaction was observed via an external monitoring service
- The interaction confirmed that the server resolved and fetched the provided URL

This demonstrates that SSRF does not require visible responses to be exploitable.

---

## Why This Is Higher Impact Than Client-Side Issues
- The request was made by the server, not the browser
- The server’s network position and identity were exposed to an external system
- Outbound server requests can be chained for exfiltration, beaconing, or further exploitation

Lack of response visibility does not imply lack of impact.

---

## Root Cause (Beyond SSRF)
- User-controlled metadata was treated as trustworthy input
- Business-driven functionality initiated outbound requests without policy constraints
- Server-side behavior was decoupled from user visibility and oversight

SSRF occurred because server capability was exposed without intent validation.

---

## Primary Control That Would Actually Prevent This
- Do not initiate outbound requests based on user-controlled headers or metadata

---

## Secondary Defense-in-Depth Controls
- Route outbound requests through a controlled proxy with destination policies
- Restrict outbound network access from application servers
- Log and monitor all server-initiated outbound requests

---

## One-Sentence Risk Statement
An attacker can cause the application server to make outbound requests to attacker-controlled destinations without any visible response, enabling blind abuse of server network capabilities.
