# SQL Injection — Code Review Notes

The most important thing to understand about SQL injection is this:

The SQL parser does not understand intent — only structure.

SQL injection happens when user-controlled data is allowed to influence the structure of a SQL statement instead of remaining data.

Another way to say it:

SQL injection happens when data becomes structure.

Everything else in this document flows from that idea.

---

## Trust boundaries

Anything that comes from outside the application must be treated as untrusted. This includes:

- URL parameters
- Request bodies
- Headers
- Cookies
- Hidden fields
- JSON input
- Values coming from other systems

Risk appears when untrusted input crosses into a trusted execution context — specifically when SQL is executed by the database engine.

---

## Where injection actually happens (the sink)

Injection does not happen “in the input.”  
It happens at the sink (the point where a string is interpreted as SQL).

Common SQL sinks include:

- String concatenation when building queries
- Dynamic SQL inside stored procedures
- ORM queries built via string concatenation
- Calls to `EXEC` / `EXECUTE`
- Native SQL passed through ORM APIs

If user input reaches any of these sinks without being structurally separated from SQL, injection is possible.

---

## The one question to ask during review

*Does user input ever become part of the SQL text that the database parses?*

- If yes → SQL injection is possible  
- If no (because parameters are bound) → SQL injection is structurally prevented

---

## Common red flags in pull requests

### ❌ String concatenation with user input

```java
"SELECT * FROM users WHERE id = " + userId;
This is vulnerable regardless of:

Programming language

Framework

Whether the value is numeric

Whether the code looks clean

If input is concatenated, the database is parsing attacker-influenced SQL.

❌ “Looks safe but isn’t”
Patterns that often fool developers:

Numeric IDs assumed to be safe

ORM queries assembled dynamically

Stored procedures called with concatenated arguments

Optional filters that remove all constraints

Frameworks do not protect against unsafe patterns — they only provide safer APIs if used correctly.

Why escaping and sanitization fail
Escaping-based defenses (e.g. addslashes(), mysql_real_escape_string()) fail because they rely on assumptions that are easy to get wrong:

They depend on how input is used

They depend on quoting context

They depend on database-specific behavior

Escaping tries to guess how the database will interpret input.

Parameterized queries do not guess.
They force the database to treat input strictly as data.

That is why parameterization works and sanitization does not.

Why parameterized queries work
Parameterized queries lock SQL structure before data is applied.

The database receives:

One fixed query structure

Separate bound values

The SQL parser never re-interprets user input as SQL.

This is the only reliable defense against SQL injection.

Stored procedures: what actually matters
Stored procedures are not automatically safe.

They are safe only if:

Parameters are bound

No dynamic SQL is constructed

sql code

SELECT * FROM users WHERE id = @id;           -- safe
EXEC('SELECT * FROM users WHERE id=' + @id); -- unsafe

Calling a stored procedure does not prevent injection.
Proper parameter binding does.

ORMs do not make SQL injection impossible
ORMs provide safer defaults, but they still allow:

Native SQL

Dynamic query construction

String concatenation

Injection is still possible if developers bypass safe APIs.

If user input becomes part of a query string, injection is possible — ORM or not.

Flexible queries: injection vs logic flaws
java
Copy code
String query = "SELECT id, firstname, lastname FROM authors WHERE forename = ?";
if (lastname != null) {
  query += " AND surname = ?";
}
Important distinctions:

This is not SQL injection

Parameterization is still intact

But the user can influence how restrictive the query is

If required inputs are omitted, constraints may be removed and the query can return the entire table.

This is a logic flaw, not injection — but it can still cause serious data exposure.

Parameterized queries prevent SQL injection, but they do not prevent logic flaws that allow unbounded data access.

Safer pattern for flexible queries
Prefer explicit query templates:

java
Copy code
if (first && last) {
  queryA();
} else if (first) {
  queryB();
} else if (last) {
  queryC();
} else {
  rejectRequest();
}
Why this is better:

No SQL assembled dynamically

Worst-case behavior is explicit

Safer against future changes

Blind SQL injection (reviewer awareness)
Even when errors are suppressed, attackers can infer behavior via:

Timing differences

Boolean conditions

Response size variations

Blind SQL injection relies on the same root cause:
user input influencing SQL structure.

Input validation: what it does and what it doesn’t
Validation is important, but it does not prevent SQL injection by itself.

What validation is good for
Enforcing type, length, and format

Preventing crashes and overflows

Preventing abuse and logic errors

Supporting business rules

What validation does not do
Prevent SQL injection without parameterization

Correct hierarchy:

Parameterization → prevents SQL injection

Validation → prevents bad states and abuse

Encoding / CSP / headers → reduce client-side impact

Validation supports security; it does not replace structural defenses.

Canonicalization (why filters break)
Input can be represented in many equivalent forms.

If validation happens before normalization:

Filters can be bypassed

Regex checks fail

Blacklists break

Always normalize input before validating it.

Practical reviewer checklist
When reviewing database code, ask:

Is user input ever concatenated into SQL?

Are parameters bound in every branch?

Is the “no filters provided” case handled explicitly?

What is the maximum data this query can return?

Is authorization enforced separately from filtering?

These questions catch most real-world SQL injection and data exposure issues.

Final summary
SQL injection occurs when untrusted input is interpreted as SQL structure.
Parameter binding prevents this by separating structure from data at the database level.
All other controls are supporting defenses, not substitutes.

