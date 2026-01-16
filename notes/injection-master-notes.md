# Injection — Master Notes

Purpose: A calm, reusable explanation of injection as a root-cause pattern (not payloads).

## Root Cause Patterns

- Injection exists when untrusted input is allowed to influence executable structure.
- Applications construct interpreter-bound statements at runtime to perform business functions (search, login, filters, reports).
- When input is merged into a statement’s structure (string building, dynamic clauses), intent is lost.
- Interpreters execute structure, not intent; they cannot reliably distinguish “data” from “instructions” after composition.
- Validation and sanitization operate on content; they do not reliably preserve structure across contexts.
- “Escaping/neutralization failures” are common symptoms because once structure is influenced, teams fall back to fragile character handling.

## Why Sanitization and Validation Fail

- Sanitization and validation operate on input content, not on executable structure.
- Injection occurs after input is merged into dynamically constructed statements, where structure has already been influenced.
- Interpreters parse and execute grammar; they do not track which characters were originally “user input.”
- Context changes (SQL clauses, encoding layers, conditional logic) make it impossible for sanitization to remain correct everywhere.
- Escaping attempts are reactive and fragile because they occur after the trust boundary has already been crossed.
- Once untrusted input participates in structure, intent cannot be reliably restored through filtering or encoding.

## Common Dangerous Sinks (Interpreters)

- SQL execution (database query engines)
- OS command execution (shell/system calls)
- Template rendering engines (server-side templates)
- Expression language evaluation (dynamic expressions, rules engines)
- Deserialization / object materialization (parsing attacker-influenced serialized input)

## Why Parameterization Works (Mechanically)

- Query structure is defined first; user input is provided separately as values.
- The interpreter parses the statement structure independently from the parameter values.
- Bound parameters are treated as data tokens, not syntax; they cannot change grammar.
- This prevents input from modifying WHERE/JOIN/ORDER BY logic via structure manipulation.
- Key point: dynamic values are fine; dynamic structure with untrusted input is the risk.

## Observable Signals of Structural Influence (How SQLi is Detected)

- Behavioral differences: content changes, row counts change, access/logic flips.
- Error differences: database/parser errors triggered by specific inputs.
- Timing differences: consistent delay changes that correlate with input variation.
- Response consistency: repeated tests produce repeatable behavioral deltas.
- Note: you infer structure change from effects; you rarely need to see the query itself.


## Real-World Scenario (Example — CWE-89)

- Function: a web application provides a login or search feature backed by a database query.
- Developer intent: construct a SQL statement that retrieves records matching user-supplied criteria (e.g., username, search term, filter).
- Implementation detail: the application dynamically builds the SQL statement at runtime by inserting user input directly into the query text.
- Trust failure: the developer assumes validation or sanitization will prevent user input from altering query intent.
- What actually happens: user-controlled input becomes part of the executable SQL structure rather than remaining a bound value.
- Result: the database interpreter executes a modified query structure without awareness of original intent.
- Observable effect: application behavior changes (authentication bypass, unexpected results, errors, or timing differences), indicating structural influence prior to execution.
- CWE framing: commonly described as “improper neutralization,” but the root cause is allowing untrusted input to participate in executable query structure.

## One-Sentence Teaching Version

- Injection is a trust-model failure: untrusted input influences executable structure, and interpreters execute structure without knowing intent.
