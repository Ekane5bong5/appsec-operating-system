# SSTI Review Checklist (AppSec OS)

## Purpose
This checklist is used to identify Server-Side Template Injection (SSTI) risk during code reviews, architecture reviews, and security assessments.

It focuses on execution context, template evaluation, and trust boundaries rather than payloads or exploitation techniques.

---

## 1. Template Usage & Entry Points
- Does the application use server-side templating?
- Is any part of the template source constructed dynamically at runtime?
- Is user-controlled input ever concatenated into a template?
- Are users allowed to author, edit, or customize templates directly or indirectly?

---

## 2. Evaluation vs Rendering
- Are user-controlled values strictly rendered as data?
- Is there any scenario where user input is evaluated as logic or expressions?
- Does the template engine support conditionals, loops, or expression evaluation?
- Can variable names, expressions, or template structure be influenced by input?

---

## 3. Template Engine Capabilities
- Is the template engine capable of executing logic?
- What objects, helpers, or functions are accessible during template evaluation?
- Does the template context expose application state, configuration, or framework objects?
- Is the execution context documented and understood?

---

## 4. Trust & Assumptions
- Is template code treated as trusted application logic?
- Is trust based on user role (e.g., “admin-only” template editing)?
- Is it assumed that escaping output prevents SSTI?
- Is sandboxing assumed to be sufficient without verification?

---

## 5. Sandboxing & Isolation
- Is the template engine sandboxed by design?
- Is sandboxing enforced or merely configured?
- What happens if sandbox restrictions fail or are bypassed?
- Is template execution isolated from sensitive server resources?

---

## 6. Impact & Blast Radius
- If SSTI occurs, what execution context is exposed?
- What permissions does the template runtime inherit?
- Can sensitive data, configuration, or secrets be accessed?
- Could this escalate beyond rendering into broader server impact?

---

## 7. Reviewer Judgment
- Is SSTI risk explicitly acknowledged in the design?
- Is template safety treated as a design concern rather than an input validation issue?
- Are controls in place to ensure user input never reaches evaluation context?

If multiple answers are “Unknown,” SSTI risk has not been adequately reviewed.
