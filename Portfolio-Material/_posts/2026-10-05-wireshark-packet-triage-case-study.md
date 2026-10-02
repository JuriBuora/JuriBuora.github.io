---
layout: post
title: "From Packet Capture to Security Handoff: Wireshark Triage Case Study"
slug: "wireshark-packet-triage-case-study"
date: 2026-10-05
categories: portfolio-material
number: 11
tags:
  - cybersecurity
  - wireshark
  - packet-analysis
  - network-security
  - incident-response
  - evidence
status: "training-complete"
portfolio: true
lab_type: "network-triage"
summary: "A training case study showing how I used Wireshark and a supplied sample capture to record network observations, separate facts from interpretation, and prepare a defensible SOC handoff."
---

# From Packet Capture to Security Handoff

## Context

As part of the Google Cybersecurity Certificate, I analysed a supplied `sample.pcap` file in Wireshark. The purpose was not to claim a live breach. It was to practise how a security analyst moves from a noisy packet list to a small, reviewable evidence set.

## Method

I inspected the packet list, selected representative SSH traffic, expanded the Ethernet, IPv4, TCP, and SSH sections, and recorded the fields visible in the frame. I treated the packet bytes and decoded protocol tree as evidence for what the capture contains, not evidence of attacker intent.

The case study uses three layers of language:

- **Observed:** the selected frame decoded as Ethernet, IPv4, TCP, and SSH; TCP port 22 appeared in the conversation.
- **Interpreted:** the endpoints exchanged traffic associated with an SSH session.
- **Open:** the capture alone did not establish account authorisation, asset ownership, or malicious intent.

## Result

The outcome was a short training handoff that another analyst could reproduce: capture name, frame number, timestamp, endpoints, protocol stack, relevant port, nearby-frame context, and questions for authentication and asset logs.

## Security Value

The strongest portfolio result is the reasoning boundary. Network tooling can expose useful facts quickly, but it can also create false confidence if a protocol label is treated as a verdict. A defensible handoff makes the evidence and the uncertainty visible at the same time.

## Verification and Limits

The source was a course-provided capture shown in Wireshark screenshots. It contained a 200-packet view and visible SSH/TCP traffic. This is a training case study, not a production investigation, and no real person or endpoint is identified.

