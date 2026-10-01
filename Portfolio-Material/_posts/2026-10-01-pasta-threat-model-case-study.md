---
layout: post
title: "PASTA Threat Model: Sneaker Marketplace Mobile App"
slug: "pasta-threat-model-sneaker-marketplace"
date: 2026-10-01
categories: portfolio-material
number: 10
tags:
  - cybersecurity
  - threat-modeling
  - pasta
  - application-security
  - sql-injection
  - authentication
  - api-security
  - data-protection
status: "complete"
portfolio: true
lab_type: "threat-model"
summary: "A practical PASTA threat-modeling exercise for a mobile sneaker marketplace, covering business objectives, architecture, data flows, threats, vulnerabilities, attack paths, and mitigations."
---

# PASTA Threat Model: Sneaker Marketplace Mobile App

## Overview

This project applies the **PASTA (Process for Attack Simulation and Threat Analysis)** framework to a fictional mobile marketplace for sneaker buyers and sellers.

The objective is to identify security risks **before launch** by examining:

- business objectives
- application architecture
- data flows
- likely threats
- exploitable vulnerabilities
- attack paths
- practical security controls

The application allows users to:

- create and manage accounts
- browse sneaker listings
- buy and sell products
- message other users
- rate sellers
- process payments through several payment methods

Because the platform handles **credentials, personal information, transaction data, and payment-related data**, compromise could result in account takeover, fraud, privacy violations, or database exposure.

---

# Stage I — Define Business and Security Objectives

The first stage of PASTA establishes why the application exists and which business outcomes must be protected.

## Business objectives

1. Provide a secure and easy-to-use marketplace where buyers and sellers can create accounts, communicate, and buy or sell sneakers.
2. Protect users' personal, authentication, and payment-related information to maintain privacy and customer trust.
3. Process transactions securely and reliably while reducing legal, regulatory, and fraud-related risk.

## Security implications

These objectives mean that the security design must prioritize:

- confidentiality of user information
- integrity of listings and transactions
- availability of the marketplace
- secure authentication
- secure payment processing
- authorization between buyers, sellers, and backend services

---

# Stage II — Define the Technical Scope

The application uses several security-sensitive technologies.

## Technologies identified

- **APIs** for communication between the mobile application, backend services, and third-party services
- **PKI / encryption**, including AES and RSA
- **SHA-256 hashing**
- **SQL databases**

## Priority technology: SQL database

I would prioritize the **SQL database** because it stores high-value information such as users, sellers, product listings, and transaction data.

If the application constructs database queries using untrusted user input, an attacker may be able to perform **SQL injection** and read, modify, or delete information.

The APIs and authentication mechanisms should also receive significant attention because insecure authorization could expose backend functionality even if the database itself is protected.

## Security note

Using plain **SHA-256 for password storage is not sufficient** for a modern authentication system.

Passwords should normally be stored using a dedicated password-hashing algorithm such as:

- Argon2id
- bcrypt
- scrypt

These algorithms are intentionally slow and resistant to large-scale password cracking.

Payment-card data should generally be **tokenized and/or encrypted**, rather than simply hashed with SHA-256.

---

# Stage III — Application Decomposition and Data Flow

PASTA Stage III breaks the application into components and examines how information moves between them.

## Simplified architecture

```text
User
  |
  v
Mobile Application
  |
  v
API Gateway / Backend API
  |
  +-------------------+
  |                   |
  v                   v
Authentication      Payment Provider
Service
  |
  v
Application Server
  |
  v
SQL Database
```

## Typical marketplace request

```text
User
  |
  | Search request
  v
Mobile App
  |
  | HTTPS API request
  v
Backend API
  |
  | SQL query
  v
Database
  |
  | Search results
  v
Backend API
  |
  v
Mobile App
```

## Purchase flow

```text
Buyer
  |
  v
Mobile App
  |
  v
Backend API
  |
  +------> Payment Provider
  |              |
  |              v
  |        Payment Result
  |
  v
SQL Database
  |
  v
Order Confirmation
```

