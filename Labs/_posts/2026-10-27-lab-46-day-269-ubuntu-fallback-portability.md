---
layout: post
title: "🧪 Lab 46 — Verify a Provider Fallback and Its Host Paths"
summary: "Test a provider fallback and confirm that a security control reads the intended Ubuntu paths and identities."
date: 2026-10-27
categories: labs
tags:
  - Cybersecurity
  - Linux
  - Authorization
  - Labs
  - LearningProcess
lab: lab
---

## Lab Objective

Verify that a provider fallback is selected after a controlled primary failure, and that a ported operator tool resolves the intended home, interpreter, service, and allowlist.

## Lab Environment

Use a disposable Linux home and a local fixture that returns a simulated provider `429`. Do not use real credentials, private messages, or real recipients.

## Scenario

A primary model provider becomes unavailable. The fallback should answer, while an operator-control tool must not silently read Mac paths or authorize a quoted, malformed identity.

## Steps

1. Configure a fixture primary provider that returns a usage-limit error.
2. Configure a local fallback endpoint in the disposable environment.
3. Run one isolated request and record the selected provider and reply.
4. Run the tool's `list`, `status`, and `doctor` operations against the fixture state.
5. Add quoted values to the allowlist fixture and confirm the parser strips dotenv-style outer quotes.
6. Compare the installed script hash with the approved source copy.

## Expected Evidence

The request completes through the fallback. The diagnostic reports no dangerous inconsistency, and a quoted allowlist value maps to the same identity as its unquoted form. A missing or unavailable tunnel remains an explicit failure, not a false success.

## Security Takeaways

Portability is part of access control. A copied script is not a verified control until its authority paths, identity parsing, and service target are tested on the new host.

## Limits

This lab uses synthetic provider failure and fixture identities. It does not authorize a real WhatsApp or Telegram action.
