#  Windows Fundamentals 2 — TryHackMe

Explore System Configuration, UAC settings, Resource Monitoring, the Windows Registry, and more.

---

##  Topics Covered
- Computer Management  
- System Information  
- Resource Monitor  
- Command Prompt  
- Registry Editor  

---

#  Computer Management (`compmgmt`)

The **Computer Management** utility has three main sections:

### 1️ System Tools
- **Task Scheduler** – Automate tasks (run apps, scripts) on schedule, logon/logoff, or specific triggers.  
- **Event Viewer** – View system and application events as an audit trail to diagnose issues.  

### 2️ Storage
- **Disk Management** – Perform advanced storage tasks:  
  - Set up a new drive  
  - Extend/Shrink partitions  
  - Assign or change drive letters  

### 3️ Services & Applications
- **WMI (Windows Management Instrumentation)** – Manage PCs locally/remotely via scripting (VBScript, PowerShell).  
- **WMIC** – Command-line interface for WMI.  

---

#  System Information (`msinfo32`)

- Provides a **comprehensive view** of hardware, system components, and software.  
- **System Summary** – General specifications, e.g., CPU model/brand.  
- **Components** – Hardware devices (Display, Input, etc.).  
- **Software Environment** – Installed software, environment variables, network connections.  

---

#  Resource Monitor (`resmon`)

- Monitors **CPU, memory, disk, and network usage** (per process and aggregate).  
- Advanced filtering allows:
  - Isolate processes or services  
  - Start, stop, pause, resume services  
  - Close unresponsive applications  

---

#  Command Prompt (`cmd`)

- CLI interface to interact with Windows.  
- Even though GUI is primary, **cmd** allows advanced configuration, scripting, and troubleshooting.  
- Many commands are still essential for sysadmin and SOC operations.  

---

#  Registry Editor (`regedt32`)

- Windows Registry is a **central hierarchical database** storing configuration for users, apps, and hardware.  
- Windows references registry continually during operation.  
- Registry can be viewed/edited via:
- **Registry Editor (`regedt32`)**  
- PowerShell / Command-line tools  
