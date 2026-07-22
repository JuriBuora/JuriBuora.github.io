---
layout: post
title: "Verifiable Artifact Delivery"
date: 2026-07-18
categories: portfolio-material
tags: [Cybersecurity, Integrity, SecureDelivery, Hashing, Authorization]
---

## Summary

I extended the supervised-agent design with a controlled way to share task artifacts. The central problem was simple: a completed task may produce a report or file, but giving broad storage access to a recipient is unnecessary and unsafe.

The solution used one-purpose, expiring artifact links, integrity checks before delivery, and durable delivery state. The result is a design that demonstrates authorization, integrity, logging, and recovery working together.

## Design Decisions

- One token maps to one artifact only.
- Tokens expire after a fixed period and can be revoked.
- The database stores a SHA-256 value of the token, never the token itself.
- The artifact's size and SHA-256 digest are recorded as evidence.
- A download re-hashes the served bytes and fails closed if the file changed after registration.
- Only the linked artifact is reachable; logs, backups, and arbitrary paths are not exposed by the delivery route.
- File delivery requires a completed task, a sealed result contract, a permitted size, successful hash verification, and destination confirmation.

## Evidence

- A fresh-process test retrieved byte-identical content using a valid link.
- Forged, expired, and revoked links were refused.
- An expiry test confirmed that a link can expire without deleting the original artifact.
- Crash and repeat-delivery tests confirmed a recipient does not receive duplicate file messages.
- The full verification suite passed 219 tests after the artifact-delivery phase.

## Security Relevance

This work maps directly to practical security decisions:

- **Authorization:** a token grants access to one defined object, not a file browser.
- **Integrity:** hash verification detects tampering between recording and delivery.
- **Confidentiality:** opaque tokens and narrow routes reduce accidental exposure.
- **Accountability:** durable access and delivery metadata makes investigation possible.
- **Resilience:** the system distinguishes a delivery attempt from a confirmed delivery, which makes safe recovery possible after a crash.

## What I Learned

An expiring link is not automatically secure. The surrounding details decide whether it is a capability with clear boundaries or just an untracked shortcut into storage. I learned to make those details explicit: scope, expiry, revocation, hashing, size limits, audit records, and duplicate-safe recovery.
