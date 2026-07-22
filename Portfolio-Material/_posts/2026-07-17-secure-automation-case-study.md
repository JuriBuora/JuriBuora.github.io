---
layout: post
title: "Building Safer Privileged Automation"
date: 2026-07-17
categories: portfolio-material
tags: [Cybersecurity, AutomationSecurity, LeastPrivilege, Approvals, Auditability]
---

## Summary

I designed and tested a staged supervision layer for a local AI-agent workflow. The goal was not to make an agent act more freely. It was to make sensitive work traceable, reviewable, and recoverable before allowing it to affect files, services, or deliveries.

The implementation remained feature-flagged and fixture-tested while the production path stayed unchanged. That boundary matters: a passing test suite is evidence that the feature works in its intended test scope, not proof that it should silently become the new default.

## Problem

An assistant that can create files, change code, manage services, or deliver results has privileged capability. Treating every request as ordinary chat would erase the security questions that matter most:

- What was requested, and what outcome was agreed?
- Which actions require approval?
- Can a repeated message trigger duplicate work?
- What happens if a process crashes while a task or delivery is in progress?
- Can an operator inspect state without depending on the main chat channel?

## Controls Implemented

- Classified read-only conversational requests separately from requests that create artifacts, change systems, or need multiple stages.
- Created a durable task record before the first sensitive side effect.
- Recorded task criteria, source channel, priority, scoped permissions, approval decisions, and lifecycle events.
- Added idempotency and restart recovery so redelivery or a crash cannot silently duplicate a task or notification.
- Kept an emergency CLI and a loopback-only, token-protected task inbox independent of the primary chat route.
- Preserved existing production behavior behind an unset feature flag while the new path was verified with injected fixtures.

## Evidence

- A two-process test showed a supervised task could be read from a fresh process before its first injected mutation.
- Restart tests confirmed that task commands and notifications survived process boundaries without duplicate completion delivery.
- The Phase 7 regression suite passed 186 tests; the fallback-client expansion passed 201 tests.
- The system was deliberately left off in normal operation: no production database, listener, or service was introduced during these phases.

## Security Relevance

This project applies familiar security principles to modern automation:

- **Least privilege:** capabilities are scoped to the task rather than assumed globally.
- **Separation of duties:** approval and inspection are independent from the agent action path.
- **Auditability:** state changes and deliveries have durable records.
- **Fail-safe defaults:** a request that looks sensitive moves into supervised handling; a flag must be explicitly enabled before state-changing controls operate.
- **Availability:** recovery behavior is tested across restart and crash scenarios, not assumed from an in-memory happy path.

## Key Takeaway

The important lesson is that trustworthy automation is not a more capable chatbot. It is a system with boundaries, evidence, operator controls, and a cautious path to production.
