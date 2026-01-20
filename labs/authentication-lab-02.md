## Authentication Lab 02 — Username Enumeration via Response Timing

### Lab Completed
**Username enumeration via response timing**

The goal of this lab was to determine whether identity-related information could be inferred from authentication behavior even when error messages and response content were uniform.

---

### High-Level Observation

The application returned identical error messages, HTTP status codes, and response lengths for failed authentication attempts. However, authentication requests involving valid usernames consistently took longer to process than requests involving invalid usernames.

These timing differences allowed valid usernames to be enumerated without triggering explicit errors or alerts.

---

### What Assumption Did the Application Make?

The application assumed that differences in server-side processing time during authentication were not observable or actionable by an attacker.

It further assumed that allowing username existence to influence authentication processing paths was safe, as long as the user-facing responses appeared identical.

---

### What Signal Leaked Information?

The application leaked identity information through **consistent response timing differences**.

Authentication attempts involving valid usernames took measurably longer to complete than attempts involving invalid usernames. This difference became more pronounced when longer passwords were supplied, as password processing occurred only when the username existed.

---

### What State Was Trusted Incorrectly?

The application treated username existence as an internal-only concern, but allowed it to influence authentication execution paths before identity was fully verified.

As a result, the system partially validated identity prior to successful authentication, enabling attackers to infer whether an account existed based on observable behavior.

Additionally, the application trusted the X-Forwarded-For header to identify the source of authentication attempts, allowing lockout controls to be bypassed by rotating header values.

---

### Why a WAF Wouldn’t Prevent This

A WAF would not prevent this issue because the requests were valid login attempts containing no malicious payloads or abnormal syntax.

The vulnerability exists in the application’s authentication logic and trust assumptions, not in request content. Timing-based identity inference falls outside the detection model of most WAFs.

---

### What Control Would Actually Fix It

Authentication failures should follow equivalent execution paths regardless of whether a username exists, minimizing observable timing differences.

Lockout and throttling mechanisms should be tied to a combination of account, session, and client characteristics rather than trusting client-controlled headers. These controls reduce the feasibility of large-scale enumeration but do not replace proper authentication flow design.
