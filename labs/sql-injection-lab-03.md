# SQL Injection Lab 03 — Behavioral Detection (Blind Conditional)

## Objective

Demonstrate that SQL injection can be **reliably detected without query visibility, returned data, or error messages** by observing deterministic application behavior changes when untrusted input influences SQL execution structure.

This lab focuses on **behavioral inference**, not exploit completion.

---

## Observed Trust Boundary

A user-controlled value originating from an **HTTP tracking cookie** is incorporated into a backend SQL query execution path.

This represents a trust boundary between:
- **Untrusted client-controlled input**
- **Database query construction and execution**

The application implicitly trusts this value despite its external origin.

---

## What Input Was Controlled

- A tracking identifier supplied via an HTTP cookie
- The value is used by the application for analytics-related database lookups

---

## What Behavior Changed

The application exhibited **binary, repeatable behavior changes** based on whether the backend SQL query returned any rows:

- Under normal input, the application returned a successful response containing a **“Welcome back”** message.
- When the tracking value was altered to cause the query to evaluate as **false**, the response no longer included the message.
- When the tracking value was manipulated to force a **true** evaluation, the message consistently reappeared.

These differences occurred **without any returned data or visible error messages**.

---

## What This Implies About Query Structure

The observed behavior implies that:

- The tracking value is embedded within a SQL **conditional context** (e.g., a WHERE clause).
- The SQL query’s result set size directly influences application behavior.
- User-controlled input is able to influence the **logical evaluation** of the query.

This confirms that untrusted input is being interpreted as **executable query logic**, not inert data.

---

## Why Visibility Into SQL Is Not Required

Visibility into the SQL query is unnecessary because:

- The application acts as a **behavioral oracle**, revealing query evaluation outcomes indirectly.
- Consistent differences between true and false conditions prove execution of user-influenced logic.
- Behavioral inference alone is sufficient to confirm structural SQL injection.

This confirms a **blind SQL injection condition**, where query results are inferred through application behavior rather than direct output.

---

## What Developer Assumption Failed

The developer implicitly assumed that:

- A non-user-facing field (analytics tracking ID) was low-risk
- Absence of errors or returned data eliminated injection risk
- Users would not manipulate non-visible parameters to infer backend logic

These assumptions failed because **execution context matters more than output visibility**.

---

## What Control Would Have Prevented This

The vulnerability would have been prevented by:

- Strict use of **parameterized queries** for all database interactions involving untrusted input
- Ensuring untrusted values are bound strictly as data, never concatenated into query logic
- Treating all externally supplied identifiers as untrusted, regardless of perceived sensitivity

---

## Key Takeaway

Blind SQL injection does not require errors, output, or data leakage.

If application behavior changes based on user-controlled input influencing query evaluation, **structural SQL injection exists**, regardless of visibility or extraction of data.
