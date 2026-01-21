# Access Control Lab 03 — URL-Based Access Control Can Be Circumvented

## Objective
Identify function-level authorization failures caused by trusting request-routing headers instead of enforcing authorization within the application itself.

This lab demonstrates how frontend or intermediary controls are not valid security boundaries.

---

## What Object Was Accessed
The administrative interface (`/admin`), which exposes privileged functionality intended only for administrators.

The admin panel represents a high-impact, function-level resource.

---

## What the Application Trusted (Root Cause)
The application trusted **request-routing metadata** (specifically the `X-Original-URL` header) as a security boundary.

Access control decisions were effectively delegated to a frontend system, rather than being enforced by the backend application itself.

This is the core failure.

---

## Why the Application Believed the Request Was Allowed
The backend framework supported the `X-Original-URL` header and used it to determine the effective request path.

By supplying this header, the backend routed the request to `/admin` without independently verifying whether the requester was authorized to access administrative functionality.

The backend assumed that requests reaching it had already been properly filtered.

---

## What Authorization Check Was Missing
The application failed to perform **function-level authorization** on the admin endpoint.

Specifically, it did not:
- verify that the requester was authenticated
- verify that the requester held an administrative role or privilege
- enforce authorization checks at the point where the admin functionality was executed

Authorization was implicitly trusted to occur upstream.

---

## Why Authentication Did Not Matter
Authentication was irrelevant because the backend did not require authentication or authorization checks for the admin functionality itself.

Once the routing restriction was bypassed, the backend executed privileged functionality without verifying identity or role.

This was not an authentication bypass.
It was a complete absence of server-side authorization enforcement.

---

## Correct Enforcement Point
Authorization must be enforced **within the backend application at the admin endpoint itself**.

The correct model is:
1. Treat all requests as untrusted, regardless of routing headers or source
2. Authenticate the requester
3. Explicitly verify administrative privileges before executing admin functionality

Frontend filters, proxies, or routing rules must never be treated as security controls.

---

## Core Lesson
Routing controls are not authorization controls.

Privileged functionality must enforce authorization explicitly at the point of execution, regardless of how the request reaches the application.
