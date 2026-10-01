---
layout: post
title: "Incident Report: An Exposed Bot Token, and a Rotation That Reported Success While the Service Was Down"
summary: "A chat-bot credential was sitting in an automation workflow. This report covers containment, why deleting it was not enough, the rotation, and a second failure where the rotation tool said it had worked and had not."
date: 2026-10-01
categories: reports
tags: [Cybersecurity, Reports, IncidentResponse, SecretsManagement, CredentialRotation, Verification]
number: 5
---

## Summary in plain language

A token is a password that lets a program act as a chat bot. In September 2026, while improving a monitoring job for one of my projects, I found such a token stored inside an automation workflow, where it did not need to be.

There is no evidence that anyone else used it. I treated it as exposed anyway, because a credential that has been copied into the wrong place cannot be proven unseen.

Two things went wrong, and the second is the more useful one:

1. **The exposure.** A reusable credential lived in a workflow file.
2. **The rotation.** The tool I wrote to replace the token reported success while the main service was still holding the old, revoked one, and was therefore down.

| Item | Detail |
| --- | --- |
| Credential | A chat-bot token used by my agent gateway |
| Where it was found | Inside an automation workflow |
| Evidence of misuse | None found |
| Mapped technique | MITRE ATT&CK T1552, Unsecured Credentials (defensive context) |
| Status | Token rotated at the provider. Rotation tool fixed and tested. One bot, one consumer |

## Phase 1: containment and the difference between deleting and revoking

**What was done first.**

- The token was removed from the workflow, so the scheduler no longer carried it.
- Ownership was made explicit: one service holds the token and is the only one allowed to read the bot's incoming messages.
- Diagnostic output was changed so it never prints the token.

**Why that was not the end.** Removing a secret from a file protects the next person who opens the file. It does nothing about anyone who already has the value. The credential's authority lives with the provider, so the provider is where it has to be invalidated. Cleaning the repository is hygiene. Revocation is the remedy.

A second point came out of this: a bot's message stream supports one reliable reader. Two processes polling with the same token make the system look randomly unreliable and make it impossible to audit which of them saw what.

## Phase 2: the rotation that lied

A few days later I rotated the token with a helper script. It reported success. The gateway was in fact still configured with the old value, which the provider had now revoked.

**Root cause.**

- The helper replaced only the copy of the token that matched one particular configuration file. The gateway's copy had already drifted from that file, so it was left untouched.
- The helper's proof of success was sending a test message with the new token. That proves the new token works. It does not prove the running service has loaded it.

This is the same failure I keep meeting in different clothes: a check that answers an easier question than the one being asked.

**Repair.**

- The helper now writes the token in one place, atomically, and verifies the saved value.
- Success requires a new gateway process whose connection to the chat service is current and receiving messages, not merely a token that validates.
- When another process shares the token, the helper stops it, so there is exactly one reader. Queued approvals are kept.
- Recovery reads the replacement without putting it in command arguments, and clean-up output no longer prints revoked credentials.

**Verification.** 218 project tests passed, including new cases for the drifted-copy situation and for a gateway that looks connected but is stale. The live gateway was confirmed connected and receiving messages after a restart.

## Phase 3: a second bot left behind

The rotation had required disabling a second process that handles approvals from my phone. Afterwards its approval buttons appeared but did nothing: its stored token was an old, revoked one.

The fix was to give it its own bot identity and token, separate from the gateway's, and to make the rotation helper refuse to assign one bot's identity to the other. Five focused regression tests cover it. The repair was confirmed with two real approvals I had queued myself, so no made-up message and no other person was used as the test.

## Lessons

- **Deleting a secret is clean-up. Revoking it is the fix.** Ask whether the provider still accepts the old value.
- **Prove the outcome, not the step.** "The new token works" and "the service is using the new token" are different claims.
- **One credential, one owner.** Shared credentials hide who did what and break in confusing ways.
- **Copies drift.** Every additional place a secret is stored is another place a rotation can miss.
- **Never use a real person as the test.** The proof came from my own queued actions.

## Preventive controls now in place

- A secret scan runs before every push to a new repository.
- Rotation has a tested procedure with a real success condition.
- Backups of credential files are taken before any rotation and named with the date.

## Limits of this report

No evidence of misuse was found, which is not proof that none occurred. The report is written from the records made at the time.

<!-- 01-10-2026 20:30 -->
