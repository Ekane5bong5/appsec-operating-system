# Auth + Session Review Checklist (v1)
*(Reviewer mindset: “What trust is being granted, and how can it be stolen or outlived?”)*

This checklist is for reviewing authentication and session management designs, implementations, and changes.
It focuses on how identity is established, how trust persists, and how that trust is revoked.

---

## 1) Identity: can identity be guessed or enumerated?

### Username / account enumeration
- Do login, signup, password reset, and account recovery responses look identical for:
  - user exists vs user does not exist?
  - account locked vs active?
  - password incorrect vs MFA required?
- Are HTTP status codes consistent (no “silent” 200 vs 401/403 differences)?
- Are response sizes and redirect behavior consistent?
- Is response timing consistent (no measurable delay differences that leak “user exists”)?
- Are error messages generic and consistent across:
  - UI messages
  - API responses
  - email flows
  - logs exposed to users/admin portals

### Brute force & credential stuffing resistance
- Is there account-based rate limiting / throttling (not just IP-based)?
- Is there protection against:
  - password spraying (one password across many accounts)?
  - credential stuffing (known leaked pairs)?
- Is lockout policy designed to avoid denial-of-service (attacker locking out victims)?
- If X-Forwarded-For is used, is it validated/trusted only behind known proxies?

### Password reset and recovery: identity proofing strength
- Is password reset tied to proof of control (email/phone) plus additional checks for risky cases?
- Are reset links:
  - single-use?
  - time-limited?
  - bound to the intended account?
- Does the system prevent reset token replay?
- Are recovery flows protected from enumeration (“If that email exists…” style responses)?

---

## 2) Session: can session state be reused?

### Session credential basics
- Treat the session ID/token as a bearer credential: does the design assume possession = user?
- Is the session ID generated with strong unpredictability (no guessable structure)?
- Is the session ID value meaningless (no embedded user ID, role, or PII)?

### Session transport and acceptance
- Is the session credential exchanged only via the intended mechanism?
  - Prefer: cookies
  - Avoid: URL parameters, hidden form fields, custom headers unless explicitly designed
- Does the server reject session IDs delivered via “unexpected” channels (fixation defense)?

### Pre-auth vs post-auth sessions (state transition)
- Is the session ID rotated on authentication (login)?
- Is the pre-auth session prevented from becoming an authenticated session?
- If multiple cookies exist (pre-auth + post-auth), does the app validate the correct set and their relationship?

### Cookie hygiene (necessary but not sufficient)
- Are session cookies set with Secure, HttpOnly, and appropriate SameSite?
- Is HTTPS enforced for the full session (not only login)?
- Is HSTS enabled where applicable?

---

## 3) Lifecycle: what happens on password reset?

This is where a lot of real breaches persist.

- Does password reset invalidate:
  - the current session?
  - all other active sessions?
  - refresh tokens / “remember me” tokens?
- Are privileged sessions (admin/support) forced to reauthenticate after password change/reset?
- Does the system rotate session credentials after:
  - password reset
  - email change
  - MFA enrollment change
  - account recovery completion

---

## 4) Lifecycle: what happens after logout?

Logout must revoke trust server-side, not just delete cookies client-side.

- Does logout invalidate the session server-side?
- Does logout revoke all relevant credentials?
  - session cookies
  - access tokens
  - refresh tokens
  - “remember me” tokens
- Is logout “global” (all devices) available for higher-risk applications?
- Are sensitive pages protected from browser caching after logout (no back-button data exposure)?
- - If a valid session is replayed or reconstructed, what actions remain possible?
- Are identity-changing actions protected against session replay?
- Does possession of a valid session allow modification of account recovery attributes?


---

## 5) Expiration & persistence: how long can stolen trust live?

- Is there an idle timeout appropriate to the risk level?
- Is there an absolute timeout (even with activity)?
- Is there periodic renewal/rotation for long-lived sessions?
- Are sessions forced to reauthenticate after risk events (step-up auth), such as:
  - new device
  - new location/IP anomaly
  - account recovery
  - sensitive actions (password/email/payment changes)

---

## 6) Trust assumed but never verified (the reviewer’s kill-shot questions)

Use these to find design-level vulnerabilities quickly.

- What does the system treat as “proof of identity” besides primary authentication?
  - possession of a session token?
  - possession of an email inbox?
  - presence of a cookie?
- Where does the system “branch” internally (creating behavioral oracles)?
  - user exists vs not
  - locked vs active
  - MFA enrolled vs not
  - reset token valid vs invalid
- What state is persisted and later trusted without re-checking?
  - “isAuthenticated” flags
  - user role claims
  - account recovery state
  - MFA completion state
- What happens if an attacker replays:
  - an old session token?
  - a password reset request with a modified username?
  - a logout request out of order?
- Where could a developer accidentally validate the wrong thing?
  - comparing two attacker-controlled values (token == token) instead of validating token server-side
  - trusting client-side timestamps for expiry
  - accepting X-Forwarded-For from the public internet

---

## 7) WAF reality check (what it can and cannot fix)

- A WAF can enforce cookie flags and basic hygiene.
- A WAF cannot reliably fix:
  - broken reset logic
  - missing session invalidation
  - weak state transitions (pre-auth to post-auth)
  - token binding mistakes
  - authorization errors

If the vulnerability is “the application trusts the wrong state,” the fix is in the application.

---

## 8) Evidence to ask for (design review artifacts)

- Auth flow diagram (login, MFA, reset, recovery)
- Session lifecycle diagram (creation, rotation, expiry, invalidation)
- Threat model notes for:
  - enumeration
  - replay/fixation
  - credential stuffing
  - session persistence after reset/logout
- Logging/monitoring plan for session anomalies (without logging raw session IDs)

- 

---

## Versioning

This is v1.
