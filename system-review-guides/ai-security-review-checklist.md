# AI Security Review Checklist (AppSec OS)

## Purpose

This checklist exists to make **AI security design reviews concrete and repeatable**.

It is not a compliance list.
It is not exhaustive.
It is a reviewer’s forcing function.

If a question cannot be answered clearly, the design is not ready.

This checklist is expected to evolve rapidly.

---

## 1. Model Inputs & Influence

Understand **everything that shapes model behavior**.

- What direct user inputs influence the model?
- What indirect inputs exist (conversation history, memory, profiles)?
- What system or developer prompts are used?
- Can user-controlled input influence:
  - Instructions?
  - Tool selection?
  - Retrieval scope?
- Are there inputs the team assumes are “safe” but has not threat-modeled?

Reviewer note:  
If you cannot enumerate all inputs, you do not control the system.

---

## 2. Context Assembly & Trust Boundaries

Identify **how context is merged and prioritized**.

- What context layers exist?
  - System instructions
  - Developer rules
  - Retrieved documents (RAG)
  - Tool outputs
  - User input
- Are trust boundaries between these layers explicitly defined?
- Is instruction precedence enforced outside the model?
- Can lower-trust context override or dilute higher-trust rules?
- What assumptions does the model make about the accuracy or authority of context?

Reviewer note:  
Models reason over meaning, not authority.

---

## 3. Retrieval & Data Access (RAG)

Make data exposure explicit.

- What data sources can the model retrieve from?
- Is retrieval scoped by:
  - User identity?
  - Role?
  - Tenant?
  - Purpose?
- Where is the **last explicit authorization check** before retrieval?
- Can prompt content influence retrieval scope?
- Can the model synthesize sensitive information from multiple benign sources?
- How are cross-tenant or cross-role leaks prevented?

Reviewer note:  
Relevance is not authorization.

---

## 4. Tool / Function Access

Treat the model as an actor, not a helper.

- What tools or functions can the model invoke?
- What permissions do those tools have?
- Are tools scoped by capability or overly generic?
- Can the model:
  - Skip steps?
  - Reorder actions?
  - Call tools repeatedly?
- Are there tools that:
  - Modify state?
  - Access sensitive data?
  - Execute code?

Reviewer note:  
If the model can act, it is part of the control plane.

---

## 5. Delegated Trust & Autonomy

Be explicit about **what the model is trusted to decide**.

- What decisions are delegated to the model?
- Are any of these decisions security-relevant?
- What assumptions are made about model intent or “good behavior”?
- Where is human confirmation required?
- What happens if the model makes the wrong decision confidently?

Reviewer note:  
Optimizing task completion is not the same as enforcing policy.

---

## 6. Dynamic Logic & Runtime Artifacts

Account for logic that never exists at rest.

- Does the model generate:
  - SQL?
  - API payloads?
  - Config changes?
  - Workflow steps?
  - Executable code?
- Where are constraints enforced on generated logic?
- How are unsafe generations detected?
- Are these artifacts logged or auditable?
- How would you investigate an incident involving runtime-generated logic?

Reviewer note:  
If it executes, it is part of the attack surface.

---

## 7. Output Handling & Verification

Treat output as data.

- Is model output used directly by users?
- Is output consumed by downstream systems?
- Are outputs constrained by format, schema, or policy?
- Is output verified before being trusted or acted upon?
- Can sensitive data appear in output through synthesis?
- What prevents confident but incorrect output from causing harm?

Reviewer note:  
Generated text can be sensitive even if no single source is.

---

## 8. Failure Modes & Detection

Plan for silent failure.

- What does incorrect behavior look like?
- What signals indicate misuse or abuse?
- Are there cases where the system behaves “correctly” but violates intent?
- How would you detect logic abuse?
- What telemetry exists beyond tool call logs?

Reviewer note:  
AI systems often fail without errors.

---

## 9. Incident Response Readiness

Assume something will go wrong.

- Can you trace:
  - Inputs?
  - Context?
  - Retrieval results?
  - Tool calls?
  - Outputs?
- Can you explain *why* the model behaved the way it did?
- Can you revoke or constrain model capabilities quickly?
- Is there a kill switch for tools or retrieval?

Reviewer note:  
If you cannot explain behavior, you cannot respond to incidents.

---

## Final Reviewer Question

**Where is the last place in this system where security is enforced deterministically?**

If the answer is “inside the model,” the design needs revision.
