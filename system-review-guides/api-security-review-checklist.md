# API Security Review Checklist

Purpose: Make API authorization, data exposure, and trust review repeatable.

Use this checklist when reviewing:
- API specifications (OpenAPI / Swagger)
- API implementations
- Microservice-to-microservice calls
- API gateway + service boundaries

---

## Authorization & Access Control

- Is authorization enforced **per object**, not just per endpoint?
- Does the API verify ownership for every object identifier it accepts?
- Are privileged actions (create, update, delete) restricted by role or capability?
- Is authorization enforced inside the service, not only at the gateway?
- Can HTTP method changes (GET → PATCH / DELETE) expose new functionality?

---

## Data Exposure

- Are API responses filtered based on the caller’s permissions?
- Does the API return full objects when only partial data is required?
- Are sensitive fields (roles, flags, pricing, internal IDs) ever returned?
- Is the client trusted to hide data rather than the server enforcing it?

---

## Input & Object Mapping

- Are request bodies mapped directly to internal domain objects?
- Can clients set fields that should only be server-controlled?
- Are allow-lists used for writable fields?
- Could mass assignment modify business-critical properties?

---

## Trust Boundaries & Service Interaction

- What trust is inherited from upstream services?
- Does the service assume upstream authorization was sufficient?
- Are internal APIs protected the same way as external APIs?
- Are internal headers or tokens blindly trusted?
- What happens if this service is called directly?

---

## API Design & Governance

- Are APIs versioned, and are older versions still reachable?
- Are undocumented or deprecated endpoints still live?
- Is “internal API” used as a justification instead of a control?
- Would this API still be safe if exposed unintentionally?

---

## Reviewer Conclusion

If the API were called directly by an attacker:
- What would break?
- What assumptions would fail?
- What authorization checks are missing by design?

If the answer is unclear, the API is unsafe by default.
