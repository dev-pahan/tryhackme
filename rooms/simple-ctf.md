# TryHackMe Simple CTF - Full Walkthrough

**Target:** TryHackMe Simple CTF  

---

## Overview

Simple CTF is a beginner-level CTF on TryHackMe that showcases a few of the necessary skills needed for all CTFs, including:

- Scanning and enumeration
- Research
- Exploitation
- Privilege escalation

This walkthrough documents my complete process for completing the Simple CTF room.

- **Attack Environment:** TryHackMe AttackBox (Kali Linux)

---

## Task 1: Recon

### 1. Start the Virtual Environment

I started the AttackBox and the target VM, confirmed connectivity, and prepared for scanning.

### 2. Port Scan

As usual, I kicked things off with an nmap scan:

```
nmap -sC -sV -p- <target_ip>
```

From the results we see ports 21 (FTP), 80 (HTTP), and 2222 (SSH) are open.

Knowing there is a web server on port 80, I browsed to it to see what was served.

### 3. HTTP Enumeration

Visiting the site returned the default Apache2 page — nothing obvious. Next I ran gobuster to discover hidden directories:

```
gobuster dir -u http://<target_ip> -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -t 100
```

Gobuster found a directory at `/simple`. Browsing to http://<target_ip>/simple revealed a CMS page - specifically "CMS Made Simple" version 2.2.8.

---

## Task 2: Research & Exploitation

### 1. Find an Exploit

I searched online for known issues with that version:
Search: "CMS Made Simple 2.2.8 exploit"

This led to an Exploit-DB entry referencing CVE-2019-9053 — a SQL injection vulnerability.

### 2. Prepare & Run the Exploit

The exploit was provided as a Python script. I copied it to the AttackBox as exploit.py. Running it without arguments displayed usage and options:

```
python3 exploit.py -u http://<target_ip>/simple --crack -w /path/to/wordlist.txt
```

Running the exploit with the URL and wordlist returned a username and cracked password.

---

## Task 3: Initial Access

### 1. SSH to the Machine

Using the credentials discovered:

```
ssh <username>@<target_ip> -p 2222
```

After logging in, I listed the home directory and found user.txt.

### 2. Check for Other Users

I inspected the /home directory for other user accounts:

```
ls /home
```

## Task 4: Privilege Escalation

### 1. Check sudo Privileges

I checked what the current user can run with sudo:

```
sudo -l
```

The output showed the user (mitch) can run /usr/bin/vim without a password.

### 2. GTFOBins Research

I checked GTFOBins for ways to escalate using vim and found a recommended command to spawn a shell:

```
sudo vim -c ':set shell=/bin/sh'
```

### 3. Spawn Root Shell and Capture Flag

Running the vim-based method escalated to root. From the root shell, I read the root flag:

```
cd /root
ls
cat /root.txt
```

## Summary

This room is a great beginner exercise that covered:

- Nmap for port and service discovery
- Gobuster for web content discovery
- Researching CVEs and using an exploit script
- Using credentials to SSH into the box
- Enumerating users and locating user flags
- Checking sudo privileges and using GTFOBins to escalate to root
- Capturing the root flag
