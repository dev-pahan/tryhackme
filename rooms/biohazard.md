# TryHackMe Biohazard – Full Walkthrough

**Target:** Ubuntu Server with Multi-Stage Web Puzzle  

---

## Overview

This walkthrough documents my complete process for completing the TryHackMe Biohazard room, a Resident Evil-themed CTF challenge. It covers:

- Web application enumeration and directory discovery
- Flag collection and puzzle solving
- Cipher decryption (Base64, Base32, Base58, Binary, Hex, ROT13, Vigenère)
- Steganography and metadata extraction
- FTP access and file analysis
- SSH authentication and privilege escalation

The purpose of this room is to provide an immersive, puzzle-based environment that tests enumeration skills, cryptography knowledge, and persistence in a narrative-driven format inspired by the survival horror game Resident Evil.

- **Target Machine:** Ubuntu Server running vsftpd 3.0.3, OpenSSH 7.6p1, and Apache 2.4.29
- **Attack Environment:** Kali Linux

---

## Task 1: Introduction & Initial Reconnaissance

### 1. Deploy the Machine

I started by deploying the target machine and allowing time for full initialization. This ensures all services are running properly before beginning reconnaissance.

### 2. Port Scanning

To identify available services, I performed a comprehensive port scan:
```bash
nmap -sV -sC -p- <target_ip>
```

**Open Ports Identified:**

- `21/tcp  open  ftp       vsftpd 3.0.3`
- `22/tcp  open  ssh       OpenSSH 7.6p1 Ubuntu`
- `80/tcp  open  http      Apache httpd 2.4.29`

The scan revealed three open ports. Since FTP and SSH require credentials I don't yet have, I focused my initial enumeration on the web service running on port 80.

**Answer to 1.2:** `3` open ports

### 3. Web Enumeration

I accessed the web server and examined the main page. The webpage displayed information about a STARS team operation, which immediately established the Resident Evil theme of this room.

By reading the main page content carefully, I found the team name mentioned in the operation details.

**Answer to 1.3:** `STARS alpha team`

---

## Task 2: The Mansion – Flag Collection

The mansion represents the main puzzle-solving phase where I needed to collect various item flags by navigating through different rooms, solving ciphers, and discovering hidden directories.

### Room 1: Dining Room & Emblem Discovery

#### Finding the First Directory

I navigated to `/mansionmain/` as indicated by the main page. I always check page source code for hidden clues, which is a fundamental web enumeration technique:
```bash
curl -s "http://<target_ip>/mansionmain/" | grep "<!--"
```

This revealed a comment pointing to `/diningRoom/`, demonstrating why source code analysis is crucial in web challenges.

#### Obtaining the Emblem Flag

In the dining room, I found a link to `emblem.php`. I retrieved the emblem flag:
```bash
curl -s "http://<target_ip>/diningRoom/emblem.php"
```

The page instructed me to refresh `/diningRoom/` after obtaining the flag, which is a common CTF mechanic where actions in one location affect another.

**Answer to 2.1:** `emblem{fec832623ea498e20bf4fe1821d58727}`

### Room 2: Tea Room & Lock Pick

#### Discovering the Tea Room

After refreshing the dining room as instructed, I found a submission form but no immediate progress. I checked the source code again and discovered a Base64-encoded comment:
```bash
curl -s "http://<target_ip>/diningRoom/" | grep "<!--"
echo "SG93IGFib3V0IHRoZSAvdGVhUm9vbS8=" | base64 -d
```

The decoded message revealed `/teaRoom/`, showing the importance of always decoding any encoded strings found during enumeration.

#### Obtaining the Lock Pick Flag

In the tea room, I found `master_of_unlock.html` which contained the lock pick flag:
```bash
curl -s "http://<target_ip>/teaRoom/master_of_unlock.html"
```

**Answer to 2.2:** `lock_pick{037b35e2ff90916a9abf99129c8e1837}`

### Room 3: Art Room & Mansion Map

#### Finding the Map

I explored the tea room content thoroughly and discovered a reference to `/artRoom/`. This room contained a crucial resource: `MansionMap.html`, which listed all available room locations:
```bash
curl -s "http://<target_ip>/artRoom/MansionMap.html"
```

This map became my reference guide for systematic exploration, listing rooms like:
- `/diningRoom/`
- `/teaRoom/`
- `/barRoom/`
- `/diningRoom2F/`
- `/tigerStatusRoom/`
- `/galleryRoom/`
- `/studyRoom/`
- `/armorRoom/`
- `/attic/`

Having a complete map allows for organized enumeration rather than random directory guessing.

### Room 4: Bar Room & Music Sheet

#### Using the Lock Pick

I navigated to `/barRoom/` where I could submit the lock pick flag. This is a good example of why keeping track of all collected flags is essential – they often serve as keys to unlock new areas.

