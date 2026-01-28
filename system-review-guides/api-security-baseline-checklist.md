# API Security Baseline Checklist

Purpose: Establish minimum expected security controls for API platforms.

This checklist represents foundational hygiene.
Passing it does NOT mean an API is secure.

---

## Authentication & Authorization

- Are APIs protected by strong authentication (OAuth2 / OIDC)?
- Are API keys stored securely (no hard-coded secrets)?
- Is role-based or attribute-based access control implemented?

---

## Encryption

- Is HTTPS enforced for all API endpoints?
- Is sensitive data encrypted at rest?

---

## Input Validation & Output Handling

- Are inputs validated against expected schemas?
- Are outputs sanitized to avoid information leakage?

---

## Rate Limiting & Abuse Prevention

- Are per-user or per-IP rate limits enforced?
- Are request quotas defined?

---

## Logging & Monitoring

- Are API requests logged with identity, action, and source?
- Are alerts configured for suspicious behavior?

---

## Error Handling

- Are error messages generic and non-revealing?
- Are stack traces and internal details hidden?

---

## Endpoint & Data Exposure

- Is returned data minimized?
- Are all endpoints (including hidden or deprecated ones) protected?

---

## Versioning & Lifecycle

- Are APIs versioned explicitly?
- Are old versions monitored and retired?

---

## CORS & Browser Access

- Are CORS policies explicitly defined where applicable?

---

## Dependency & Supply Chain

- Are dependencies kept up to date?
- Are dependency scanners in use?

---

## Penetration Testing

- Are APIs tested regularly for logical vulnerabilities?
- Are findings tracked and remediated?

---

## Documentation & Training

- Is API documentation accurate and up to date?
- Are teams trained on secure API usage?

---

## Resilience & Availability

- Are backups performed and tested?
- Are DoS protections in place?

---

## API Gateway Usage

- Is an API gateway used for common controls?
- Is it understood that gateways do NOT enforce business logic?

---

## Core Reminder

This checklist measures **baseline maturity**, not real-world safety.

Most API breaches occur even when all of the above are “checked.”
