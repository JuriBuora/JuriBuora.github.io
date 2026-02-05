---
layout: post
title: ' "Lab 01 – Linux Permissions & Ownership Hands-On Practice"'
date: 2026-02-05
categories: labs
tags:
  - LinuxFundamentals
  - Linux
  - CommandLine
  - Permissions
  - FileSystem
  - Bash
  - LearningProcess
  - Labs
---

## 🎯 Lab Objective

Practice real-world manipulation of Linux file permissions, ownership, and identity inspection through hands-on command experimentation. The goal was to reinforce conceptual understanding of access control mechanisms and begin developing enumeration and troubleshooting skills.

---

## 🧪 Lab Environment

- OS: Ubuntu 24.04 (Parallels VM)
- User: `parallels`
- Working Directory: `~/perm_game`

---

## 🧩 Lab Setup

Created a dedicated lab directory to avoid modifying system files.

```bash
mkdir perm_game
cd perm_game
```

Verified working directory:

```bash
pwd
```

Output:

```
/home/parallels/perm_game
```

---

## 📄 File Creation & Baseline Permissions

Created test files to manipulate:

```bash
touch fileA fileB script.sh
```

Checked initial permissions:

```bash
ls -l
```

Output:

```
-rw-rw-r-- fileA
-rw-rw-r-- fileB
-rw-rw-r-- script.sh
```

---

## 🔐 Permission Modification Exercises

### Making Script Executable Only By Owner

Attempted:

```bash
chmod 100 script.sh
```

Result:

```
---x------
```

This demonstrated numeric permission assignment and how removing read/write affects script usability.

---

### Setting File Permissions

Adjusted permissions to simulate real access control scenarios.

```bash
chmod 644 fileA
chmod 600 fileB
```

Verified:

```
-rw-r--r-- fileA
-rw------- fileB
```

---

## ⚠️ Troubleshooting Moment

Initially attempted:

```bash
chmod 644 fileA
```

But file name mismatch (`filA`) caused an error:

```
chmod: cannot access 'fileA': No such file or directory
```

This reinforced:

- Case sensitivity in Linux
- Importance of verifying file names

---

## 👤 Identity & Privilege Enumeration

Checked user identity and group membership:

```bash
id
```

Output:

```
uid=1000(parallels)
gid=1000(parallels)
groups=1000(parallels),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),101(lxd)
```

### Key Observations

- User belongs to `sudo` group → administrative privileges
- Additional groups provide extended system and device access
- Identity enumeration is commonly performed during privilege escalation reconnaissance

---

## 👑 Ownership Manipulation

### Attempt Without Elevated Privileges

```bash
chown root fileA
```

Result:

```
Operation not permitted
```

Confirmed that ownership changes require elevated privileges.

---

### Changing File Owner Using sudo

```bash
sudo chown root fileA
```

---

### Changing File Group

```bash
sudo chown :sudo fileB
```

---

### Verification

```bash
ls -l
```

Output:

```
-rw-r--r-- root      parallels fileA
-rw------- parallels sudo      fileB
```

---

## 🧠 Observations & Learning Points

### Permission Structure Remembered

```
Owner | Group | Others
```

Numeric Permission Reference:

| Value | Permission |
|----------|----------------|
7 | rwx  
6 | rw-  
5 | r-x  
4 | r--  
0 | ---  

---

### Ownership Hierarchy

- Owner controls file primary access
- Group enables collaborative permission sharing
- Root ownership protects system integrity

---

### Common Mistakes Encountered

| Mistake | Lesson Learned |
|-------------|------------------|
File name mismatch | Linux is case sensitive  
Missing sudo | Ownership requires elevated privileges  
Incorrect command syntax | Careful command validation required  

---

## 🕵️ Cybersecurity Relevance

This lab simulated several real-world privilege escalation investigation scenarios:

- Identifying weak file permissions
- Verifying user privilege level
- Understanding access control boundaries
- Practicing enumeration fundamentals

Permission misconfiguration remains one of the most common Linux exploitation vectors.

---

## 🔄 Commands Practiced

```bash
mkdir
cd
pwd
touch
ls -l
chmod
chown
sudo
id
```

---

## 🚧 Challenges Encountered

- Remembering numeric permission values
- Handling permission modification errors
- Understanding ownership vs permission relationship
- Syntax precision during command execution

---

## 💡 Key Takeaways

- File permissions control system trust boundaries
- Ownership determines ultimate file authority
- Root privileges override standard access controls
- Enumeration commands reveal privilege escalation opportunities
- Small mistakes reinforce deeper operational understanding

---

## 🔜 Future Lab Expansion

Planned follow-up topics:

- SUID / SGID / Sticky Bit exploitation
- World-writable file discovery
- Privilege escalation methodology
- Advanced permission auditing using `find`
- Log analysis using pipelines and text processing tools

---

## 📌 Reflection

This lab transitioned theoretical Linux permission knowledge into practical execution. Encountering command errors and permission restrictions improved understanding of Linux access control behavior and reinforced the importance of careful enumeration and verification. The exercise also highlighted how simple configuration mistakes can introduce significant security vulnerabilities.

---
