# SSRF Review Checklist (AppSec OS)

## Purpose
This checklist is used to identify Server-Side Request Forgery (SSRF) risk during architecture reviews, code reviews, and incident analysis.

It focuses on trust boundaries, server identity, and outbound request behavior rather than payloads or bypass techniques.

---

## 1. SSRF Entry Points
- Does the server make outbound network requests as part of its functionality?
- Can user-controlled input influence any part of the outbound request destination (host, scheme, path, headers)?
- Are outbound requests triggered by features such as:
  - URL fetching or previews
  - Webhooks or callbacks
  - PDF/image rendering
  - Integrations or connectors
  - Analytics or logging behavior

---

## 2. Destination Control & Policy
- Are outbound destinations explicitly allow-listed?
- If allow-lists are not feasible, is this acknowledged as a design risk?
- Is destination policy enforced at request time (not just input validation time)?
- Are redirects followed, and if so, is policy re-applied after redirects?
- Is DNS resolution consistent with destination policy?

---

## 3. Validation Assumptions
- Is URL or input “sanitization” being relied on as a primary control?
- Is validation limited to format rather than destination intent?
- Could parsing differences, redirects, or DNS resolution change the effective destination?

---

## 4. Internal Network & Metadata Exposure
- Can the service reach internal networks from its runtime environment?
- Can the service reach cloud metadata or identity endpoints?
- If metadata is reachable, what credentials or identity could be obtained?
- Is access to identity surfaces considered in the threat model?

---

## 5. Server Identity & Authority
- What identity does the server have on the network?
- What permissions does the server inherit that users do not have?
- Are internal services trusting the server based solely on network origin?
- Is authentication or authorization enforced independently by internal services?

---

## 6. Network-Level Controls
- Are outbound requests constrained by network egress rules or proxies?
- If the application is compromised, what destinations remain reachable?
- Is SSRF blast radius limited even if application-layer controls fail?

---

## 7. Detection & Visibility
- Are server-initiated outbound requests logged?
- Would blind SSRF be detectable through logs, telemetry, or network monitoring?
- Is outbound traffic reviewed during incident response?

---

## Final Reviewer Judgment
- Is SSRF risk acknowledged as a design concern?
- If SSRF occurs, is the impact limited by architecture?
- Does the design rely on trust assumptions that are not explicitly enforced?

If multiple answers are “Unknown,” SSRF risk has not been adequately reviewed.
