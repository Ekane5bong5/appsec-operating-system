# Authentication Mental Model  
*(How Authentication Actually Breaks in Real Systems)*

## What Authentication Is — and What It Is Not

Authentication exists to answer one question:

**“Is this request really being made by the identity it claims to be made by, right now?”**

Authentication does not decide what a user is allowed to do.  
It does not permanently prove identity.  
It does not create trust that should last forever.

A system often creates a digital identity for a user very early (for example, at sign-up), before that identity has been strongly proven.  
Later, after a user authenticates, systems often keep trusting that identity for too long.

Authentication failures commonly live in this gap:
- trusting identity **too early**
- trusting identity **for too long**

---

## Authentication vs Authorization (In Practice)

Authentication proves *who you are*.  
Authorization controls *what you are allowed to do*.

In real systems, authentication fails when identity is inferred incorrectly or reused beyond its safe boundary. Authorization fails when permissions are misapplied to an already authenticated identity.

A common failure pattern is allowing sensitive or identity-changing actions based only on an existing session. This is an authentication failure, because the system fails to re-prove identity at a critical moment.

---

## How Broken Authentication Appears Before Exploitation

Broken authentication usually does not appear as an error or exploit.

It appears as **different behavior** based on identity-related input.

Examples include:
- Login attempts for real users taking longer to fail than attempts for non-existent users
- Different HTTP status codes for similar authentication failures
- MFA, CAPTCHA, or lockout logic triggering only for valid accounts
- Redirects or state changes that differ based on account status

Even when error messages are generic, behavior may not be.

---

## Behavioral Oracles

A behavioral oracle exists when an attacker can learn identity-related information by observing how the system behaves rather than what it explicitly says.

These oracles exist because authentication logic almost always branches internally:
- user exists vs does not exist
- account locked vs active
- password correct vs incorrect

Even if the final response looks identical, the system may:
- perform more work
- take longer to respond
- trigger additional controls
- change session state

Attackers observe these differences to infer identity information.

---

## Authentication Can Fail Without Errors

Authentication does not need to throw errors to be broken.

A system can return the same message and still leak identity through:
- timing differences
- HTTP response codes
- redirects
- session state changes
- secondary controls like MFA or CAPTCHA

Security validation must focus on **what the system does**, not just what it says.

---

## What Attackers Learn From Authentication Behavior Alone

Without guessing passwords, attackers can often determine:
- which usernames or emails exist
- which accounts are active or locked
- which users trigger MFA
- which identities are higher value

This enables targeted attacks such as credential stuffing, phishing, and account takeover.

Authentication failure often enables exploitation rather than being the exploit itself.

---

## Why Automated Scanners Struggle With Authentication

Authentication logic is stateful and context-dependent. Automated tools assume stateless requests and obvious failures.

While scanners can catch some issues, they often miss:
- timing-based identity leaks
- missing re-authentication boundaries
- identity lifecycle mistakes
- risk-based authentication inconsistencies

Authentication validation requires human judgment.

---

## Re-Authentication as an Identity Boundary

Authentication should be re-performed when identity trust meaningfully changes.

This includes:
- changing passwords or email addresses
- modifying payment or recovery details
- performing high-risk actions
- after account recovery or suspicious activity

Re-authentication protects against session hijacking, CSRF, and silent account takeover.

If identity is not re-proven at these moments, authentication has failed by design.

---

## Common Developer Mistake

The most common mistake is treating authentication as a one-time event.

Once a user logs in, their identity is trusted indefinitely, even for sensitive actions. This turns temporary access or session theft into full account compromise.

---

## Most Dangerous AppSec Miss

The most dangerous authentication failures are quiet.

They produce no errors, no alerts, and no obvious exploitation, yet allow identity inference and targeted attacks through behavior alone.

---

## How a Real AppSec Engineer Validates Authentication Findings

Authentication validation starts by observing behavior.

Key questions include:
- Does the system behave the same for all identity states?
- Are identity boundaries enforced consistently?
- Is identity re-proven before sensitive actions?
- Can identity be inferred through timing or control flow?

Authentication issues are validated through behavior, not payloads.

---

## Calibration Check

Authentication can be broken even when passwords are strong, MFA is enabled, and error messages are generic.

If identity can still be inferred through behavior, timing, or missing re-authentication boundaries, authentication is broken.

Quiet failures are the real failures.
