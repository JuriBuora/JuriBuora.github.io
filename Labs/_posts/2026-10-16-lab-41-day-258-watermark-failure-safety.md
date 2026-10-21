---
layout: post
title: "🧪 Lab 41 – Preventing Silent Loss in a Capture Pipeline"
summary: "Test a stateful message-capture pipeline so failed extraction does not advance its watermark or hide unread evidence."
date: 2026-10-16
categories: labs
tags:
  - Cybersecurity
  - DataIntegrity
  - SecureAutomation
  - Testing
  - Labs
  - LearningProcess
lab: lab
---

## Lab Objective

Practise designing and testing a safe state transition for a pipeline that reads message windows, extracts structured findings, and advances a watermark. The exercise focuses on preserving evidence when parsing fails.

## Lab Environment

Use a local Python project or a small standalone test harness. Do not use real contacts, private messages, or a production calendar. The input should be synthetic JSON representing two chats and their message timestamps.

## Scenario

The pipeline remembers the newest message it has processed for each chat. A model or parser may return an empty response, invalid JSON, or a valid response with no findings. These cases must not all produce the same state transition.

## Step 1 - Define the state

Represent each chat with:

```text
watermark: newest timestamp successfully handled
failure_count: consecutive unreadable windows
```

Create a window containing several synthetic messages newer than the watermark. Keep a copy of the input so the test can verify that it remains available after a failure.

## Step 2 - Exercise a failed extraction

Make the parser return an empty string or invalid JSON. The expected result is:

```text
watermark unchanged
failure_count increased by one
no action applied
```

Run the same input again and confirm that the messages are still selected for processing. This is the key evidence that the failure did not silently move the evidence boundary.

## Step 3 - Exercise a successful extraction

Return valid synthetic JSON containing one safe, test-only finding. Confirm that the pipeline validates the result, applies the planned action to the fixture, advances the watermark, and clears the consecutive failure count.

## Step 4 - Bound a poison window

Repeat the failed extraction until the configured retry limit is reached. Confirm that the pipeline writes an explicit log event and releases that one window, rather than blocking every later window in the chat. The log must distinguish this bounded decision from a normal quiet run.

## Step 5 - Test output shape

Exercise the no-new-work path and the calendar-or-destination-unavailable path. Every return value should expose the same keys, even when no action is taken. A dry run must not crash merely because there is nothing to write.

## Expected Evidence

- failed extraction leaves the watermark unchanged;
- the failed input is visible on the next retry;
- successful extraction advances state only after validation;
- the retry limit produces an explicit, reviewable log line;
- no real endpoint receives a message or calendar entry;
- tests fail if the failure record or state guard is removed.

## Security Takeaways

State cursors are security-relevant when they decide which records remain visible to an analyst or automation. Advancing a cursor after a failed parse can create silent loss, weaken auditability, and make a later investigation impossible. Retrying forever creates a denial-of-service risk, so safe designs need both preservation and a bounded escape path.

This lab does not prove a production monitoring system is secure. It demonstrates one narrow, reproducible control: do not claim to have processed evidence that the parser did not successfully understand.

<!-- Edited 21-10-2026 20:00 Europe/Rome -->
