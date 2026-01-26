# Server-Side Template Injection — Detection & Evaluation Summary

## Purpose
This artifact consolidates multiple Server-Side Template Injection (SSTI) labs into a single reviewer-friendly reference.

The focus is not exploitation, but **proving when user input transitions from data to executable logic** during server-side rendering.

This document links to deeper lab artifacts that each capture a distinct SSTI primitive.

---

## What SSTI Fundamentally Is
Server-Side Template Injection occurs when user-controlled input is **evaluated by a server-side template engine** instead of being rendered as inert data.

The core failure is not escaping — it is **allowing user input to reach a template evaluation context**.

---

## How SSTI Was Detected Across Labs
Across all labs, SSTI was confirmed by observing **deterministic server-side behavior changes**, including:
- Arithmetic or expressions being evaluated instead of rendered literally
- Template engine–specific error messages
- Framework-specific rendering behavior
- Server-side state or configuration becoming observable

No client-side execution was required to prove impact.

---

## Distinct SSTI Primitives Observed

### 1) Evaluation Proof (Baseline SSTI)
**Artifact:** `ssti-basic-evaluation-proof.md`

- User input was evaluated as template logic.
- Demonstrated that data had crossed into execution.
- Key insight: *evaluation alone is already a security boundary violation*.

---

### 2) Code Context Evaluation
**Artifact:** `ssti-code-context-evaluation.md`

- User input was evaluated inside a live application rendering path.
- Execution context inherited server permissions during template rendering.
- Key insight: *logic inside templates magnifies impact beyond reflection*.

---

### 3) Object Exposure & Information Disclosure
**Artifact:** `ssti-object-exposure-secret-leak.md`

- User-modifiable templates exposed privileged framework objects.
- Sensitive configuration data became accessible.
- Key insight: *template context equals trust — exposing it is a design flaw*.

---

## Why SSTI Is a Design-Level Vulnerability
SSTI is not caused by bad payload filtering.

It is caused by:
- Mixing **template logic** and **user data**
- Allowing user input to influence **template structure**
- Treating rendering as “presentation-only” rather than execution

Once evaluation is possible, exploitation is often trivial.

---

## Primary Preventive Principle
> Templates must be treated as **code**, not content.

Effective prevention requires:
- Immutable templates
- Logic-less rendering where possible
- Strict separation of data and evaluation
- No user-controlled influence over template syntax or context

---

## Interview-Ready One-Liner
Server-side template injection happens when user input is evaluated during rendering, allowing data to become executable logic inside the server’s runtime context.
