# SQL Injection Lab 01 — WHERE Clause Logic Bypass

## Lab Context
This lab demonstrated a SQL injection vulnerability where user-controlled input was concatenated directly into a SQL WHERE clause, allowing an attacker to bypass business logic and retrieve hidden data.

The vulnerability was not about extracting new data, but about bypassing authorization enforced at the database query level.

---

## What input was trusted?
The application trusted the `category` parameter from the URL query string.

User input was assumed to be a safe string value and was embedded directly into a SQL query without parameterization or proper separation from query logic.

---

## Where validation failed
Validation failed at the data access layer.

User input was concatenated into a SQL statement instead of being passed as a bound parameter. This allowed the input to modify the logical structure of the WHERE clause rather than being treated as data.

---

## What SQL actually executed
Original intended query structure:

```sql
SELECT * FROM products
WHERE category = 'Food'
AND released = 1
Injected input:

Food' OR 1=1--


Resulting SQL execution:

SELECT * FROM products
WHERE category = 'Food' OR 1=1--'
AND released = 1


This caused the WHERE clause to always evaluate as true, bypassing the released = 1 restriction and returning unreleased products.

How SAST would detect it

A SAST tool would flag this as:

String concatenation used to build a SQL query

User-controlled input flowing into a database execution sink

Absence of prepared statements or parameterized queries

Typical detection signals:

executeQuery("SELECT ... " + userInput)

Use of raw SQL with dynamic string construction

How a developer could re-introduce it

Even if fixed once, this vulnerability could be reintroduced if:

A developer adds a new filter condition using string concatenation

Business logic (e.g., visibility, authorization, tenant filtering) is enforced directly in SQL

A new feature reuses legacy query patterns without parameterization

This commonly happens during feature expansion or “quick fixes.”

The correct fix (in principle)

The correct fix is to:

Use parameterized queries or prepared statements

Ensure user input is always treated as data, not executable logic

Enforce authorization and business rules outside of raw SQL where possible

Example principle:

SQL defines structure

Parameters supply values

Application logic enforces access control

Filtering or escaping input is not a sufficient fix.

Key Takeaway

This was a logic-bypass SQL injection, not a data-extraction attack.

The root cause was trusting user input to participate in query logic, allowing authorization controls to be bypassed at the database layer.


---
