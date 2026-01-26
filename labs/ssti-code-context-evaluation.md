# SSTI — Code Context Evaluation (Tornado)

## What indicated evaluation was occurring?
- User-controlled input influenced a value rendered by a server-side template.
- An error condition revealed the template engine/framework (context leak).
- When a controlled value was supplied, it behaved like template logic (server-side evaluation), not like a literal string.

## What execution context was exposed?
- The template evaluation context that renders the blog author area.
- The key exposure was that the template engine interpreted the attacker-controlled value inside the server runtime.
- Practical meaning: the input inherited the permissions and capabilities of the server process during rendering.

## Why this was server-side, not client-side
- The result was visible in the server response without relying on browser execution.
- The behavior was produced by the backend rendering path (template engine), not client-side code.

## What assumption the developer made
- “This field is safe because it’s only used in rendering.”
- “Errors are harmless” (but they reveal engine/context).
- “User profile fields are trustworthy inputs.”

## Primary control that would prevent this class of bug
- Treat template source as immutable.
- Strictly separate template logic from user-controlled data (no string building of template expressions).
- Avoid exposing template evaluation surfaces to user-editable fields.

## One-sentence risk statement
If attacker-controlled values are evaluated in a template expression context, they can alter server-side logic during rendering and inherit server authority.
