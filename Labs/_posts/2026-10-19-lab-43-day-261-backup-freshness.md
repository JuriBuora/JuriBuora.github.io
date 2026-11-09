---
layout: post
title: "🧪 Lab 43 – Detect a Stale Backup Mirror"
summary: "Use a bounded receipt and representative-file check to distinguish a fresh backup mirror from a stale one."
date: 2026-10-19
categories: labs
tags:
  - Cybersecurity
  - Backups
  - Integrity
  - Labs
  - LearningProcess
lab: lab
---

## Lab Objective

Practice verifying backup freshness without treating a mounted directory or a successful copy command as proof of recoverability.

## Lab Environment

Use the local backup helper and its test fixtures in `/Users/juribuora/ai-agent-laptop`. Do not use private message content or a real person's account as test data.

## Scenario

A mirror exists, but its last trusted receipt is older than the allowed freshness window. The analyst must identify the stale condition and preserve enough evidence to explain it.

## Steps

1. Run the backup staleness-alert test suite:

   ```bash
   cd /Users/juribuora/ai-agent-laptop
   python3 -m pytest tests/test_backup_staleness_alert.py tests/test_hermes_mac_backup.py -q
   ```

2. Review the fixture for a fresh receipt and a stale receipt.
3. Confirm that the result names the failed phase or affected file when hashing fails.
4. Confirm that a stale mirror is not reported as fresh merely because its directory exists.
5. Record the evidence age, destination state, and remaining coverage.

## Expected Evidence

The tests pass, and the result distinguishes fresh, stale, unreadable, and unavailable states. No private file contents need to be printed.

## Security Takeaways

Backup freshness is an integrity and availability signal. The useful receipt says what was checked, when, and what remains unproven.

<!-- Written from verified work recorded on 2026-11-09. -->
