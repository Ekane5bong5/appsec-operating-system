# AI Security Mental Model — LLMs as Trust Amplifiers

Large Language Models (LLMs) should not be treated as features or assistants.
They are **probabilistic decision-making components** that operate inside application trust boundaries.

This document establishes a precise mental model for reasoning about AI security
from an Application Security perspective.

---

## Core Reframe

**LLMs do not create new privileges.  
They amplify whatever trust, access, and authority the system already grants them.**

Most AI security failures are therefore **system design failures**, not model failures.

---

## Why AI Systems Are Different From Traditional Applications

Traditional applications:
- Execute deterministic logic written by engineers
- Follow predefined code paths
- Can be reviewed and tested at build time

LLM-powered applications:
- Make decisions at runtime based on probabilistic reasoning
- Assemble behavior from prompts, context, retrieved data, and tool outputs
- Generate logic, queries, or actions that may not exist in source code
- Can produce different outcomes for identical inputs

**Security impact:**  
Critical behavior may emerge only at runtime, beyond the reach of traditional SAST and static review.

---

## What Prompt Injection Actually Exploits

Prompt injection is **not** a parsing or input validation bug.

It exploits a **trust boundary failure** where:
- Untrusted input is allowed to influence trusted reasoning
- The model does not distinguish between “instructions” and “data”

Key points:
- Prompt injection is semantic, not syntactic
- Jailbreaking is a subset of prompt injection
- Human-readable content is not required — only model-parsed content
- Indirect prompt injection can occur via retrieved documents, tool output, or multimodal input

**Mental mapping:**
- Prompt injection ≈ semantic request smuggling
- System prompt ≈ implicit policy layer
- Context assembly ≈ request reconstruction

---

## How Trust Boundaries Blur in AI Systems

LLM reasoning commonly merges multiple sources into a single context:
- System instructions
- Developer rules
- Retrieved documents (RAG)
- Tool or API responses
- Conversation history
- Direct user input

If these sources are not clearly separated and governed:
- Untrusted data can influence trusted decisions
- Attackers can manipulate behavior without breaking functionality
- Vulnerabilities resemble business logic abuse rather than classic exploits

---

## Tool Invocation and Excessive Agency

LLMs become dangerous when they are allowed to:
- Choose which tools to invoke
- Sequence actions across systems
- Execute workflows with insufficient mediation

**Excessive Agency occurs when:**
- Functionality is too broad
- Permissions are too powerful
- Autonomy is too high

This mirrors classic AppSec failures:
- Confused deputy
- Mass assignment
- Missing authorization enforcement

**Critical rule:**  
Authorization must be enforced by downstream systems — never by the model.

---

## System Prompt Leakage: Symptom vs Root Cause

System prompts should **never** be treated as secrets or security controls.

If leaking a system prompt causes compromise, the real issues are:
- Secrets embedded in prompts
- Authorization logic described instead of enforced
- Business rules implemented as text rather than code

Disclosure of prompt content is rarely the core risk.
The risk lies in **delegating security decisions to the model**.

---

## Data and Model Poisoning as Supply Chain Risk

Data poisoning is an integrity attack that affects:
- Pre-training data
- Fine-tuning datasets
- Embedding sources
- Shared or third-party models

This maps directly to:
- Dependency poisoning
- CI/CD compromise
- Malicious packages
- Runtime configuration manipulation

Backdoors may remain dormant until triggered, making detection difficult.

---

## Why Traditional AppSec Tooling Falls Short

SAST limitations:
- Assumes logic is deterministic
- Cannot analyze runtime-generated behavior
- Does not reason about prompts or context

DAST limitations:
- Misses semantic manipulation
- Fails to detect multi-step reasoning abuse
- Cannot infer intent from natural language

**Effective AI security focuses on:**
- Trust boundaries
- Authorization enforcement
- Context control
- Tool permission minimization
- Runtime monitoring and mediation

---

## Key Takeaway

LLM-powered applications expand the attack surface by introducing:
- Non-deterministic behavior
- Runtime-generated logic
- Semantic trust failures

**AI security is not a new discipline.  
It is AppSec applied to systems that decide instead of execute.**
