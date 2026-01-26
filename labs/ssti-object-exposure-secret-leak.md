# SSTI — Information Disclosure via User-Supplied Objects (Django)

## What indicated evaluation was occurring?
- The application provided a server-side template editing surface.
- Template syntax was interpreted by the backend (errors and rendering behavior confirmed server-side evaluation).
- The rendering behavior demonstrated that expressions were executed within the template engine rather than displayed as text.

## What execution context was exposed?
- The Django template rendering context with access to framework-level objects/configuration.
- The impact was information disclosure: the template context enabled retrieval of a sensitive secret (framework secret key).
- Practical meaning: template evaluation had visibility into privileged server-side configuration state.

## Why this was server-side, not client-side
- The template was rendered on the server and returned as a completed response.
- Secrets were extracted from server-side state, not from the browser environment.

## What assumption the developer made
- “Only trusted users will access template editing.”
- “Template editing is a safe admin feature.”
- “If a user can edit content, they can edit templates” (incorrect trust boundary).

## Primary control that would prevent this class of bug
- Do not allow untrusted users to author or modify templates.
- If template customization is required, use a logic-less templating approach and strict sandboxing.
- Prevent template contexts from exposing secrets/configuration objects.

## One-sentence risk statement
If users can influence template execution and the template context exposes privileged objects, SSTI can become a direct secret disclosure and escalation pathway.
