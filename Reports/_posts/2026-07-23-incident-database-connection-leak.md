---
layout: post
title: "Incident Report: A Service Stopped Answering Because It Never Closed Its Database Connections"
summary: "A root-cause report on a local web service that degraded until it refused connections. One helper function leaked a file handle on every call. The report covers the diagnosis, a one-place fix, and a test that could have failed."
date: 2026-07-23
categories: reports
tags: [Cybersecurity, Reports, IncidentResponse, Availability, RootCauseAnalysis, Python, SQLite]
number: 2
---

## Summary in plain language

On 22 July 2026 the web service that lets me manage my AI agents from my phone stopped answering. Restarting it helped for a while, then it failed again.

The cause was one small function. Each time the service talked to its database it opened a connection and never closed it. Every operating system limits how many files and connections one program may hold open. When the service reached that limit, it could no longer open its own database and started refusing requests.

This is an availability incident, not a breach. It is worth a report because the bug was invisible in normal testing, the error message pointed somewhere else, and the fix was verified with a test designed so that it could fail.

| Item | Detail |
| --- | --- |
| Affected | A local web service and its phone app |
| Symptom | Connection refused, and "unable to open database file" in the log |
| Root cause | A database helper that never closed its connections |
| Fix | One function changed. 37 call sites untouched |
| Status | Fixed, merged, verified on the running service |

## What was observed

- Requests to the service were refused.
- The error log repeated "unable to open database file".
- The service process held 293 open file handles. The limit for a process on this system was 256.

The error message is misleading on its own. The database file was fine. The process had simply run out of handles, and the database library reports that as a generic failure to open the file.

## Root cause

The service had a helper used everywhere as `with db() as conn:`. In Python, using a SQLite connection that way commits or rolls back the transaction when the block ends. It does **not** close the connection. That is documented behaviour, and it is easy to assume otherwise.

So every use leaked one handle. The pages that listed generated files were the worst, because they recorded an event once per file per task, in a loop.

## The fix

The helper was rewritten as a proper context manager that commits on success, rolls back on error, and **always closes** the connection afterwards.

Because all 37 call sites already used the same pattern, none of them had to change. The whole fix was in one place. A separate one-time connection used only at start-up was left alone, since it was not part of the leak.

## Verification

I wanted proof that the test could detect the bug before trusting it to show the bug was gone.

| Run | Requests | Open handles before | Open handles after |
| --- | --- | --- | --- |
| Old code (control) | 120 | 51 | 186 |
| Fixed code | 120 | 41 | 41 |

The control run on the unfixed code climbed by 135 handles. The same test on the fixed code stayed flat. Both ran on separate ports with separate scratch databases, so the live service was not involved.

After the fix was merged and the live service restarted:

- Five batches of 40 authenticated requests: 41 handles before and after every batch.
- Two more batches on a later process: again 41 to 41.
- A final check on the current process: 49 to 49, both pages returning normally, and the error log not growing.
- The existing browser tests for the two affected pages passed.

The operating system limit was **not** raised. Raising it would have delayed the failure and hidden the bug.

## What made this worse

During diagnosis, my own verification traffic was enough to push the already-leaking live service over its limit. Nothing destructive was done, but it is a reminder that testing against a degraded production service can finish it off. The later tests ran against scratch copies.

## Lessons

- **Read what a convenience actually does.** The `with` form on that object commits. It does not close.
- **An error message names a symptom.** "Unable to open database file" was a resource limit, not a file problem.
- **Do not raise the limit to make the alarm stop.** Find what is consuming the resource.
- **A test must be able to fail.** Running it first on the broken code is what gave the flat line any meaning.
- **A fix at one well-chosen point beats 37 edits.** The consistent pattern in the code base made that possible.

## Limits of this report

The measurements are from the day and were not re-run for this write-up. The service is local and single-user, so "outage" here means I could not reach my own tools.

*Written up on 1 October 2026 from the records made at the time. The date at the top places this report next to the events it describes.*

<!-- 01-10-2026 22:59 -->
