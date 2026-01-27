# HTTP Request Desync — Block 2 Observations (Labs)

## Core invariant (what makes this possible)
In HTTP/1.1, multiple requests can share the same connection as a continuous byte stream. Each component (proxy/load balancer/WAF vs backend server) must decide where one request ends so the next can begin.

A desync happens when different components interpret the same bytes using different rules (for example, one trusts Content-Length while the other trusts Transfer-Encoding: chunked), so they “finish” the request at different points.

## What breaks when components disagree
When the frontend and backend disagree on request boundaries, bytes that one component believes belong to request #1 can be treated by another component as the start of request #2.

This causes *state bleed across requests*:
- “leftover” bytes from a prior request get prepended to or interfere with the next request
- subsequent requests can produce unintended responses/behavior even if the follow-up request is normal

## Signals that indicate possible desync (behavioral, not exploit-based)
The strongest signals I observed were:
- **Cross-request anomalies**: the second request behaves differently because it inherits leftover bytes/state from the first.
- **Unexpected parsing outcomes**: server errors indicating it interpreted the next request line/method incorrectly (symptom of request boundary corruption).
- **Differential responses**: a normal request that should return 200 instead triggers an unexpected response (e.g., 404) after a preceding malformed/ambiguous request.
- **Timing irregularities**: delays/timeouts that appear only after ambiguous length/encoding conditions are introduced.

## Safe validation approach (how to confirm without “exploiting”)
Goal: confirm request boundary disagreement, not impact.

1. **Create an ambiguity**: send a request that includes both:
   - `Content-Length: ...`
   - `Transfer-Encoding: chunked`
   (or otherwise produce conflicting termination cues)
2. **Observe parser behavior**:
   - look for timeouts/hangs
   - look for parsing errors or method/route confusion
3. **Confirm cross-request effect**:
   - send a clean follow-up request immediately after
   - if the clean request’s response becomes inconsistent (errors, unexpected routes, differential responses), that is strong evidence of desync

## How I would explain this to a developer (no protocol jargon)
The system that receives requests is not consistently agreeing on where one request ends and the next begins. Because of that, extra bytes from one request can be carried into the next request on the same connection, which can change how the next request is interpreted and lead to unexpected responses.

This indicates that upstream and downstream components are parsing the same request differently, and we should align request parsing/termination behavior across the stack.