## Important trust boundaries

Security controls are especially important at transitions between:

- user input and application logic
- mobile application and backend API
- API and database
- application and payment provider
- authenticated user and protected account resources

These boundaries are common locations for exploitation.

---

# Stage IV — Threat Analysis

Two high-priority threats were identified.

## Threat 1 — SQL Injection

An attacker may submit malicious input through:

- login forms
- search fields
- product filters
- seller-management fields
- API parameters

If this input is directly inserted into SQL queries, the attacker may be able to manipulate the database.

### Possible impact

- unauthorized data access
- modification of listings
- deletion of records
- disclosure of user information
- authentication bypass
- potential database compromise

---

## Threat 2 — Credential Theft and Account Takeover

Attackers may attempt to access accounts using:

- credential stuffing
- password spraying
- brute-force attacks
- phishing
- previously leaked passwords

A compromised seller account could also be abused to change payment details, create fraudulent listings, or communicate with buyers.

### Possible impact

- unauthorized account access
- fraudulent transactions
- exposure of private messages or personal information
- seller impersonation
- abuse of stored payment or account information

---

# Stage V — Vulnerability Analysis

Threats become actionable when exploitable weaknesses exist.

## Vulnerability 1 — Unsafe SQL Query Construction

The application may be vulnerable if it constructs queries by directly concatenating user-controlled input.

### Vulnerable pattern

```sql
SELECT * FROM users
WHERE username = '$username'
AND password = '$password';
```

If the input is not safely handled, the database may interpret attacker-controlled characters as SQL syntax.

### Safer design

Use:

- parameterized queries
- prepared statements
- strict server-side validation
- least-privilege database accounts

---

## Vulnerability 2 — Weak Authentication or API Authorization

The application may expose accounts or backend functionality if it has:

- weak password controls
- no MFA
- unlimited login attempts
- predictable or long-lived sessions
- missing authorization checks
- insecure object references
- over-permissive API endpoints

For example, a user who changes an API object ID should **not** gain access to another user's order or seller account data.

---

# Stage VI — Attack Modeling

Attack modeling connects threats and vulnerabilities into realistic attack paths.

## Attack path A — SQL Injection

```text
Attacker
  |
  v
Finds user-controlled input
  |
  v
Sends crafted SQL input
  |
  v
Application builds unsafe query
  |
  v
Database executes unintended SQL
  |
  v
Unauthorized data access or modification
```

### Example scenario

A product-search endpoint accepts:

```http
GET /api/search?q=red
```

If the backend directly concatenates `q` into a SQL statement, a malicious request could alter the intended query.

The important security issue is not the specific payload but the **trust failure**: attacker-controlled data is allowed to change SQL logic.

---

## Attack path B — Account Takeover

```text
Attacker
  |
  v
Obtains leaked username/password pairs
  |
  v
Automates login attempts
  |
  v
No MFA / weak rate limiting
  |
  v
Valid credential pair succeeds
  |
  v
Account takeover
  |
  v
Fraud, data exposure, or seller impersonation
```

### Detection opportunities

A SOC or security team could detect this activity through:

- repeated failed logins
- many accounts accessed from one IP address
- one account receiving login attempts from many IP addresses
- impossible-travel patterns
- unusual device fingerprints
- sudden changes to payment details
- API requests inconsistent with normal user behavior

---

# Stage VII — Risk Reduction and Security Controls

The final PASTA stage identifies safeguards that reduce the likelihood or impact of successful attacks.

## Control 1 — Parameterized SQL Queries

Use prepared statements and parameterized queries so user input is treated as **data**, not executable SQL.

This is one of the primary defenses against SQL injection.

---

## Control 2 — Strong Authentication

Implement:

- multi-factor authentication
- secure password hashing
- login rate limiting
- credential-stuffing detection
- session expiration
- secure password reset workflows

These controls reduce the likelihood of account takeover.

