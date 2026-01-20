## Authentication Lab 03 — Password Reset Broken Logic

### Lab Completed
**Password reset broken logic**

The goal of this lab was to analyze how identity is re-established during a password reset flow and determine whether the application correctly validated that the requester was authorized to reset the target account.

---

### High-Level Observation

The application allowed a user to reset the password for any account by supplying an arbitrary reset token, as long as the same token value was provided consistently in the request. The application did not verify that the token was legitimate, unexpired, or bound to the user whose password was being reset.

This allowed full account takeover without knowing the victim’s password or completing any authentication checks.

---

### What Assumption Did the Application Make?

The application assumed that if a password reset request contained a matching token value in multiple locations, the token must be valid and the requester must be authorized to reset the account.

It further assumed that reaching the password reset form implied sufficient proof of identity.

---

### What Signal Leaked or Logic Failed?

This vulnerability did not rely on information leakage through responses. Instead, it relied on a **logic failure** where the application trusted user-supplied reset tokens without validating them server-side.

The application compared token values for equality rather than verifying that the token was:
- issued by the server
- bound to a specific user
- time-limited
- single-use

---

### What State Was Trusted Incorrectly?

The application trusted the password reset token as proof of identity without validating its authenticity or association with the target user.

As a result, identity was re-bound incorrectly during account recovery, allowing an attacker to assert ownership of another user’s account by choosing both the token value and the username.

---

### Why a WAF Wouldn’t Prevent This

A WAF would not prevent this issue because the requests were valid password reset submissions with no malicious payloads or anomalous syntax.

The vulnerability exists entirely in application logic and trust assumptions during the authentication recovery process, which is outside the scope of signature-based or request-pattern defenses.

---

### What Control Would Actually Fix It

Password reset tokens must be generated server-side, stored securely, and strictly bound to a specific user, expiration time, and single use.

The application should validate that the reset token:
- exists
- has not expired
- has not been used
- is associated with the account being reset

Completing a password reset should also invalidate existing authentication state and require re-authentication, ensuring that trust is fully re-established rather than assumed.
