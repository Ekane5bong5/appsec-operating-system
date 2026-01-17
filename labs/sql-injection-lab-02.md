Visible error-based SQL injection

# SQL Injection Lab 02 — Behavioral Detection (Visible Error-Based)

## Objective

Demonstrate how SQL injection can be **reliably detected without visibility into the underlying SQL query or returned data** by observing deterministic application behavior changes when untrusted input crosses a trust boundary into a database execution context.

This analysis focuses on **structural inference**, not exploit completion.

---

## Observed Trust Boundary

A user-controlled value originating from an HTTP cookie is incorporated into a backend SQL execution path.

This represents a trust boundary between:
- **Untrusted client input**
- **Database query construction and execution**

The application implicitly trusts this value despite its external origin.

---

## What Behavior Changed?

The application exhibited **distinct and repeatable response behavior changes** when the user-controlled value was modified:

- Under normal input, the application consistently returned a successful response.
- When the input was altered in a way that affected SQL syntax validity, the application returned a server-side error.
- When the input was adjusted again to restore syntactic validity, the application returned to a successful response without any visible data changes.

These response transitions were deterministic and directly correlated with changes to the structure of user input.

---

## What Does This Imply About Query Structure?

The observed behavior implies that:

- The user-controlled value is embedded into a SQL statement executed by the database.
- The database engine attempts to **parse** the supplied input as part of the SQL grammar.
- The input is not bound strictly as inert data at execution time.

This indicates **dynamic query construction**, where untrusted input influences SQL structure rather than being isolated via parameterization.

The exact SQL statement is not required to reach this conclusion.

---

## Why Visibility Into SQL Is Not Required

SQL injection is confirmed through **execution behavior**, not query disclosure.

Databases are deterministic systems:
- If malformed input alters parsing outcomes or execution paths,
- Then the input is being interpreted as part of the SQL command structure.

Because application responses changed solely based on the syntactic properties of user input, visibility into the SQL text or returned query results is unnecessary to confirm exploitability.

This principle applies equally to:
- Error-based SQL injection
- Boolean-based blind SQL injection
- Time-based blind SQL injection

---

## What Developer Assumption Failed?

The primary failed assumption was:

> “If query results are not returned to the user, the input cannot be abused.”

Additional failed assumptions include:
- Treating analytics or tracking inputs as low-risk
- Assuming sanitization or filtering prevents structural manipulation
- Trusting usage context instead of enforcing strict trust boundaries

The application allowed untrusted input to influence database execution without enforcing structural separation.

---

## What Control Would Have Prevented This?

The vulnerability would have been prevented by:

- **True parameterized queries**, where user input is bound strictly as data
- Avoiding dynamic SQL construction involving untrusted input
- Enforcing trust boundaries consistently across all input sources, including cookies and headers

Parameterized execution ensures that user input cannot alter SQL structure regardless of content or encoding.

---

## Why Exploitation Steps Are Omitted

This analysis intentionally stops at **structural confirmation**.

From an application security perspective:
- Demonstrating influence over query execution is sufficient to assess risk
- Additional exploitation would not provide further insight into root cause or prevention
- Root cause analysis and preventive controls are the primary AppSec objectives

---

## Key Takeaway

SQL injection does not require:
- Query visibility
- Returned data
- Explicit error messages

It only requires that **user input influences SQL structure**.

Behavioral analysis alone is sufficient to detect this class of vulnerability.
