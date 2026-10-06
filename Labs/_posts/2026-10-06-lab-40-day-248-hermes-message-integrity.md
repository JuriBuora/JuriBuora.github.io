---
layout: post
title: "🧪 Lab 40 – Verify Message Integrity and Channel Authorization with Synthetic Fixtures"
summary: "Run a no-send acceptance check for a multi-channel assistant and verify that authorised owner actions pass while forged origins, ambiguous identities, routing errors, and ordering failures are rejected."
date: 2026-10-06
categories: labs
tags:
  - Cybersecurity
  - Authorization
  - MessageIntegrity
  - AccessControl
  - AIagents
  - Testing
  - Labs
  - LearningProcess
lab: lab
---

## Lab Objective

Practise testing the security boundary of a multi-channel assistant without using unaware people or sending real messages. The goal is to distinguish an authorised owner action from a forged channel origin and to verify that identity, ordering, destination, and pacing remain correct.

This is a defensive acceptance exercise. Use synthetic subprocesses, fixtures, and a no-send adapter only.

## Setup

- A local test checkout containing the message-integrity acceptance script
- A Python environment with the project's dependencies
- Synthetic Telegram and WhatsApp identities
- A temporary output directory for the JSON acceptance report
- No real contact identifiers or outbound delivery targets

The Hermes implementation used for this exercise records the deployed source hash and checks that the installed source matches the reviewed code.

## Procedure

1. Run the acceptance script in a test environment with synthetic identities and no-send dispatch:

   ```bash
   python scripts/verify-hermes-message-integrity.py \\
     --output /tmp/message-integrity-acceptance.json
   ```

2. Confirm that an authorised Telegram owner command is accepted.
3. Attempt the same control action through a lower-trust WhatsApp fixture and with a forged environment origin. The action should be refused.
4. Test contact resolution with one proven alias and with two distinct ambiguous matches. The proven alias may resolve; ambiguity must fail closed.
5. Verify that steering can be set and cleared for both known address forms without merging different people.
6. Feed synthetic text, photo, and voice events across a reconnect. Confirm that preceding text is not overtaken by media and that separate chats remain separate.
7. Check that operational notices stay on the configured owner route and that the no-send harness records zero diagnostic sends to contacts.
8. Inspect the pacing result and confirm that buffered work drains once, without duplicate accounting.
9. Save the JSON result and record the implementation/source hash, timestamp, accepted cases, rejected cases, and any remaining unobserved behaviour.

## Expected Evidence

A successful run should show:

```text
Telegram owner authority: accepted
Forged WhatsApp authority: refused
Ambiguous contact match: refused
Owner route: preserved
Diagnostic contact sends: 0
Bridge ordering: preserved
Queued delivery accounting: no duplicates
```

The real acceptance run used synthetic subprocesses and reported the expected wait sequence `[250, 5, 250, 250, 0]`. It also verified installed-source equality and completed without real-contact sends.

## Limitations

This lab does not prove how every future natural conversation will behave. It proves the tested acceptance and refusal paths under synthetic conditions. Do not replace the fixtures with unaware real people, and do not treat a passing harness as proof of every deployment or configuration state.

## Security Takeaway

Message security is broader than checking the text of a command. A safe multi-channel system must verify provenance at the action handler, resolve identity without guessing, preserve event order, route responses to the intended owner, and fail closed when authority is absent or contradictory.
