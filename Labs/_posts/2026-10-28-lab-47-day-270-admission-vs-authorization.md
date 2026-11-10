---
layout: post
title: "🧪 Lab 47 — Distinguish Event Admission From Authorization"
summary: "Use a recording transport to show why a callback receipt does not prove current authorization or completed delivery."
date: 2026-10-28
categories: labs
tags:
  - Cybersecurity
  - Authorization
  - Audit
  - Labs
  - LearningProcess
lab: lab
---

## Lab Objective

Demonstrate the difference between observing an update, authorizing it, durably admitting it, dispatching it, and delivering a reply.

## Lab Environment

Use a disposable recording transport and synthetic Telegram-style updates. Block external network access and do not send real messages.

## Scenario

An updater advances its next offset while a consumer has not durably admitted the earlier event. The exercise checks whether a receipt is incorrectly treated as proof of completion.

## Steps

1. Feed a synthetic update through the recording transport.
2. Record the callback receipt and the updater's next offset.
3. Leave the consumer queue intentionally unconsumed.
4. Repeat the callback and verify duplicate suppression.
5. Compare the receipt fields with the original source identity and completion state.
6. Run a negative mutant that removes the duplicate guard and confirm the test rejects it.

## Expected Evidence

The receipt identifies the event but does not contain enough information to prove current authorization, original transport provenance, or a delivered reply. Duplicate callbacks are suppressed, and the weakened mutant fails.

## Security Takeaways

Audit fields need precise meanings. A single `accepted` value can hide whether the system merely saw an event or actually completed an authorized action.

## Limits

This is a diagnostic fixture, not a production denial or data-loss claim.
