---
layout: post
title: "🧪 Lab 45 – Verify a Scoped Telegram Handoff"
summary: "Use synthetic routes and negative mutants to verify that a Telegram handoff cannot borrow another platform's authority."
date: 2026-10-21
categories: labs
tags:
  - Cybersecurity
  - Authorization
  - Telegram
  - Labs
  - LearningProcess
lab: lab
---

## Lab Objective

Verify that a handoff route is bound to its prepared source, session, destination, and owner, and that excluded routes fail before side effects.

## Lab Environment

Use the synthetic verifier and prepared evidence bundle in `/Users/juribuora/ai-agent-laptop`. This lab performs no real Telegram or WhatsApp send.

## Steps

1. Run the bounded verifier:

   ```bash
   python3 -B /Users/juribuora/ai-agent-laptop/scripts/verify-hermes-telegram-handoff-owner.py
   ```

2. Confirm the positive path switches the actual synthetic session and restores the expected scope.
3. Confirm that an excluded route is rejected before effects.
4. Run or inspect the negative mutants for a skipped session switch and an omitted callback join.
5. Verify that cleanup completes before the resource is released.

## Expected Evidence

The verifier reports the bounded positive path and the exact mutants as failures. It does not claim live authentication, production activation, or successful real delivery.

## Security Takeaways

Internal routing is still an authorization boundary. A persisted row or a shared process does not grant a route authority over another platform's session.

<!-- Written from verified work recorded on 2026-11-09. -->
