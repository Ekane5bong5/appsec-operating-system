# SQL Injection Detection — Mental Model (Behavioral)

## Core Question

How do I know SQL injection exists before I ever see a payload, query, error message, or leaked data?

---

## Behavioral Proof of SQL Injection

SQL injection can be identified through reliable application behavior changes that can be reproduced that occur when user-controlled input is modified.

If changes to externally supplied input cause differences in application behavior that cannot occur unless backend query logic is being influenced, then SQL injection exists — regardless of visibility into SQL execution.

The key signal is not data extraction, but evaluation.

---

## Why Payloads Are Irrelevant to Detection

Payloads are merely tools used to test hypotheses.  
They are not the vulnerability itself.

The vulnerability exists at the moment untrusted input is allowed to influence SQL query structure or logical evaluation. Once input participates in query execution rather than being treated strictly as data, structural failure has already occurred.

Detection therefore precedes exploitation.

---

## How Application Behavior Leaks Query Structure

When untrusted input crosses a trust boundary into a database execution context, backend evaluation outcomes can manifest indirectly at the application layer.

Examples of such behavioral signals include:
- Conditional rendering of content
- Differences in response logic
- Execution failures or recoveries
- Timing or flow changes

In these cases, the application itself becomes a application behavior serves as the feedback mechanism, revealing whether backend query conditions evaluated as true or false.

Direct query visibility is unnecessary.

---

## What Must Be True in the Backend

For these behavioral signals to exist, the backend must satisfy all of the following:

- User-controlled input is incorporated into a SQL execution path
- Query structure or logic is dynamically influenced by that input
- Input is not strictly bound as inert data at execution time
- Trust boundaries are inferred rather than enforced

These conditions describe a structural vulnerability, not an output or error-handling issue.

---

## Failed Assumptions That Enable SQL Injection

Across SQL injection variants, the same assumptions repeatedly fail:

- “This input is not user-facing, so it is safe”
- “Nothing is returned, so exploitation is impossible”
- “Sanitization is sufficient to prevent abuse”
- “Context implies trust”

SQL injection exists wherever execution context is shared with untrusted input, regardless of intent or visibility.

---

## SQLi Core Statement

If user-controlled input can influence how a database query is **evaluated**, producing observable differences in application behavior, then SQL injection exists — even when queries, errors, and data are never exposed.
