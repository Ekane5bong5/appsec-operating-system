# SSTI Mental Model — Rendering vs Evaluation

## Purpose
This document explains Server-Side Template Injection (SSTI) at a conceptual level, focusing on how user input can transition from data to executable logic within server-side templating engines.

It is intended for interview preparation, secure design review, and long-term AppSec mastery.

---

## What SSTI Actually Is
Server-Side Template Injection occurs when user-controlled input is evaluated as template logic instead of being rendered as data.

The vulnerability is defined by execution context, not by specific syntax or payloads.

---

## What Templating Engines Do
- Generate dynamic content on the server
- Combine static templates with dynamic data
- Support logic such as conditionals, loops, and expressions
- Execute within the application’s runtime environment

Templates are executable code, not just presentation layers.

---

## Rendering vs Evaluation

**Rendering**
- Input is treated as data
- Data is inserted into placeholders
- Output is displayed
- No logic execution occurs

**Evaluation**
- Input is treated as instructions
- Expressions are interpreted by the template engine
- Logic executes within the server runtime

SSTI occurs when user input reaches an evaluation context.

---

## Why Logic Inside Templates Is Dangerous
- Template logic executes with server-level authority
- Templates often have access to application objects and helpers
- Evaluation happens before output encoding
- Small design mistakes can convert data into executable logic

---

## How SSTI Differs from XSS and SQL Injection

**XSS**
- Executes in the browser
- Impacts users and sessions
- Client-side execution context

**SQL Injection**
- Executes in the database
- Impacts queries and stored data
- Data-layer execution context

**SSTI**
- Executes in the application server
- Impacts application logic and behavior
- Server-side execution context

---

## Why Escaping Output May Not Help
- Escaping protects rendering, not evaluation
- If input is evaluated before escaping, escaping is ineffective
- SSTI exists before output is produced

Escaping output assumes the input will only be displayed.

---

## Why SSTI Often Looks Harmless
- Templates are commonly viewed as “just presentation”
- Escaped output gives a false sense of safety
- No visible exploit or error may appear initially
- SSTI is often only detected by auditors looking for evaluation behavior

---

## Key Mental Anchor
SSTI is not about payloads.

It is about:
- Who interprets the input
- When that interpretation occurs
- What authority the interpreter has
