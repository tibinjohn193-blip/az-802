# 🖥️ Lab 3: Active Directory Forest Deployment & Static IP Configuration

Welcome to Lab 3! In this interactive simulation, you will step into the shoes of a System Administrator to perform one of the most critical tasks in a Windows environment: configuring a Static IP and deploying a new **Active Directory Forest** from scratch.

---

## 🧠 Core Concepts: Active Directory Fundamentals

Before executing the lab, it is crucial to understand the terminology and architecture behind Active Directory. These are common interview topics for IT professionals:

### 1. What is AD DS?
**Active Directory Domain Services (AD DS)** is Microsoft's primary identity and access management service. It acts as a centralized phonebook and security authority for a network, allowing administrators to manage users, computers, passwords, and security policies from one single location.

### 2. Domain & Forest
* **Domain:** A logical group of network objects (computers, users, devices) that share the same Active Directory database and security policies. (e.g., `smec.com`).
* **Forest:** The highest level of organization within Active Directory. A forest is a collection of one or more domains that share a common logical structure, schema, and global catalog. When you create the very first domain, you are implicitly creating a new Forest.

### 3. The Active Directory Database (`ntds.dit`)
Active Directory is ultimately a database. All the users, computers, groups, and password hashes you create are stored in a single physical file.
* **File Name:** `ntds.dit`
* **Full Form:** **N**ew **T**echnology **D**irectory **S**ervices **. D**irectory **I**nformation **T**ree.
* **Default Location:** `C:\Windows\NTDS\`
* *(Note: During the promotion wizard in the lab, you will see a specific step asking to confirm this path along with the SYSVOL folder).*

### 4. Active Directory Protocols
AD DS relies on several underlying network protocols to function correctly:
* **LDAP (Lightweight Directory Access Protocol):** The core protocol used to read from and write to the Active Directory database. (Port 389 / 636 for Secure LDAP).
* **Kerberos:** The primary authentication protocol used by Active Directory to securely verify user passwords and grant access tickets. (Port 88).
* **DNS (Domain Name System):** AD cannot function without DNS. DNS is used by client computers to locate the Domain Controller on the network. (Port 53).

### 5. What is the DSRM Password?
**Directory Services Restore Mode (DSRM)** is a special safe mode boot option for Windows Server Domain Controllers. If the Active Directory database crashes or gets corrupted, you must boot into DSRM to repair or restore it from a backup. The DSRM password is the "offline local administrator" password used to log in when Active Directory is broken.

---

## 🎯 Lab Objectives & Workflow

In this browser-based simulation, you will experience a 100% authentic Windows Server 2025 interface to complete the following tasks:

1. **Host Interaction:** Launch VMware Workstation, boot the server, and enter **Full Screen** mode.
2. **Secure Login:** Send the `Ctrl+Alt+Delete` command to unlock the server and log in.
3. **Network Configuration:** Use the Run dialog (`ncpa.cpl`) to open Network Connections and assign a Static IPv4 address (`192.168.10.1`) and DNS server.
4. **Role Installation:** Open Server Manager and use the wizard to install the **Active Directory Domain Services** role.
5. **Domain Promotion:** Click the Post-Deployment Configuration warning flag (⚠️) to promote the server to a Domain Controller.
6. **Forest Creation:** Configure the Root Domain Name (`smec.com`), set the DSRM password, verify the NetBIOS name, and complete the installation.

---

## 🚀 Launch the Interactive Simulation

Ready to configure your first Active Directory Forest? Click the link below to start the simulation!

### 👉 [LAUNCH LAB 3: AD DS Forest Deployment](https://tibinjohn193-blip.github.io/az-802/lab3.html) 👈