After submission, I was redirected to a new subdirectory containing `musicNote.html`.

#### Decoding the Music Sheet

The music note contained a Base32-encoded string. Recognizing encoding types comes with practice – Base32 typically uses uppercase letters and numbers 2-7:
```bash
curl -s "http://<target_ip>/barRoom357162e3db904857963e6e0b64b96ba7/musicNote.html"
echo "NV2XG2LDL5ZWQZLFOR5TGNRSMQ3TEZDFMFTDMNLGGVRGIYZWGNSGCZLDMU3GCMLGGY3TMZL5" | base32 -d
```

**Answer to 2.3:** `music_sheet{362d72deaf65f5bdc63daece6a1f676e}`

### Room 5: Gold Emblem Discovery

#### Progressive Flag Submission

After submitting the music sheet flag, I accessed `barRoomHidden.php` which led to `gold_emblem.php`. This demonstrates the progressive nature of the room – each flag unlocks the next piece of the puzzle:
```bash
curl -s "http://<target_ip>/barRoom357162e3db904857963e6e0b64b96ba7/gold_emblem.php"
```

**Answer to 2.4:** `gold_emblem{58a8c41a9d08b8a4e38d02a4d7ff4843}`

### Room 6: Shield Key & Vigenère Cipher

#### Obtaining Rebecca's Name

I refreshed the hidden bar room page and submitted the emblem flag, which revealed the name `rebecca`. This name would later serve as a decryption key.

#### Decrypting the Vigenère Cipher

Back in the dining room, after submitting the gold emblem, I received an encrypted message:
```
klfvg ks r wimgnd biz mpuiui ulg fiemok tqod. Xii jvmc tbkg ks tempgf tyi_hvgct_jljinf_kvc
```

Vigenère ciphers require a key for decryption. Using the name `rebecca` as the key with an online Vigenère decoder, the message decrypted to:
```
there is a shield key inside the dining room. The html page is called the_great_shield_key
```

This shows the importance of keeping notes on all discovered information – the name seemed insignificant at first but became crucial later.

#### Retrieving the Shield Key

Following the decrypted instructions:
```bash
curl -s "http://<target_ip>/diningRoom/the_great_shield_key.html"
```

**Answer to 2.5:** `shield_key{48a7a9227cd7eb89f0a062590798cbac}`

### Room 7: Blue Gem & ROT13

#### Second Floor Exploration

I navigated to `/diningRoom2F/` and found another commented cipher in the source code:
```bash
curl -s "http://<target_ip>/diningRoom2F/" | grep "<!--"
```

This was a ROT13 cipher (rotating each letter 13 positions in the alphabet). ROT13 is recognizable because "Lbh" often appears as the first word, which decodes to "You":
```
You get the blue gem by pushing the status to the lower floor. The gem is on the diningRoom first floor. Visit sapphire.html
```

#### Collecting the Blue Gem

Following the decoded instructions:
```bash
curl -s "http://<target_ip>/diningRoom/sapphire.html"
```

**Answer to 2.6:** `blue_jewel{e1d457e96cac640f863ec7bc475d48aa}`

### Room 8: Tiger Status Room & The Four Crests

#### Submitting the Blue Gem

In `/tigerStatusRoom/`, I submitted the blue gem flag, which revealed that I needed to collect four crests, combine them, and decode the result to reveal a new path. This is the most complex puzzle in the mansion section.

#### Crest 1: Double Encoding (Base64 + Base32)

The first crest was already displayed on the page but was encoded twice. I needed to decode it from Base64 first, then decode the result from Base32:
```bash
echo "S0pXRkVVS0pKQkxIVVdTWUpFM0VTUlk9" | base64 -d | base32 -d
```

**Crest 1:** `RlRQIHVzZXI6IG`

The double encoding technique is common in CTFs to increase difficulty. Always try multiple decoding steps if a single decode produces gibberish.

#### Crest 2: Gallery Room (Base32 + Base58)

I navigated to `/galleryRoom/` and found a link to `note.txt` containing the second crest with helpful hints:
```bash
curl -s "http://<target_ip>/galleryRoom/note.txt"
```

The hints indicated double encoding and an 18-letter result. I decoded from Base32 first, then Base58:
```bash
echo "GVFWK5KHK5WTGTCILE4DKY3DNN4GQQRTM5AVCTKE" | base32 -d | base58 -d
```

**Crest 2:** `h1bnRlciwgRlRQIHBh`

Base58 encoding is commonly used in cryptocurrency addresses and doesn't use confusing characters like 0, O, I, and l.

#### Crest 3: Armor Room (Base64 + Binary + Hex)

In `/armorRoom/`, I submitted the shield key flag to access a hidden note containing the third crest. This crest required triple decoding:

