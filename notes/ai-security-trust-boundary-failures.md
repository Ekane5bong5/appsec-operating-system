# AI Security — Trust Boundary & Authority Failures in LLM Systems

## Purpose

Capture the core security failure mode observed across LLM systems:
**LLMs are treated as trusted decision-makers instead of untrusted intermediaries.**

This artifact consolidates multiple AI security labs into a single senior-level mental model
focused on architecture, trust boundaries, and authorization — not prompt gimmicks.

---

## Core Failure Theme

> LLM vulnerabilities are rarely about the prompt.
> They are about **what the system allows the model to decide and execute**.

Across all labs, exploitation succeeded because:
- authority was delegated to the model
- trust boundaries were implicit
- authorization was assumed instead of enforced

---

## Mental Model Shift

Traditional AppSec assumes:
- deterministic logic
- fixed execution paths
- explicit authorization in code

LLM-backed systems break these assumptions.

LLMs:
- interpret meaning, not structure
- operate probabilistically
- assemble context dynamically
- influence runtime decision-making
- often trigger actions across systems

**Trust becomes the primary attack surface.**

---

## Case Study 1 — Excessive Agency (Direct Authority Abuse)

### Scenario Summary

An LLM-backed chat interface exposes a Debug SQL API capable of executing raw SQL.
Through natural language interaction, the model is instructed to delete a user.

The model complies.

---

### What Altered System Behavior

Not the SQL syntax.

The system behavior changed because:
- the LLM was trusted to decide if the action was valid
- the SQL tool executed with elevated privileges
- no authorization boundary existed between chat input and destructive actions

---

### Trust Assumption Violated

> “If the request comes through the LLM, it must be allowed.”

This mirrors classic internal-trust failures:
- authenticated ≠ authorized
- internal service == trusted
- policy delegated instead of enforced

---

### Why This Is Not “Just a Prompt Issue”

A prompt cannot delete data by itself.

The failure required:
- a privileged execution tool
- missing authorization controls
- a system that let the model trigger side effects

This is an **access control failure with a natural-language trigger**.

---

### AppSec Equivalent

- Broken Object Level Authorization (BOLA)
- Excessive privilege
- Server-side action execution without mediation

Functionally:
> the LLM is acting as a microservice with root permissions

---

### Risk-Reducing Controls

- Remove destructive capabilities from LLM tools
- Enforce authorization **after** model output
- Require backend approval for high-risk actions
- Treat the LLM as an untrusted caller, not a policy engine

---

## Case Study 2 — Indirect Prompt Injection (Context Abuse)

### Scenario Summary

An attacker injects hidden instructions into stored content (product reviews).
The LLM later reads this content while responding to a user.

The injected instructions cause the model to delete the active user account.

---

### What Altered System Behavior

The model did not receive a new user command.

It received **poisoned context**.

The system failed to distinguish:
- instruction vs data
- user input vs stored content
- authority vs influence

---

### Trust Assumption Violated

> “Stored content is safe to reuse as instructions.”

This is a **context desynchronization problem**, similar to:
- HTTP request smuggling
- multi-parser inconsistencies
- header/body confusion

---

### Why This Is Not “Just a Prompt Issue”

The attacker never directly instructed the model.

They:
- injected instructions into a data channel
- relied on the system to replay that data as context
- exploited implicit trust between context sources

This is **semantic injection**, not string injection.

---

### AppSec Equivalent

- Stored XSS (logic-level)
- Trust boundary confusion
- Business logic abuse
- Multi-source desync vulnerabilities

---

### Risk-Reducing Controls

- Strict separation of:
  - system instructions
  - user input
  - retrieved/stored data
- Label and sanitize untrusted context
- Never allow context to invoke actions directly
- Enforce complete mediation downstream

---

## Unified Insight

Despite different techniques, both failures share the same root cause:

> The system allowed the LLM to act instead of advise.

Common failures:
- excessive authority
- implicit trust
- missing authorization checks
- blurred trust boundaries

---

## Architectural Reframe (Key Takeaway)

> **LLMs should be treated as untrusted microservices.**

They should:
- propose actions, not execute them
- operate under least privilege
- be audited like any other service
- never own policy or authorization decisions

This framing aligns LLM security with established AppSec principles.

---

## Why This Scales Poorly in Enterprises

At scale:
- context sources multiply (RAG, tools, logs, users)
- models gain more agency
- actions affect real systems
- failures appear legitimate in logs

Without enforced boundaries:
- abuse looks like normal behavior
- detection becomes extremely difficult
- blast radius expands silently

---

## Reviewer / Interview Lens

When reviewing an LLM-backed system, ask:

- What actions can the model trigger?
- Where is authorization actually enforced?
- Which context sources are trusted?
- Can stored data influence execution?
- What happens when the model is wrong?

If these answers are unclear, the system is unsafe by default.

---

## Final Conclusion

These labs are not about clever prompts.

They demonstrate a fundamental shift:
> Security failures now emerge from **how systems reason**, not just how they parse.

LLMs amplify traditional AppSec mistakes.
The fix is not better prompts.

The fix is **better architecture**.
