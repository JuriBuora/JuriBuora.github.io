---
layout: post
title: "Training Report: Wireshark Packet-Capture Triage and Evidence Handoff"
summary: "A bounded training report on inspecting a supplied sample.pcap in Wireshark, documenting visible network facts, and identifying what the capture cannot prove."
date: 2026-10-05
categories: reports
tags: [Cybersecurity, Reports, Wireshark, PacketAnalysis, NetworkSecurity, IncidentResponse, Evidence]
number: 10
---

## Executive summary

This report documents a training exercise using the course-provided `sample.pcap` capture in Wireshark. The capture displayed 200 packets, including visible SSH/TCP traffic. I inspected representative Ethernet, IPv4, TCP, and SSH fields and prepared a bounded analyst handoff.

The result supports a network observation, not an incident verdict. The capture alone cannot prove that a login was unauthorised, that an endpoint was compromised, or that the traffic was malicious.

## Scope and source

The scope was limited to the supplied packet capture and the fields visible in Wireshark. The screenshots showed the packet list, a selected frame, the decoded protocol tree, and packet bytes. No live capture, production system, credential, or real-person identity was involved.

## Observations

1. Wireshark displayed a 200-packet view for the supplied file.
2. The packet list included SSH traffic carried over TCP.
3. A selected frame exposed Ethernet addresses, IPv4 source and destination, TCP source and destination ports, sequence information, and an SSH protocol section.
4. The decoded fields could be compared with the underlying packet bytes.

## Interpretation

The selected traffic is consistent with an SSH conversation. That is a useful lead for an analyst who needs to compare authentication logs, maintenance windows, asset ownership, and expected network paths.

## Limitations

The source does not identify the endpoint owners or establish whether the connection was approved. It does not provide enough context to determine impact, persistence, or compromise. The report therefore makes no production-security finding.

## Recommended next evidence

For an authorised investigation, the next checks would be the relevant authentication events, account ownership, asset inventory, time synchronisation, expected administrative activity, and neighbouring traffic. Each result should be added to the timeline with its source and confidence.

## Conclusion

The exercise met its training objective: I can now turn a packet capture into a concise, reproducible observation while preserving the distinction between network evidence and security judgement.

