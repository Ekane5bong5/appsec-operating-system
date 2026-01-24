# SSRF Mental Model

## What SSRF Breaks Conceptually
- SSRF breaks the assumption that backend-initiated requests are inherently safe.
- It violates the idea that input validation at the edge is sufficient once the server makes a request.
- It turns internal trust relationships into an attack surface.
- SSRF is fundamentally a failure of trust delegation, not a failure of URL parsing.

## Why the Server Is a More Powerful Attacker Than the User
- Backend systems operate from trusted network locations.
- Internal requests often bypass authentication and authorization checks.
- Servers inherit permissions intended for infrastructure, not end users.
- Cloud environments grant additional capabilities to trusted server identities.

## Trust Zones and Call Chains
- Modern applications operate across multiple trust zones.
- Each hop in a request chain typically increases trust and authority.
- Validation and scrutiny decrease as trust increases.
- SSRF exploits the point where user intent is no longer re-verified.

Typical call chain:
- User → Web Application → Internal Service → Cloud or Control Plane

## What a Trust Boundary Actually Is
- A trust boundary is the point where assumptions change without re-verification.
- It is not defined by frontend or backend code, but by shifts in identity and authority.
- SSRF occurs when user-controlled input crosses a boundary and is used to initiate a trusted server-side request.

## Where SSRF Is Introduced vs Where It Pays Off
- SSRF is introduced when an application uses user-influenced input to make a server-side request.
- SSRF becomes critical when downstream systems trust that request based on origin rather than intent.
- Cloud metadata and control planes amplify SSRF impact because they assume trusted infrastructure callers.

## Final Mental Model
- SSRF is not a web input vulnerability.
- It is an authorization and trust boundary failure where the server becomes the attacker.
