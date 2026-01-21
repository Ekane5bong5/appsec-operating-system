# Access Control Lab 01 — Insecure Direct Object References (IDOR)

## Objective
Learn to identify IDOR vulnerabilities by observing authorization failures, not by fuzzing payloads.

This lab demonstrates how object-level authorization failures occur even when authentication is functioning correctly.

---

## What Object Was Accessed
A server-side chat transcript file stored on the application’s filesystem.

Each transcript represents a distinct resource that should be scoped to a single authenticated user.

---

## What the Application Trusted (Root Cause)
The application trusted a **user-supplied object identifier** as sufficient proof of authorization to access a server-side resource.

Possession of a valid transcript ID was implicitly treated as permission to access the corresponding file.

This is the core failure.

---

## Why the Application Believed the Request Was Allowed
The request:
- came from an authenticated user
- referenced an existing transcript identifier

No additional authorization logic was applied.

The server validated identity but never evaluated ownership or scope.

Object existence was treated as authorization.

---

## What Authorization Check Was Missing
Object-level authorization.

Specifically, the application failed to:
- resolve ownership of the requested transcript
- verify that the transcript belonged to the authenticated user

There was no binding between user identity and object access.

---

## Why Authentication Did Not Matter
Authentication only established *who* the requester was.

It did not establish whether the requester was authorized to access this specific object.

Because authorization scope was never evaluated, any authenticated user could access any transcript by modifying the identifier.

---

## Correct Enforcement Point
Authorization must be enforced **server-side at the moment the transcript is retrieved**.

The correct logic is:
1. Resolve the transcript owner
2. Compare ownership against the authenticated user
3. Deny access if the relationship does not match

Frontend controls, redirects, or URL structure are not valid enforcement points.

---

## Core Lesson
Object identifiers must never be treated as proof of permission.

Access to one object does not imply access to all objects of the same type.