1. **Base64 decode** – First layer
2. **Binary decode** – Second layer (the result contained 1s and 0s)
3. **Hex decode** – Final layer
```bash
curl -s "http://<target_ip>/armorRoom547845982c18936a25a9b37096b21fc1/note.txt"
```

The process demonstrates the importance of recognizing data formats. Binary data is obvious (only 0s and 1s), and hex typically contains 0-9 and A-F.

**Crest 3:** `c3M6IHlvdV9jYW50X2h`

#### Crest 4: Attic (Base58 + Hex)

In `/attic/`, I submitted the shield key again to access the fourth crest:
```bash
curl -s "http://<target_ip>/attic909447f184afdfb352af8b8a25ffff1d/note.txt"
```

This crest required double decoding from Base58 then Hex:
```bash
echo "gSUERauVpvKzRpyPpuYz66JDmRTbJubaoArM6CAQsnVwte6zF9J4GGYyun3k5qM9ma4s" | base58 -d | xxd -r -p
```

**Crest 4:** `pZGVfZm9yZXZlcg==`

#### Combining and Decoding All Crests

As instructed, I combined all four crests in order (1+2+3+4):
```
RlRQIHVzZXI6IGh1bnRlciwgRlRQIHBhc3M6IHlvdV9jYW50X2hpZGVfZm9yZXZlcg==
```

The equals signs at the end indicated Base64 encoding. Decoding revealed FTP credentials:
```bash
echo "RlRQIHVzZXI6IGh1bnRlciwgRlRQIHBhc3M6IHlvdV9jYW50X2hpZGVfZm9yZXZlcg==" | base64 -d
```

**Result:** `FTP user: hunter, FTP pass: you_cant_hide_forever`

This complex multi-stage puzzle demonstrates the importance of careful note-taking and systematic approach to cryptography challenges.

**Answer to 2.7:** `hunter`  
**Answer to 2.8:** `you_cant_hide_forever`

---

## Task 3: The Guard House – FTP Analysis

### Accessing FTP and Downloading Files

With the credentials obtained from the crest puzzle, I connected to the FTP service:
```bash
ftp <target_ip>
```

I logged in with username `hunter` and password `you_cant_hide_forever`, then listed all files including hidden ones:
```bash
ls -la
```

**Files found:**
- `001-key.jpg`
- `002-key.jpg`
- `003-key.jpg`
- `helmet_key.txt.gpg` (encrypted file)
- `important.txt`

I downloaded all files for local analysis:
```bash
mget *
```

### Analyzing important.txt

Reading the text file revealed crucial information:
```bash
cat important.txt
```

The note from Barry mentioned:
1. The helmet key is in an encrypted text file
2. There's a locked `/hidden_closet/` directory

**Answer to 3.1:** `/hidden_closet/`

### Extracting Keys from Images

The three JPEG files contained hidden keys that needed to be extracted using different steganography techniques.

#### Key 1: Steghide Extraction

Steghide is a tool for hiding data in image and audio files. I extracted data from the first image with no passphrase:
```bash
steghide extract -sf 001-key.jpg
cat key-001.txt
```

**Key 1:** `cGxhbnQ0Ml9jYW`

This demonstrates the importance of trying steganography tools on images found during CTF challenges.

#### Key 2: EXIF Metadata

EXIF data stores metadata about images, including camera settings, timestamps, and sometimes hidden messages. I used `exiftool` to examine the second image:
```bash
exiftool 002-key.jpg
```

The key was hidden in the Comment field of the EXIF data:

**Key 2:** `5fYmVfZGVzdHJveV9`

Always check image metadata as it's a common hiding spot for clues.

#### Key 3: Binwalk Extraction

Binwalk analyzes and extracts embedded files and data from binary images. I used it on the third image:
```bash
binwalk 003-key.jpg -e
cd _003-key.jpg.extracted
cat key-003.txt
```

The `-e` flag extracts any discovered embedded files. Binwalk found a ZIP archive containing the third key:

**Key 3:** `3aXRoX3Zqb2x0`

### Combining Keys and Decrypting GPG File

I combined all three keys in order:
```
cGxhbnQ0Ml9jYW5fYmVfZGVzdHJveV93aXRoX3Zqb2x0
```

This appeared to be Base64 encoded (evident from the character set and padding). Decoding it:
```bash
echo "cGxhbnQ0Ml9jYW5fYmVfZGVzdHJveV93aXRoX3Zqb2x0" | base64 -d
```

**Result:** `plant42_can_be_destroy_with_vjolt`

**Answer to 3.2:** `plant42_can_be_destroy_with_vjolt`

### Decrypting the Helmet Key

Using the decoded password, I decrypted the GPG file:
```bash
gpg helmet_key.txt.gpg
cat helmet_key.txt
```

**Answer to 3.3:** `helmet_key{458493193501d2b94bbab2e727f8db4b}`

---

