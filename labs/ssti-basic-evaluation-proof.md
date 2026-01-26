# SSTI — Basic Evaluation Proof (ERB)

## What indicated evaluation was occurring?
- A user-controlled value was reflected through a server-side template.
- When a simple expression was supplied, the output returned the evaluated result (not the literal text).
- This proves the server interpreted the input as template logic rather than rendering it as data.

## What execution context was exposed?
- The ERB template execution context (server-side rendering runtime).
- Even without enumerating objects, the key exposure is that input reached the template interpreter.
- The impact is defined by the fact that evaluation occurs inside the application server process.

## Why this was server-side, not client-side
- The expression result appeared in the server response, meaning evaluation happened before the HTML was sent.
- No browser execution was needed to observe the result.
- The interpreter was the backend template engine, not client-side JavaScript.

## What assumption the developer made
- “Template rendering is safe because it’s presentation.”
- “User input will remain data.”
- “If output is sanitized/escaped, evaluation can’t occur.”

## Primary control that would prevent this class of bug
- Never concatenate user-controlled input into template source.
- Pass user input as data via placeholders/variables only (data stays data, template stays code).

## One-sentence risk statement
If user input reaches template evaluation, an attacker can turn data into server-executed logic within the application runtime.
