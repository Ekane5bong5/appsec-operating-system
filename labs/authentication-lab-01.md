# Authentication Lab 01 — Username Enumeration via Different Responses

## Lab Completed
**Username enumeration via different responses**

The goal of this lab was to identify whether authentication behavior leaked information about valid usernames.

---

## High-Level Observation

The application responded differently during failed login attempts depending on whether the supplied username existed. These differences allowed an attacker to enumerate valid usernames and then proceed to enumerate passwords for a confirmed account.

---

## What Assumption Did the Application Make?

The application assumed it was safe to return different authentication failure responses depending on whether the username existed, believing that these differences were not meaningful or observable to an attacker.

This assumption was incorrect because authentication behavior is observable, even when the application does not explicitly state whether a username is valid.

---

## What Signal Leaked Information?

The application leaked identity information through multiple response discrepancies:

- A difference in HTTP response length when comparing invalid usernames to valid usernames with incorrect passwords
- A difference in HTTP status codes during password enumeration, where a successful authentication attempt resulted in a 302 redirect instead of a standard failure response

These discrepancies allowed valid usernames and credentials to be identified without triggering errors or alerts.

---

## What State Was Trusted Incorrectly?

The application treated the username as a harmless input, even though it caused different internal authentication paths to be executed depending on whether the account existed.

As a result, identity state was partially revealed before authentication was successfully completed, enabling user enumeration.

---

## Why a WAF Wouldn’t Prevent This

A WAF would not prevent this issue because the requests were valid login attempts with no malicious payloads or abnormal syntax.

The vulnerability exists in the application’s authentication logic and response handling. The attacker learns information by observing behavior, which is outside the detection model of most WAFs.

---

## What Control Would Actually Fix It

The application should return uniform authentication responses regardless of whether a username exists. This includes consistent error messages, HTTP status codes, and response lengths, as well as equivalent execution paths for authentication failures.

Additionally, account-based rate limiting and throttling should be implemented to reduce the effectiveness of large-scale enumeration attempts.
