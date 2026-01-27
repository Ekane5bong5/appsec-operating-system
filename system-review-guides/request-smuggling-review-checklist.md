# Request Smuggling / HTTP Desynchronization Review Checklist

## Purpose
This checklist is used during architecture reviews, design reviews, and system assessments to identify HTTP request smuggling (desynchronization) risk. It focuses on infrastructure behavior and request parsing, not payloads.

---

## Request Path Awareness

- [ ] Are there multiple components that parse HTTP requests (CDN, load balancer, reverse proxy, ingress, application server)?
- [ ] Does traffic pass through any Layer 7 proxies before reaching the application?
- [ ] Is HTTP terminated and re-encoded at any point in the request path?

---

## Protocol and Parsing Behavior

- [ ] Do any components support HTTP/1.1?
- [ ] Is HTTP/2 used at the edge but downgraded to HTTP/1.1 internally?
- [ ] Are both `Content-Length` and `Transfer-Encoding` supported anywhere in the stack?
- [ ] Do different components handle malformed, duplicated, or obfuscated headers differently?

---

## Request Boundary Definition

- [ ] Which component is the first to decide where a request ends?
- [ ] Which component is the final authority on request boundaries before application logic?
- [ ] Are request boundaries interpreted consistently across all components?
- [ ] Could leftover bytes from one request be interpreted as the start of another?

---

## Normalization and Rewriting

- [ ] Does any component normalize, rewrite, or reconstruct HTTP requests?
- [ ] Are headers added or modified (e.g., `X-Forwarded-*`, auth headers, TLS metadata)?
- [ ] Is request rewriting consistent across layers?
- [ ] Could rewritten requests differ in length or structure from the original request?

---

## Connection Handling

- [ ] Are persistent connections used between front-end and back-end systems?
- [ ] Are multiple requests forwarded over the same backend connection?
- [ ] Is request pipelining or multiplexing possible at any layer?
- [ ] Does the backend assume all requests were fully validated upstream?

---

## Caching and Shared State

- [ ] Is any component caching responses?
- [ ] Are cache keys derived from request components that could be influenced by desync?
- [ ] Could a response be cached under th
