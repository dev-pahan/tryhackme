# Windows Fundamentals 1 - TryHackMe

Learn about the Windows desktop, the NTFS file system, UAC, the Control Panel, Task Manager, and more.

---

##  Topics Covered
- Windows Editions  
- The File System (NTFS)  
- Windows / System32 Folders  
- Users & Permissions  
- UAC  
- Control Panel  
- Task Manager  

---

#  Windows Editions

Common editions of Windows include:

- Windows XP  
- Windows Vista  
- Windows 7  
- Windows 10  
- Windows 11  

 **Note:** Windows 11 *Pro* supports BitLocker encryption, while Window


### `C:\Windows\System32`
Contains **critical operating system files** essential for Windows functionality.

---

#  Users & Account Types

Windows has two common local account types:

###  Administrator
- Can make system changes  
- Add/remove users  
- Modify groups & system settings  
- Install software  

###  Standard User
- Can only modify their own files  
- Cannot make system-level changes  

Use the following command to view local users & groups:


---

#  User Account Control (UAC)

UAC helps prevent malware from executing with full admin rights.

How UAC works:
- Even if an admin logs in, the session runs **without elevated privileges** by default.
- When an operation needs admin rights, Windows prompts the user to confirm.

---

#  Control Panel

The **Settings app** was introduced in Windows 8, but the **Control Panel** still handles many advanced system configurations.

Sometimes Windows routes you from Settings → Control Panel for deeper settings.

---

#  Task Manager

Shows:
- Running applications  
- Background processes  
- CPU, RAM, Disk, Network usage (Performance tab)
