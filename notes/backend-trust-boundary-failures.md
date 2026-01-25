# Backend Trust Boundary Failures (SSRF Reviewer Lens)

## Purpose
This document captures how SSRF risk enters backend and cloud architectures, how to recognize it during design reviews, and why SSRF is fundamentally a trust and authorization problem rather than a URL validation issue.

It is intended for interview preparation, architecture reviews, and long-term AppSec mastery.

---

## Where SSRF Risk Usually Enters Systems
SSRF risk is introduced whenever user-controlled data can influence where a server makes outbound network requests across a trust boundary.

Common design entry points:
- URL fetchers (avatars, previews, imports, link unfurling)
- Webhooks and callback URLs
- Integrations that pull data from customer-provided endpoints
- PDF or image renderers that fetch remote assets
- Analytics or logging systems that follow URLs or headers

These features are typically added intentionally for business reasons and later under-protected.

---

## The Two SSRF Design Scenarios

### Case 1 — Allow-List Feasible
- Destinations are known and stable (internal services, approved partners).
- SSRF risk should be eliminated by enforcing explicit destination policies.
- Failure to use allow-lists in this case is a design flaw.

### Case 2 — Allow-List Not Feasible
- Destinations are arbitrary or customer-defined (webhooks, callbacks).
- SSRF risk cannot be eliminated, only constrained.
- Designs must rely on strong network-level controls and blast-radius reduction.

Key reviewer insight:
- If allow-lists are infeasible, compensating controls are mandatory.

---

## Common Dangerous Patterns

### URL Fetchers
- User input influences destination directly or indirectly.
- Validation focuses on format rather than destination policy.
- Redirects and DNS resolution can change the effective destination after validation.

### Webhooks and Callbacks
- Arbitrary outbound destinations are required by design.
- Teams default to sanitization and block-lists, which are brittle.
- Outbound calls become both an SSRF vector and an exfiltration channel.

### PDF / Image Renderers
- Rendering engines fetch external resources beyond developer intent.
- SSRF may be invisible because results are embedded in generated artifacts.
- Server-side fetch behavior is often underestimated.

### Integrations
- Integration services often run with elevated permissions.
- Trust is placed in the service rather than validated per request.
- SSRF impact is amplified by broad access and implicit trust.

---

## Why “We Sanitize URLs” Is Not Enough
- Sanitization validates format, not intent or destination policy.
- SSRF exploits differences between validation time and request execution time.
- DNS resolution, redirects, and parsing ambiguity can alter the final destination.

SSRF is not a parsing problem.  
It is a trust and authorization problem.

---

## Why Network-Level Controls Matter
- Application-layer validation cannot reliably enforce network reach.
- Network egress controls can enforce where a service is allowed to talk, even if the app is vulnerable.
- Strong designs assume SSRF is possible and limit blast radius accordingly.

Reviewer questions:
- What outbound destinations does this service truly need?
- What prevents access to internal networks and control planes if the app is compromised?
- Is outbound traffic constrained by policy, not just code?

---

## Metadata Service Risk (Cloud Conceptual)
- Cloud metadata services exist to provide identity and credentials to legitimate infrastructure.
- They assume callers are trusted infrastructure on trusted network paths.
- SSRF turns an application flaw into a credential event by making the server request identity on the attacker’s behalf.

Key insight:
- SSRF + metadata is server identity theft, not simple data exposure.

---

## Why SSRF Is a Design Flaw
- SSRF persists because servers are granted network reach and identity without intent enforcement.
- Input validation alone cannot solve SSRF because the danger comes from trusted execution context.
- Effective SSRF prevention requires architectural controls, not just code fixes.

---

## One Realistic Cloud Breach Chain
1. A feature allows user-influenced server-side URL fetching.
2. User input affects the outbound request destination.
3. The server makes requests from a trusted network position.
4. A cloud identity surface is reached.
5. Credentials or tokens are issued to the server.
6. Actions occur with server authority, not user authority.

Design takeaway:
- SSRF becomes critical when it can reach identity surfaces and when permissions are broad.
