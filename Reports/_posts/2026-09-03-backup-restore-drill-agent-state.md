---
layout: post
title: "Test Report: Restoring an Encrypted Backup of My AI Agent's Data on a Second Machine"
summary: "A restore drill, written up as a test report. The backup was encrypted and kept off the main machine, then restored elsewhere and checked: 2,606 files, 21 database tables, 64,977 rows, without touching the live service."
date: 2026-09-03
categories: reports
tags: [Cybersecurity, Reports, BackupRecovery, DisasterRecovery, Encryption, SQLite, Validation]
number: 6
---

## Summary in plain language

A backup you have never restored is a hope, not a backup. On 2 September 2026 I tested whether the data behind my AI agent could really be brought back if the laptop it runs on were lost.

The backup is encrypted and stored on a different machine. I restored it on that second machine and checked the result in detail, without interrupting the live service and without starting a second copy of it.

**Result: pass.** The restore was complete and the database was sound. The test also exposed a way of backing up that looks fine and produces a damaged copy, which is the most useful thing it found.

| Check | Result |
| --- | --- |
| Files restored and read | 2,606 |
| Verification checks completed | 14 of 14 |
| Database tables inspected | 21 |
| Rows read | 64,977 |
| Live service process after the test | Unchanged |
| Live credential file after the test | Unchanged (same hash) |

## Objective

Show that the agent's state can be recovered on an independent machine, that the recovered data is intact, and that testing this does not put the live service at risk.

## What is backed up, and how

- **What:** the agent's database, configuration and working state.
- **Where:** an encrypted repository on a second machine, reached over a private network. It is never exposed to the internet.
- **Schedule:** run automatically by the operating system's scheduler.

## Test procedure

1. Take a backup using a consistent snapshot of the database.
2. On the second machine, restore into an isolated location. Not over anything live.
3. Check the restored file structure.
4. Open the restored database and run integrity checks.
5. Read every table and count the rows.
6. Record the live service's process and the hash of its credential file before and after, to prove the test did not disturb it.

## Findings

### 1. Copying a live database file produces a broken backup

The database writes changes to a side file before merging them. A plain copy of the main file, taken while the service is running, can catch it mid-change. I demonstrated this: the raw copy was inconsistent, and the snapshot-based copy was sound.

**Why it matters.** The broken backup looks exactly like a good one. Same size, same name, no error. You only find out at restore time, which is the worst moment.

**Control.** The backup uses the database's own snapshot mechanism. A direct file copy is not used.

### 2. Not every warning is corruption

The integrity check raised complaints about the search index. That index is derived data: it can be rebuilt from the tables it describes. Those warnings were recorded separately from anything touching the original data, of which there was none.

**Why it matters.** Treating every warning as corruption makes a verifier useless, because it always fails. Treating none as corruption makes it useless the other way.

### 3. A restore test must not start a second live service

The restored data includes the credentials the agent uses for messaging. Starting the agent from the restored copy would have put two live clients on the same accounts.

**Control.** The verifier only reads. It opens tables and checks structure. It never starts the service. The unchanged process and credential hash are the evidence.

### 4. The schedule had to be proven too

A backup that runs by hand and fails on the schedule is a common trap. The scheduled run was exercised under the scheduler's own environment, which is more limited than an interactive session, with an explicit path and a fixed release of the code.

## Result against the objective

| Objective | Met | Evidence |
| --- | --- | --- |
| Recoverable on an independent machine | Yes | Restored and read on the second machine |
| Data intact | Yes | 14 checks, 21 tables, 64,977 rows |
| Confidential at rest | Yes | Repository is encrypted, off the main machine |
| Live service undisturbed | Yes | Same process, same credential hash |
| Runs unattended | Yes | Exercised under the scheduler |

## What is not covered

- The drill restored and verified the data. It did not rebuild a full working service from nothing on a new machine and time it. That is the next test.
- The drill used one second machine. If both machines can be lost to the same event, a further copy somewhere else entirely is needed.
- How long recovery takes, and how much recent data could be lost, were not measured.

## Lessons

- **A backup is accepted when a restore succeeds,** not when the copy finishes.
- **Back up databases with their own snapshot tools.** File copies of a running database can be silently wrong.
- **Verify by reading, not by running.** Restored credentials are live credentials.
- **Test the schedule in the scheduler.**

## Limits of this report

One drill, on one date, for a single-user system. A backup strategy is only current as of its last successful restore.

*Written up on 1 October 2026 from the records made at the time. The date at the top places this report next to the events it describes.*

<!-- 01-10-2026 22:59 -->
