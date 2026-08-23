---
layout: post
title: "Recovery by Proof: Encrypted Off-Host Hermes State Backup"
date: 2026-09-02
categories: portfolio-material
tags: [Cybersecurity, BackupRecovery, Encryption, SQLite, IncidentResponse, Validation]
---

## Summary

I designed an encrypted, off-host backup and restore-verification workflow for the local state behind Hermes. The work focused on the real recovery question: can protected state be restored on a separate machine and checked safely, rather than merely copied somewhere?

## Design Decisions

- Back up a consistent SQLite snapshot rather than copying a live WAL-mode database.
- Encrypt the backup repository and keep it off the primary machine.
- Restore on the recovery machine, not over the live installation.
- Verify file structure, database readability, table contents, and integrity checks.
- Treat derived FTS-index compatibility warnings separately from non-derived corruption.
- Never start a second live messaging bridge from restored credentials.

## Evidence

- A raw live SQLite copy demonstrated an inconsistency; the SQLite snapshot was structurally sound.
- The restore read 2,606 files, completed 14 checks, and inspected 21 tables with 64,977 rows.
- The live bridge PID and credential-file hash were unchanged before and after verification.
- A scheduled backup was then exercised under launchd with an explicit runtime PATH and an immutable release checkout.

## Security Relevance

This work demonstrates:

- **Confidentiality:** encrypted off-host backup storage.
- **Integrity:** snapshot-aware database capture and structural validation.
- **Availability:** recovery on an independent machine.
- **Operational safety:** restore testing that does not disturb the live service.
- **Evidence discipline:** a backup is not considered successful until its recovery path has been exercised.

## What I Learned

Recovery planning is not a storage problem alone. It combines data consistency, encryption, runtime safety, compatibility handling, and independent verification. The strongest outcome was not that a backup command returned success; it was that the restored state could be inspected on another machine without changing the service that was still running.