## Task 4: The Revisit – SSH Credentials

### Study Room & SSH Username

I returned to `/studyRoom/`, which I had skipped earlier. With the helmet key, I could now access it. After submitting the helmet key flag, I gained access to a new directory containing a downloadable tar archive.

I downloaded and extracted the archive:
```bash
tar -xvf doom.tar.gz
cat eagle_medal.txt
```

**Answer to 4.1:** `umbrella_guest`

### Hidden Closet & SSH Password

I navigated to the previously discovered `/hidden_closet/` directory and submitted the helmet key to enter. Inside, I found two important files:

1. **MO_DISK1.txt** – Another Vigenère cipher (would need a key later)
2. **wolf_medal.txt** – Contains the SSH password
```bash
curl -s "http://<target_ip>/hiddenCloset8997e740cb7f5cece994381b9477ec38/wolf_medal.txt"
```

**Answer to 4.2:** `T_virus_rules`

### STARS Bravo Team Leader

The hidden closet page content mentioned the STARS Bravo team leader. By reading the text carefully:
```bash
curl -s "http://<target_ip>/hiddenCloset8997e740cb7f5cece994381b9477ec38/" | grep -i "bravo"
```

**Answer to 4.3:** `Enrico`

---

## Task 5: Underground Laboratory – Root Access

### SSH Access and Initial Enumeration

With SSH credentials (`umbrella_guest:T_virus_rules`), I connected to the system:
```bash
ssh umbrella_guest@<target_ip>
```

After logging in, I performed basic enumeration to understand the environment:
```bash
ls -la
```

I found a hidden directory named `.jailcell`, which stood out as unusual. Hidden directories (starting with a dot) often contain important clues in CTFs.

**Answer to 5.1:** `jailcell`

### Finding the Traitor

I navigated to the hidden directory and read the file:
```bash
cd .jailcell
cat chris.txt
```

The text revealed a conversation between Chris and Jill, exposing the traitor's identity and providing "MO Disk 2" with the key `albert`.

**Answer to 5.2:** `weasker`

### Decrypting Weasker's Password

I now had the key (`albert`) to decrypt the Vigenère cipher found earlier in `/hidden_closet/`. Using an online Vigenère decoder with the key, the encrypted message:
```
wpbwbxr wpkzg pltwnhro, txrks_xfqsxrd_bvv_fy_rvmexa_ajk
```

Decrypted to:
```
weasker login password, stars_members_are_my_guinea_pig
```

**Answer to 5.3:** `stars_members_are_my_guinea_pig`

### Lateral Movement to Weasker

I switched to the weasker user account:
```bash
su weasker
```

After entering the password, I navigated to weasker's home directory:
```bash
cd ~
ls
cat weasker_note.txt
```

The note revealed a conversation about the ultimate lifeform called the "Tyrant," which tied back to the Resident Evil narrative.

**Answer to 5.4:** `Tyrant`

### Privilege Escalation to Root

To check for privilege escalation opportunities, I examined weasker's sudo permissions:
```bash
sudo -l
```

The output showed:
```
User weasker may run the following commands on umbrella_corp:
    (ALL : ALL) ALL
```

This meant weasker could run any command as root with sudo privileges. This is a misconfiguration that allows trivial privilege escalation. I escalated to root:
```bash
sudo su
```

I verified root access:
```bash
id
```

Output confirmed: `uid=0(root) gid=0(root) groups=0(root)`

### Capturing the Root Flag

I navigated to the root directory and read the final flag:
```bash
cd /root
ls
cat root.txt
```

The root.txt file contained the conclusion of the Resident Evil narrative and the final flag.

**Answer to 5.5:** `3c5794a00dc56c35f2bf096571edf3bf`

---

## Summary

This walkthrough demonstrated a comprehensive range of penetration testing and CTF skills:

**Reconnaissance & Enumeration:**
- Nmap service scanning
- Web directory enumeration
- Source code analysis

**Cryptography & Encoding:**
- Base64, Base32, Base58 decoding
- Binary and hexadecimal conversion
- ROT13 cipher
- Vigenère cipher with key-based decryption

**Steganography:**
- Steghide extraction
- EXIF metadata analysis
- Binwalk file carving

**File Analysis:**
- GPG decryption
- Archive extraction
- Multi-stage encoded data

**System Exploitation:**
- FTP authentication
- SSH access
- Lateral movement between users
- Sudo misconfiguration exploitation

> **The TryHackMe Biohazard room is an excellent narrative-driven challenge that tests enumeration skills, cryptographic knowledge, and systematic problem-solving. The Resident Evil theme adds an engaging story element while teaching fundamental CTF techniques.**

The key to success in this room was maintaining detailed notes of all discovered flags, keys, and credentials, as each piece built upon the previous discoveries. Systematic exploration and trying multiple decoding methods were essential for progress.
