# Template Engine Risk Patterns (SSTI Reviewer Lens)

## Purpose
This document captures common design and code review patterns that introduce Server-Side Template Injection (SSTI).

It is intended for fast review during interviews, architecture discussions, and secure code reviews.

---

## Where SSTI Typically Appears
SSTI commonly appears in features that allow flexibility in presentation or formatting, including:
- User-supplied or editable templates (CMS, marketing, email systems)
- Dynamic template construction at runtime
- Concatenation of user input into template source
- Advanced markup or “custom formatting” features

These risks often arise intentionally to support business functionality.

---

## Why Logic in Templates Increases Risk
- Templates execute server-side within the application runtime
- Template engines support logic such as conditions, loops, and expressions
- Logic executes with application-level privileges
- Evaluation occurs before output encoding or escaping

Templates are treated as executable code by frameworks, not passive views.

---

## Dangerous Patterns in MVC Frameworks
Common high-risk patterns include:
- Building templates using string concatenation
- Allowing users to author or modify template content
- Passing user-controlled values as template structure rather than data
- Dynamic resolution of variable names or expressions
- Relying on output escaping as a primary safety control

Controllers often pass trust downstream to templates without re-validation.

---

## Why “We Trust This Variable” Fails
- Trust is contextual and depends on where input is interpreted
- A variable may be safe as data but dangerous as logic
- Template engines do not distinguish between “trusted” and “untrusted” input at evaluation time
- Evaluation context determines impact, not input source alone

Trusting a variable ignores the execution context in which it is used.

---

## Why Escaping Does Not Equal Safety
- Escaping protects rendered output, not execution
- SSTI occurs during template evaluation, before output is produced
- Input can be executed even if final output appears safe

Escaping assumes display-only behavior, which SSTI violates.

---

## “Looks Safe but Isn’t” Scenario
A content management system allows privileged users to customize templates.
User input is escaped and no XSS is visible.
The template engine evaluates user input as logic during rendering.
Server-side execution occurs silently within the application runtime.

---

## Reviewer Mental Anchor
Templates are code.

If user input influences template evaluation, SSTI risk exists regardless of escaping or user role.
