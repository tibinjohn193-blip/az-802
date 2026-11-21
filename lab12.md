# Read-Only Domain Controller (RODC) Setup Lab — Windows Server 2025 Simulator

<div align="center">

### 🔒 Branch Office Active Directory Security — Interactive RODC Lab (`lab12.html`)

[![Click to Launch Interactive Lab](https://img.shields.io/badge/▶_CLICK_TO_START-INTERACTIVE_RODC_LAB_(lab12.html)-4338ca?style=for-the-badge&logo=windows&logoColor=white)](https://tibinjohn193-blip.github.io/az-802/lab12.html)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab12.html>

</div>

An interactive, single-file web-based **Windows Server 2025 Dual-Server Simulator** designed for hands-on Active Directory Domain Services (AD DS) training. This lab walks students step-by-step through verifying an existing writable Primary Domain Controller (`SMEC-DC1`), configuring static IP/DNS on a second server (`SMEC-RODC`), joining it to `smec.com`, promoting it as a **Read-Only Domain Controller (RODC)** with a **Password Replication Policy (PRP)**, testing existing AD user sign-in, and verifying that creating new AD objects is blocked on an RODC.

---

## Table of Contents

1. [What is a Read-Only Domain Controller (RODC)?](#what-is-a-read-only-domain-controller-rodc)
2. [Writable DC (ADC) vs. Read-Only DC (RODC)](#writable-dc-adc-vs-read-only-dc-rodc)
3. [Lab Environment Specifications](#lab-environment-specifications)
4. [Lab Credentials](#lab-credentials)
5. [Simulator Interface & Features](#simulator-interface--features)
6. [Complete 14-Step Lab Walkthrough](#complete-14-step-lab-walkthrough)
7. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
8. [How to Run & Deploy](#how-to-run--deploy)
9. [Technology Stack](#technology-stack)

---

## What is a Read-Only Domain Controller (RODC)?

A **Read-Only Domain Controller (RODC)** is a specialized type of Active Directory Domain Controller that hosts a **read-only copy** of the Active Directory database (`NTDS.dit`). Unlike a standard Additional Domain Controller (ADC), an RODC **cannot** accept direct write operations (such as creating users, resetting passwords, or modifying OUs) locally.

### Why Do Organizations Use RODCs?
RODCs are specifically designed for **remote branch offices, retail stores, or perimeter networks** where physical security is limited and dedicated IT staff are not on-site. If a physical server in a branch office is stolen or compromised, an RODC drastically minimizes the security impact:

* **Read-Only AD Database:** Attackers cannot modify Active Directory objects locally or replicate malicious changes back to the headquarters Domain Controllers.
* **Unidirectional Replication:** Changes only flow **one way** — from a writable Domain Controller (`SMEC-DC1`) down to the RODC (`SMEC-RODC`). The RODC never replicates changes back upstream.
* **Password Replication Policy (PRP):** By default, an RODC stores **zero user passwords** (except its own machine account and `krbtgt` account). Administrators explicitly choose which branch-office users (e.g., `SMEC\Arun`) are **Allowed** to have their password hashes cached locally for faster logins. High-privilege accounts (**Domain Admins**, **Enterprise Admins**, **Schema Admins**) are **Denied** by default.
* **Administrator Role Separation:** A local branch technician can be delegated local administrative rights on the RODC to perform maintenance (such as updating drivers or restarting services) without being granted full Domain Admin rights across the entire company.
* **Read-Only DNS:** An RODC can host a read-only copy of the AD-integrated DNS zone for local name resolution.

---

## Writable DC (ADC) vs. Read-Only DC (RODC)

| Feature | Additional Domain Controller (ADC) | Read-Only Domain Controller (RODC) |
| :--- | :--- | :--- |
| **AD Database (`NTDS.dit`)** | Read / Write | **Read-Only** |
| **Create / Edit Users in ADUC** | Allowed | **Blocked** (Must be done on a Writable DC) |
| **Replication Direction** | Bidirectional (Multi-Master) | **Unidirectional** (Writable DC $\rightarrow$ RODC only) |
| **Cached Passwords** | Stores all domain password hashes | **Only accounts allowed** by Password Replication Policy |
| **Best Use Case** | Secure Data Centers / Headquarters | Branch Offices / Low Physical Security Sites |

---

## Lab Environment Specifications

| Component | Server 1 (PDC) | Server 2 (Target RODC) |
| :--- | :--- | :--- |
| **Hostname** | `SMEC-DC1` | `SMEC-RODC` |
| **Operating System** | Windows Server 2025 | Windows Server 2025 |
| **IP Address** | `192.168.1.1` | `192.168.1.2` *(Configured in Lab)* |
| **Subnet Mask** | `255.255.255.0` | `255.255.255.0` *(Configured in Lab)* |
| **Preferred DNS Server** | `192.168.1.1` | `192.168.1.1` *(Points to PDC)* |
| **Active Directory Domain** | `smec.com` | Joins `smec.com` $\rightarrow$ Promoted to RODC |
| **Existing Tech OU Users** | `Arun`, `User1`, `User2` | Replicated read-only from `SMEC-DC1` |

---

## Lab Credentials

| Account / Purpose | Username | Password |
| :--- | :--- | :--- |
| **Domain Administrator** (Domain Join & Promotion) | `smec\administrator` | `password123` |
| **DSRM Password** (Directory Services Restore Mode) | *(N/A)* | `password123` |
| **Existing AD Users** (RODC PRP & Login Test) | `smec\Arun`, `smec\User1`, `smec\User2` | `password123` |

---

## Simulator Interface & Features

The simulator (`lab12.html`) provides a full-screen **Dual-Server Switcher** layout with zero overlapping windows:

1. **Left Sidebar (Server Switcher & Reference):**
   * Switch cleanly between **🖥️ Server 1 (PDC — `SMEC-DC1`)** and **🔒 Server 2 (RODC — `SMEC-RODC`)**.
   * **Attention Pulse Highlight:** Automatically pulses a yellow border around the Server 1 or Server 2 button when the current step requires switching servers.
   * Displays live status updates (`DHCP` $\rightarrow$ `192.168.1.2` $\rightarrow$ `Member: smec.com` $\rightarrow$ `RODC: smec.com 🔒`), a quick **"What is an RODC?"** summary card, credentials, and a **Reset Lab** button.
2. **Main Workspace Stage:**
   * **Authentic Manual IP & Domain Configuration:** Students manually enter IPv4 settings in `ncpa.cpl` and join `smec.com` in `sysdm.cpl`.
   * **Authentic Windows User Picker (`Select...` & `Check Names`):** On the **RODC Options** screen, clicking **Select...** opens the realistic *Select Users, Computers, Service Accounts, or Groups* dialog where students type a username (`Arun`, `User1`, or `User2`) and click **Check Names** to resolve it before adding it to the Allowed Password Replication Policy.
   * **Live RODC Verification:** Tests both successful sign-in with an existing AD user (`smec\Arun`) and the authentic Windows error dialog blocking `New -> User...` inside ADUC on `SMEC-RODC`.
3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed below the desktop stage so it never obscures Start menus, login screens, or wizard controls.
   * Tracks progress from **Step 1 / 14** to **100% Complete**.

---

## Complete 14-Step Lab Walkthrough

### Phase 1: Verify AD DS & Existing Users on Primary DC (`SMEC-DC1`)
1. **Verify AD DS Installation:** On **SMEC-DC1**, click **Start $\rightarrow$ Run**, type `dsa.msc`, and click **OK** (or click the **AD Users & Computers** desktop icon).
2. **Inspect Existing Users:** Click the **Tech** OU under `smec.com` to confirm existing AD users **Arun** (`arun@smec.com`), **User1** (`user1@smec.com`), and **User2** (`user2@smec.com`).

### Phase 2: Configure Static IP & DNS (`SMEC-RODC`)
3. **Open Network Connections:** Switch to **🔒 Server 2 (RODC)** in the left sidebar. Open **Start $\rightarrow$ Run**, type `ncpa.cpl`, and click **OK**.
4. **Open IPv4 Properties:** Right-click **Ethernet** $\rightarrow$ **Properties** $\rightarrow$ double-click **Internet Protocol Version 4 (TCP/IPv4)**.
5. **Enter Static IP & DNS:** Manually configure:
   * **IP address:** `192.168.1.2`
   * **Subnet mask:** `255.255.255.0`
   * **Preferred DNS server:** `192.168.1.1` *(PDC IP)*
   Click **OK** to save.

### Phase 3: Join Domain & Sign In as Domain Admin (`SMEC-RODC`)
6. **Open System Properties:** Click **Start $\rightarrow$ Run**, type `sysdm.cpl`, and click **OK**.
7. **Join `smec.com`:** Click **Change...** $\rightarrow$ select **Domain** $\rightarrow$ type `smec.com` $\rightarrow$ click **OK**. Authenticate with `smec\administrator` / `password123` and allow the server to restart.
8. **Domain Admin Sign-In:** After restart, sign in as Domain Administrator using `smec\administrator` and `password123`.

### Phase 4: Install AD DS Role (`SMEC-RODC`)
9. **Open Server Manager:** Launch **Server Manager** and click **+ Add roles and features**.
10. **Install AD DS:** Navigate to **Server Roles**, check **Active Directory Domain Services**, click through to **Confirmation**, and click **Install**. Once complete, click **Close**.

### Phase 5: Promote Server to Read-Only Domain Controller (`SMEC-RODC`)
11. **Launch Promotion Wizard:** Click the yellow **🚩 Notifications** flag in Server Manager and click **Promote this server to a domain controller**.
12. **Configure RODC & Password Replication Policy:**
    * **Deployment Configuration:** Select **Add a domain controller to an existing domain** (`smec.com`).
    * **Domain Controller Options:** Keep **DNS** and **Global Catalog (GC)** checked, check **🔒 Read only domain controller (RODC)**, and enter/confirm the DSRM password (`password123`).
    * **RODC Options (PRP):** Click **Select...** next to *Accounts allowed to replicate passwords to the RODC*. Type `Arun` (or `User1` / `User2`), click **Check Names** to resolve `Arun (arun@smec.com)`, and click **OK**.
    * **Additional Options / Paths / Review:** Keep replication source as `SMEC-DC1.smec.com` and click **Next**.
    * **Prerequisites Check:** Verify all checks pass and click **Install**. Wait for promotion and automatic restart.

### Phase 6: Test User Login & Read-Only Restriction (`SMEC-RODC`)
13. **Verify Existing AD User Login:** After restart, sign in to **SMEC-RODC** using an existing AD user (e.g., `smec\Arun` or `smec\User1` with password `password123`) to confirm domain authentication works on the RODC.
14. **Verify Write Operations Are Blocked:** Open **AD Users & Computers** (`Start -> Run -> dsa.msc` or Desktop icon) on **SMEC-RODC**:
    * Click the **Tech** OU to view the replicated read-only user list and PRP cached status.
    * **Right-click Tech OU $\rightarrow$ New $\rightarrow$ User...**.
    * Observe the authentic Windows error blocking new user creation on a Read-Only Domain Controller, click **OK**, and achieve **100% Lab Completion**.

---

## Key Technical Concepts & Best Practices

* **Password Caching Behavior:** If a user who is *not* in the Allowed Password Replication Policy logs in at a branch office, the RODC forwards the authentication request over the WAN to a writable DC (`SMEC-DC1`) without caching the password locally.
* **What Happens if an RODC is Stolen?** Because only a small subset of branch users' credentials are cached on the RODC, administrators can quickly run the **Reset all passwords for accounts that were cached on this RODC** wizard on the primary DC without having to reset passwords for the entire enterprise or Domain Admins.
* **Domain Functional Level:** Deploying an RODC requires at least a **Windows Server 2003** forest functional level (with `adprep /rodcprep` run) and at least one writable Domain Controller running Windows Server 2008 or later.

---

## How to Run & Deploy

### Launch Live Online
Open the hosted simulator directly in your browser:
> **https://tibinjohn193-blip.github.io/az-802/lab12.html**

### Run Locally (Offline)
1. Download `lab12.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

---

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server 2025 UI styling, sidebar attention indicators)
* **Vanilla JavaScript** (14-step validation engine, IPv4 & credential validators, authentic `Select...` / `Check Names` object picker, AD DS & RODC promotion wizards, and read-only write-block simulation)