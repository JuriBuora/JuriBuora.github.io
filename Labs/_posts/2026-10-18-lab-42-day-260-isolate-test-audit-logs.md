---
layout: post
title: "🧪 Lab 42 – Isolate Test Failures from a Production Audit Log"
summary: "Build a small command wrapper whose tests record simulated failures in a temporary audit log while the production log remains untouched."
date: 2026-10-18
categories: labs
tags:
  - Cybersecurity
  - Auditability
  - Testing
  - DataIntegrity
  - Observability
  - Labs
  - LearningProcess
lab: lab
---

## Lab Objective

Practise separating synthetic test evidence from operational audit evidence. The exercise shows why a passing test can still damage observability if it writes mocked failures into the same log used by a live service.

## Lab Environment

Use a local Python project or a standalone script. Create two paths in a temporary working directory:

```text
runtime/audit.jsonl
tests/test-audit.jsonl
```

Do not point the exercise at a real service log, personal message archive, or production database.

## Scenario

The command wrapper records one JSON line for every operation. A unit test forces a timeout so it can check the error-handling path. In the unsafe design, the test uses the default runtime path and leaves a fake timeout among real events.

Your task is to make the test use an isolated destination while still proving that the timeout is recorded with the expected operation and error code.

## Step 1 - Create a minimal audit writer

Write a small function that accepts an audit path, operation name, and error code, then appends one JSON object per line. Keep the schema simple, for example:

```json
{"operation":"agenda","error_code":"timeout","source":"test"}
```

The exact schema is less important than keeping the event structured and readable.

## Step 2 - Demonstrate the unsafe test

Use the runtime audit path as the default in a test fixture. Force a synthetic timeout and inspect the file. Record why this is unsafe: a later operator cannot know whether the timeout came from a real command or a test.

Do not use a real service while demonstrating the problem. A fixture file is enough.

## Step 3 - Redirect tests to a temporary destination

Create a temporary directory in the test setup and configure the writer to use a file inside it. Preserve the default runtime path as a value to compare against, but never write to it from the test.

Add a guard such as:

```python
assert test_audit_path != runtime_audit_path
```

Run the forced timeout again and verify that the temporary file contains the expected JSON event.

## Step 4 - Test cleanup and repeatability

Run the test twice. Confirm that each run creates or resets its own temporary destination and that no stale event is required for the assertion to pass. Confirm that the runtime audit file is absent or unchanged.

## Expected Evidence

The completed exercise should show:

- the production audit path and test audit path are different;
- the mocked timeout is recorded in the test log;
- the event still contains the expected operation and error code;
- the production log is not created or changed by the test;
- the temporary directory is removed after the test finishes.

## Security Takeaways

Audit logs are evidence, so test provenance matters. A temporary fixture is not merely convenient test plumbing: it prevents synthetic failures from changing the operational record and gives the test an explicit, reviewable destination.

This pattern also applies to telemetry queues, alert stores, incident tickets, backup receipts, and other state that a security investigation may later trust.

<!-- Written 21-10-2026 09:30 Europe/Rome -->
