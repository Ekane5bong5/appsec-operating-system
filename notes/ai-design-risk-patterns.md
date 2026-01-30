# AI Design Risk Patterns (Reviewer Lens)

## Purpose

This document captures **design-level security risk patterns specific to AI-powered systems**, with emphasis on:

- Retrieval-Augmented Generation (RAG)
- Tool / function calling
- MCP-based integrations
- Agentic workflows

It is intended for **architecture and design reviews**, not implementation audits.

If a risk cannot be identified before code exists, it belongs here.

---

## Mental Model: Why AI Systems Break Differently

Traditional applications:
- Inputs flow through deterministic code paths
- Authorization is explicit and enforced in services
- Security failures are observable through errors or violations

AI-powered systems:
- Inputs are merged into layered context
- Logic is constructed dynamically at runtime
- Decisions are made probabilistically rather than deterministically

Security controls that rely on explicit code paths, static analysis, or predictable execution order do not map cleanly to these systems.

**Key shift:**  
Security decisions move from code into retrieval, ranking, reasoning, and orchestration layers that were not designed to enforce policy.

---

## Core Design Risk Patterns

### 1. Implicit Authorization via Retrieval Logic (RAG)

**Hidden assumption**  
Relevant data is safe data.

**Why it fails**  
Retrieval systems optimize for semantic similarity, not entitlement.  
Similarity does not imply permission.

Prompt content, ranking behavior, or agent reasoning can expand retrieval scope beyond what authorization models intended.

**Failure mode**
- Partial disclosure of sensitive internal data
- Cross-tenant or cross-role synthesis
- Sensitive facts reconstructed from multiple benign sources

**Why scanners miss it**
- No unauthorized API call
- No single sensitive record returned
- Output is derived, not directly retrieved

**Reviewer pushback**
- Where is the last explicit authorization check before retrieval?
- Is retrieval scoped by identity, tenant, and purpose?
- Can prompt content influence retrieval scope?
- How is synthesis across documents with different sensitivity levels prevented?

---

### 2. Trust Boundary Collapse in Layered Context

**Hidden assumption**  
Once inside the prompt, all context is equivalent.

**Why it fails**  
AI systems merge system instructions, developer rules, retrieved data, tool outputs, conversation history, and user input without enforcing trust hierarchies.

Models reason over meaning, not authority.

**Failure mode**
- Lower-trust context influences higher-trust decisions
- Policies applied inconsistently
- Indirect prompt manipulation without classic injection

**Why scanners miss it**
- No malformed input
- No exploit string
- Behavior appears valid and functional

**Reviewer pushback**
- Which context layers are authoritative?
- How is instruction precedence enforced outside the model?
- Can untrusted context override or dilute trusted rules?

---

### 3. Orchestration Without Governance (Agents + Tools)

**Hidden assumption**  
The model will call tools responsibly.

**Why it fails**  
Models optimize task completion, not safety.  
Tool selection and sequencing are part of the model’s reasoning process.

Over-permissive tools turn the model into an ungoverned orchestration layer.

**Failure mode**
- Unauthorized or unintended API actions
- Workflow steps skipped or reordered
- Sensitive operations triggered indirectly

**Why scanners miss it**
- Tools are legitimate
- APIs are authorized
- Calls appear valid in isolation

**Reviewer pushback**
- What decisions is the model allowed to make autonomously?
- How are tools scoped by capability, context, and identity?
- Can the model skip approval or validation steps?

---

### 4. Dynamic Logic That Never Exists at Rest

**Hidden assumption**  
If it’s not in the repository, it’s not part of the attack surface.

**Why it fails**  
AI systems dynamically generate:
- SQL queries
- API payloads
- Configuration changes
- Workflow logic
- Executable code

These artifacts often exist only transiently at runtime.

**Failure mode**
- Unreviewed logic executes in production
- Policy enforcement bypassed indirectly
- Incomplete auditability

**Why scanners miss it**
- No static artifact to analyze
- No deterministic output
- Logic is ephemeral

**Reviewer pushback**
- What constraints exist on runtime-generated logic?
- How is it audited?
- How are unsafe generations detected or prevented?

---

### 5. Logic Abuse Without Failure Signals

**Hidden assumption**  
If nothing breaks, nothing is wrong.

**Why it fails**  
LLMs can:
- Misinterpret business rules
- Apply policies inconsistently
- Skip or reorder steps
- Still produce plausible output

The system behaves as designed, but not as intended.

**Failure mode**
- Silent policy violations
- Business logic abuse
- Gradual trust erosion

**Why scanners miss it**
- No exception
- No anomaly
- No protocol-level violation

**Reviewer pushback**
- What defines correct behavior?
- How is it enforced?
- How do you detect “almost right” outcomes?

---

## Non-Obvious Reviewer Traps

- “We filtered the documents” ≠ authorization
- “The model is instructed not to do X” ≠ enforcement
- “We log tool calls” ≠ understanding intent
- “It’s just an assistant” ≠ low impact

If the model can act, it is part of the control plane.

---

## Anchor Insight

**AI systems fail by doing the wrong thing correctly.**

That is why security review must move upstream into design.
