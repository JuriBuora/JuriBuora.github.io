---
layout: post
title: "🧪 Lab 44 – Test a Safe Voice-Service Fallback"
summary: "Verify that an unavailable voice backend fails clearly and cannot create a false delivery receipt."
date: 2026-10-20
categories: labs
tags:
  - Cybersecurity
  - Availability
  - FailSafe
  - Labs
  - LearningProcess
lab: lab
---

## Lab Objective

Test the boundary between a text response and a voice response when the Mac voice backend is unavailable.

## Lab Environment

Use the Hermes Mac-control tests and synthetic offline conditions. Do not send a real message.

## Steps

1. Run the focused offline voice tests:

   ```bash
   cd /Users/juribuora/ai-agent-laptop
   python3 -m pytest tests/test_hermes_tts_offline.py -q
   ```

2. Exercise the unavailable-backend fixture.
3. Confirm that synthesis is not started after the dependency check fails.
4. Confirm that subprocess cleanup runs on failure and timeout paths.
5. Confirm that any text fallback has a distinct disposition from voice delivery.

## Expected Evidence

The tests pass, the offline result is explicit, and no voice-delivered receipt is produced for an unavailable backend.

## Security Takeaways

Fail-fast behavior limits ambiguous retries and prevents an operator from confusing “text generated” with “voice delivered.” Availability failures need honest receipts.

<!-- Written from verified work recorded on 2026-11-09. -->
