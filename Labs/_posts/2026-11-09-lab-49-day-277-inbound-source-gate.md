---
layout: post
title: "🧪 Lab 49 — Gate Queued Events on Current Source Authority"
summary: "Exercise an inbound queue that refuses revoked, orphaned, or source-drifted events before consuming them."
date: 2026-11-09
categories: labs
tags:
  - Cybersecurity
  - Telegram
  - Authorization
  - InputValidation
  - Labs
  - LearningProcess
lab: lab
---

## Lab Objective

Verify that a queued event is admitted only through the current trusted receiver and authorization generation.

## Lab Environment

Use a disposable inbound-owner fixture with synthetic sessions, queue entries, and authorization generations. No real network or message delivery is needed.

## Scenario

An event remains in a queue after the receiver is revoked or replaced. The consumer must refuse it before claiming the session or removing the event from the queue.

## Steps

1. Register a trusted receiver and current authorization generation.
2. Queue a synthetic event with the matching source.
3. Revoke or replace the receiver before consumption.
4. Attempt admission and verify refusal before FIFO removal or session claim.
5. Repeat with a wrong receiver, orphaned event, and changed source generation.
6. Run mutants that consume before checking authority or skip the join/finalizer path; both must fail.

## Expected Evidence

Every invalid source is refused, the queued event remains available for review, and finalizers release any partially entered session. Logs distinguish source refusal from successful processing.

## Security Takeaways

Queue availability is not authorization. Current source identity must be checked before consuming work, especially when the queue survives a process or profile transition.

## Limits

This lab proves a bounded admission gate, not full adapter ownership, complete agent execution, or live Telegram delivery.
