---
layout: post
title: "Incident Report: A Chat Message Powered Off My Computer and Nothing Could Say Who Did It"
summary: "A post-incident report on an unexplained remote shutdown in my AI agent setup: what was ruled out, the path that allowed it, the fixes, and how both fixes were nearly lost in a merge the next day."
date: 2026-08-19
categories: reports
tags: [Cybersecurity, Reports, IncidentResponse, AccessControl, DefaultDeny, ChangeManagement]
number: 5
---

## Summary in plain language

In mid-August 2026 a Mac in my AI agent setup shut down shortly after a chat message arrived. The agent that handles those messages had not produced a reply or run any tool at that point, so it could not have been the one that did it. Something else had called the shutdown, and there was no record of what.

The incident had no lasting damage: the machine restarted and no data was lost. What made it serious was the missing answer. A destructive action had happened and I could not say who was responsible.

This report covers the investigation, the two fixes, a near-miss the following day that would have silently undone them, and what is still open.

| Item | Detail |
| --- | --- |
| What happened | A computer was powered off after an inbound chat message |
| Harm | Unplanned shutdown, service interruption, no data loss |
| Core problem | The caller could not be identified |
| Detected by | The shutdown itself. There was no alert |
| Status | Two fixes in place and verified. One path deliberately left documented as open |

## Timeline

| When | Event |
| --- | --- |
| Day 1 | Machine powers off after a chat message arrives. |
| Day 1 | Investigation shows the agent's own turn had not produced a response or executed a tool when the shutdown happened. |
| Day 1 | Enabling condition found: a routing component that could be reached from a messaging surface where the sender is not verified as me. |
| Day 1 | Fix 1: that route closed at its source. Fix 2: the shutdown script made default-deny, with caller logging. The shutdown sequence itself repaired. |
| Day 2 | A large branch merge is found to contain both fixes on one side only. Deploying the other side would have re-opened the hole. Resolved file by file and verified. |

## Investigation

**First hypothesis: the agent did it.** This was the obvious explanation and it was wrong. No model response had been generated yet and no tool had run. Ruling it out mattered more than it sounds: the easy fix would have been a patch to the agent, which would have left the real path open.

**What the evidence pointed to.** If the agent's turn did not do it, something else reachable from that message did. The enabling condition was a routing layer that could be reached from a messaging surface where the system acts on my behalf. From there a request could arrive at something able to call the shutdown.

**What I could not establish.** The exact caller for that one event. The records at the time did not capture it. I am stating that plainly instead of presenting a tidy root cause I cannot prove.

## Fixes

**1. Close the route.** The behaviour that made the shutdown reachable from the messaging surface was fixed where it originated, not only at the far end.

**2. Make shutdown default-deny.** The shutdown script now requires the caller to state its source, and the source must be on an allowlist. An unknown or missing source is refused. Every attempt records the chain of processes that led to it, so if anything unexpected calls it again, the next occurrence is attributable even if this one was not.

**3. Narrow who can ask.** The context that describes how to shut the machine down is now only available on the two channels where I am verified.

**4. Repair the shutdown sequence.** While investigating I found the sequence itself was degraded:

- A fallback step had failed 3 times out of 3 and wasted 25 seconds each time. It was removed.
- Fixed 20-second waits were too short for a real shutdown and caused applications to be force-closed unnecessarily. They were replaced with waits that check progress.
- The script was put under version control for the first time, so a missing or stale copy cannot quietly mean the capability does not work.

## The near-miss on day 2

The next day a branch that had diverged by 70 commits was being merged back. Both fixes existed only on the branch. The main line still had the old, exploitable routing behaviour. Deploying from the main line as it stood would have re-armed the original problem with no error and no warning.

The merge was resolved per file, according to which side had actually continued development, instead of a blanket "this side wins". After the merge: 788 tests passing (743 before, the rest arriving with the other side), no conflict markers left, and a direct check that the routing fix was present in the merged code.

## Still open

The operating system's permission configuration still allows a direct call to the system shutdown command, bypassing my script. Closing that needs a root-owned wrapper and a change I have to make interactively. It is recorded as open work, not implied to be solved.

## Lessons

- **"The obvious suspect did not do it" should widen an investigation, not end it.** It is the moment a quick patch is most tempting and least useful.
- **Destructive actions need default-deny and attribution.** If I cannot say who did something, that is the first defect.
- **A fix in a commit is not a fix in what is running.** The two have to be verified separately. This one nearly vanished within 24 hours.
- **Safety mechanisms rot when they are never exercised.** The fallback had failed every time it was used and nobody had noticed.

## Limits of this report

This was a single-user system. The report is written from the records made at the time. The original caller was never identified, and the report does not claim otherwise.

*Written up on 1 October 2026 from the records made at the time. The date at the top places this report next to the events it describes.*

<!-- 01-10-2026 22:59 -->