---

## Control 3 — Encryption and Secure Payment Handling

Use:

- TLS for data in transit
- strong encryption for sensitive data at rest
- tokenization for payment data where possible
- secure key management
- trusted payment processors

Applications should minimize the amount of payment-card data they store directly.

---

## Control 4 — Least Privilege, Logging, and Monitoring

Enforce least privilege across:

- user roles
- application services
- API permissions
- database accounts
- administrative interfaces

Generate security telemetry for:

- authentication events
- authorization failures
- database errors
- unusual API activity
- payment changes
- administrator actions

Centralized monitoring allows suspicious activity to be detected and investigated quickly.

---

# Risk Summary

| Risk | Likelihood | Impact | Primary Controls |
|---|---|---|---|
| SQL injection | Medium | High | Parameterized queries, validation, DB least privilege |
| Credential stuffing | High | High | MFA, rate limiting, login monitoring |
| Broken API authorization | Medium | High | Server-side authorization, object-level access checks |
| Sensitive-data exposure | Medium | High | TLS, encryption, tokenization, access controls |

> Likelihood values are illustrative for this lab. A production assessment would require telemetry, architecture details, threat intelligence, and business context.

---

# Defensive Telemetry

From a SOC or detection-engineering perspective, useful data sources include:

- web/API access logs
- application logs
- authentication logs
- identity-provider logs
- WAF alerts
- database audit logs
- payment-provider events
- EDR telemetry from application servers
- cloud audit logs

## Suspicious patterns

Examples worth investigating:

```text
Hundreds of failed logins from one IP
```

```text
One username targeted from many geographically unrelated IP addresses
```

```text
Large numbers of HTTP 400/403/500 responses against the same API route
```

```text
Database syntax errors immediately following unusual user input
```

```text
A newly authenticated account immediately changes seller payout details
```

These signals do not automatically prove compromise, but they provide useful investigation pivots.

---

# Key Lessons

This exercise reinforced several practical security concepts:

1. **Threat modeling starts with business context.**
   Security controls should protect business-critical assets and processes rather than exist in isolation.

2. **Data flow matters.**
   Security weaknesses often appear at trust boundaries between users, APIs, application services, databases, and external providers.

3. **A threat is not the same as a vulnerability.**
   SQL injection is an attack technique or threat; unsafe query construction is the vulnerability that enables it.

4. **Authentication and authorization are different.**
   Authentication establishes identity. Authorization determines what that identity is allowed to access.

5. **Detection should be designed alongside prevention.**
   Even strong preventive controls can fail. Logs and telemetry are necessary to detect and investigate attacks.

---

# Improvements for a Production Assessment

A real-world assessment would go further by reviewing:

- API endpoint inventory
- authentication and session architecture
- authorization model
- database permissions
- secrets management
- payment-provider integration
- mobile application storage
- certificate validation
- third-party dependencies
- cloud infrastructure
- logging coverage
- incident-response requirements
- privacy and regulatory requirements

It would also include testing for common application-security weaknesses from the OWASP ecosystem and review known vulnerabilities affecting specific deployed software versions.

---

# Conclusion

The sneaker marketplace has several high-value attack surfaces, particularly its authentication system, APIs, SQL database, and payment workflows.

The most important risks identified in this threat model are **SQL injection, account takeover, broken authorization, and sensitive-data exposure**.

These risks can be significantly reduced through secure query handling, strong authentication, encryption, strict authorization, least privilege, and centralized security monitoring.

---

## Portfolio Context

**Project type:** Threat modeling / application security
**Framework:** PASTA
**Focus areas:** SQL injection, authentication, API security, data protection, security monitoring
**Role simulated:** Security analyst / application security analyst
**Environment:** Fictional sneaker marketplace mobile application

This project was completed as part of hands-on cybersecurity training and was expanded beyond the original exercise to include practical attack paths, SOC telemetry, detection opportunities, and production security considerations.

<!-- 01-10-2026 14:50 -->
