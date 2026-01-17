# Injection Behavioral Signals

This document captures observable application behaviors that indicate how user input is handled by backend SQL queries.

The focus is on reasoning about **query structure**, not exploiting with payloads.

---

## What behaviors suggest query concatenation?

- Application behavior changes based on how input affects logic, not just data returned

-Authorization or filtering rules behave inconsistently based on input

- Different responses occur when input alters conditional logic

- SQL-related errors appear when input is malformed

- Query behavior depends on how strings are interpreted rather than on business rules

These prove user input is being merged into the query string, allowing it to influence SQL grammar instead of being treated as data.

---

## What behaviors suggest parameterization?

Input is consistently treated as literal data

Special characters do not influence query behavior

Query results change only when legitimate business inputs change

Errors (if any) are generic and not tied to SQL syntax

Input validation happens before database interaction

These behaviors indicate query structure and user input are separated and query structure is fixed at compile time and user input is bound separately at execution.

---

## Why error suppression does not remove exploitability

Primarily because errors are feedback, and not the vulnerability itself

Suppressing errors hides evidence, but does not eliminate the fact that an attacker was able to control the query structure

The database still executes attacker-influenced logic

Logical manipulation can occur without visible failures

Authorization bypasses do not require errors

If user input influences query structure, the trust boundary is already broken therefor error visibility is irrelevant.

---

## Why blind SQL injection still proves structural failure

Blind SQLi confirms influence over query execution without direct output

Observable differences (timing, boolean behavior) demonstrate control

Output suppression does not remove interpreter-level control

The vulnerability exists entirely at the query construction layer

Blind SQLi proves the same root cause as classic SQLi: user input is participating in SQL grammar.
