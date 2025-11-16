#  Active Directory Fundamentals - TryHackMe

This room introduces the basic concepts and functionality provided by **Active Directory (AD)**.

---

##  Topics Covered
- Windows Domains  
- Users, Computers, and Security Groups  
- Organizational Units (OUs) & Delegation  
- Group Policies (GPOs)  
- Authentication Protocols (Kerberos & NetNTLM)  
- Trees, Forests, and Trust Relationships  

---

#  Windows Domains

- **Centralized credential storage:** Active Directory stores user credentials and privileges.  
- **Domain Controller (DC):** Server running AD services.  
- **Advantages of a domain:**
  - Centralized identity management  
  - Centralized security policies  
  - Easier user/computer administration  

**Example:** School/university networks use domain credentials to allow login on any machine without creating local accounts.

**THM Practice:** Accessing `THM.local` via RDP using **Remmina**.  
- Username: `Administrator`  
- Password: `Password321`  

---

#  Active Directory Objects

### 1️ Users
- Can represent **people** or **services** (IIS, SQL, etc.).  
- Security principals: authenticated by the domain, can have permissions assigned.  

### 2️ Computers / Machines
- Each computer joining a domain gets a **machine account** (e.g., `TOM-PC$`).  
- Machine accounts are local admins on their computers and passwords are automatically rotated.  

### 3️ Security Groups
- Assign permissions to multiple users/machines at once.  
- Default important groups:
  - **Domain Admins:** Admin privileges over the entire domain  
  - **Server Operators:** Manage Domain Controllers  
  - **Backup Operators:** Backup files ignoring permissions  
  - **Account Operators:** Create/modify accounts  
  - **Domain Users & Computers**  

---

#  Organizational Units (OUs) & Delegation

- **OUs:** Containers to organize users and computers for policy application.  
- **Delegation:** Grant a user control over specific OUs without giving full Domain Admin privileges.  
- Example: IT support user can reset passwords for low-privilege users.  

**Practice:** Resetting Sophie’s password via PowerShell:  
```
powershell
Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose
```

# Group Policies (GPOs)

- SYSVOL: Network share for distributing GPOs to domain machines.
- Can apply settings to both users and computers.
- Separate OUs for Servers and Workstations is recommended for management.

# Authentication Methods
**Kerberos**
- Default authentication protocol in modern Windows domains.
- Uses Ticket Granting Tickets (TGT) and Ticket Granting Service (TGS) tickets to authenticate without sending passwords over the network.
**NetNTLM**
- Legacy challenge-response authentication.
- Passwords are never transmitted in plaintext.
- Should be considered obsolete but may still be enabled for compatibility.

# Trees, Forests & Trust Relationships
**Trees**
- Multiple domains sharing the same namespace (e.g., thm.local, uk.thm.local).
- Domains can be managed independently within a tree.
**Forests**
- Union of multiple trees with different namespaces.
- Enables enterprise-level domain organization.
**Trust Relationships**
- Allow users in one domain to access resources in another domain.
- Can be one-way (AAA trusts BBB) or two-way (mutual trust).
- Trust relationships do not automatically grant access—permissions still need to be assigned.
