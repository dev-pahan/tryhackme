# TryHackMe Pickle Rick – Full Walkthrough

**Target:** Ubuntu Server with Web Exploitation Puzzle  

---

## Overview

This walkthrough documents my complete process for completing the TryHackMe Pickle Rick room, a Rick and Morty-themed CTF challenge. It covers:

- Web application enumeration and source code analysis  
- Credential discovery and login bypass  
- Command execution via web panel  
- File system exploration and privilege escalation  
- Flag collection (three potion ingredients)

The purpose of this room is to provide a fun, narrative-driven environment that tests enumeration skills, web exploitation, and persistence in a humorous theme inspired by *Rick and Morty*.

- **Target Machine:** Ubuntu Server running Apache 2.4.18  
- **Attack Environment:** Kali Linux  

---

## Task 1: Introduction & Initial Reconnaissance

### 1. Deploy the Machine

I deployed the target machine and noted the assigned IP address (example: `10.10.53.48`). Your IP may vary, so always check your TryHackMe instance.

### 2. Port Scanning

I performed a comprehensive port scan with Nmap:

```bash
nmap -sV -sC -p- <target_ip>
```

**Open Ports Identified:**

- `22/tcp  open  ssh`
- `80/tcp  open  http`

Since SSH requires credentials I don’t yet have, I focused on the web service running on port 80.

**Answer to 1.2:** `2` open ports  

---

## Task 2: Web Enumeration & Credential Discovery

### 1. Inspecting the Web Page

Navigating to `http://<target_ip>:80` revealed a page where Rick asks Morty to help find three secret ingredients.  

Checking the page source revealed a hidden comment:

```html
<!--
  Note to self, remember username!
  Username: R1ckRul3s
-->
```

**Answer to 2.1:** `R1ckRul3s`

### 2. Directory Scanning with Nikto

Running Nikto against the target:

```bash
nikto -host <target_ip>
```

**Interesting Findings:**
- `/robots.txt`
- `/login.php`

### 3. Robots.txt

Visiting `/robots.txt` revealed the string:

```
Wubbalubbadubdub
```

This looked like a password.

**Answer to 2.2:** `Wubbalubbadubdub`

### 4. Login Portal

Navigating to `/login.php` presented a login form. Using the discovered credentials:

- **Username:** `R1ckRul3s`  
- **Password:** `Wubbalubbadubdub`  

I successfully logged in and gained access to a command panel.

---

## Task 3: Ingredient Collection

### Ingredient 1: Sup3rS3cretPickl3Ingred.txt

From the command panel, listing files revealed:

```
Sup3rS3cretPickl3Ingred.txt
assets
clue.txt
denied.php
index.html
login.php
portal.php
robots.txt
```

Accessing `http://<target_ip>/Sup3rS3cretPickl3Ingred.txt` directly revealed the first ingredient:

**Answer to 3.1:** `mr. meeseek hair`

---

### Ingredient 2: Rick’s Home Directory

Exploring `/home` via the command panel showed:

```
rick
ubuntu
```

Inside `/home/rick/` was a file named `second ingredients`. Using `sudo cp` to copy it into the web directory:

```bash
sudo cp /home/rick/second\ ingredients ./ingred2.txt
```

Accessing `http://<target_ip>/ingred2.txt` revealed the second ingredient:

**Answer to 3.2:** `1 jerry tear`

---

### Ingredient 3: Root Directory

Exploring `/root` revealed a file named `3rd.txt`. Again, I copied it into the web directory:

```bash
sudo cp /root/3rd.txt ./third.txt
```

Accessing `http://<target_ip>/third.txt` revealed the final ingredient:

**Answer to 3.3:** `fleeb juice`

---

## Summary

This walkthrough demonstrated a range of fundamental web exploitation and CTF skills:

**Reconnaissance & Enumeration:**
- Nmap service scanning  
- Nikto directory discovery  
- Source code analysis  

**Exploitation:**
- Credential discovery via hidden comments and robots.txt  
- Login bypass with discovered credentials  
- Command execution via web panel  

**Privilege Escalation & File System Exploration:**
- Navigating user directories  
- Copying restricted files into accessible locations  

**Flag Collection:**
1. `mr. meeseek hair`  
2. `1 jerry tear`  
3. `fleeb juice`  

> **The TryHackMe Pickle Rick room is a lighthearted challenge that teaches web enumeration, credential discovery, and privilege escalation in a fun narrative format.**

The key to success was careful source code inspection, directory enumeration, and persistence in exploring the file system for hidden flags.
