# Access Control Lab 02 — User ID Controlled by Request Parameter

## Objective
Learn to identify horizontal privilege escalation caused by trusting client-supplied identity parameters instead of enforcing authorization server-side.

This lab demonstrates how authentication can succeed while authorization is completely bypassed.

---

## What Object Was Accessed
Another user’s API key.

The API key is a sensitive, user-scoped resource that should only ever be accessible to the user it belongs to.

---

## What the Application Trusted (Root Cause)
The application trusted a **user-supplied identity parameter** (`id`) as sufficient proof of authorization.

Instead of deriving the target user from the authenticated session, the server relied on a client-controlled parameter to decide *which user’s data* to return.

This is the core failure.

---

## Why the Application Believed the Request Was Allowed
The request:
- originated from an authenticated session
- contained a valid-looking user identifier in the `id` parameter

The application assumed that supplying a username in the request was equivalent to being that user.

No server-side authorization logic validated that the requested user identity matched the authenticated session identity.

---

## What Authorization Check Was Missing
The application failed to perform **identity-to-session binding**.

Specifically, it did not:
- derive the target user from the authenticated session
- verify that the requested user identifier matched the authenticated user

Client-supplied identity was treated as authoritative.

---

## Why Authentication Did Not Matter
Authentication only established that the requester was a valid user.

It did not establish that the requester was authorized to act on behalf of the user specified in the request parameter.

Because authorization relied on a client-controlled identity field, any authenticated user could access any other user’s API key.

---

## Correct Enforcement Point
Authorization must be enforced **server-side when resolving the user context** for the request.

The correct approach is:
1. Identify the authenticated user from the session or token
2. Ignore or strictly validate any client-supplied user identifiers
3. Ensure user-scoped resources are always resolved using the authenticated identity, not request parameters

Authorization must not depend on user-controlled identity input.

---

## Core Lesson
Client-supplied identity must never be treated as proof of authorization.

Authentication establishes who the requester is; authorization must strictly bind all user-scoped resources to that authenticated identity.
