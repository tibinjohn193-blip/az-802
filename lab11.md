# Additional Domain Controller (ADC) Setup Lab — Windows Server 2025 Simulator

<div align="center">

### 🏢 Active Directory High Availability — Interactive ADC Lab (`lab11.html`)

[![Click to Launch Interactive Lab](https://img.shields.io/badge/▶_CLICK_TO_START-INTERACTIVE_LAB_(lab11.html)-0078D7?style=for-the-badge&logo=windows&logoColor=white)](https://tibinjohn193-blip.github.io/az-802/lab11.html)

**Live URL:** [https://tibinjohn193-blip.github.io/az-802/lab11.html](https://tibinjohn193-blip.github.io/az-802/lab11.html)

</div>

---

An interactive, single-file web-based **Windows Server 2025 Dual-Server Simulator** designed for hands-on Active Directory Domain Services (AD DS) training. This lab guides students step-by-step through verifying an existing Primary Domain Controller (`SMEC-DC1`), configuring static IP/DNS on a second server (`SMEC-DC2`), joining it to the `smec.com` domain, installing the AD DS role, promoting it as an **Additional Domain Controller (ADC)**, and verifying automatic Active Directory replication.

---

## Table of Contents

1. [Overview & Learning Objectives](#overview--learning-objectives)
2. [Lab Environment Specifications](#lab-environment-specifications)
3. [Lab Credentials](#lab-credentials)
4. [Simulator Interface & Features](#simulator-interface--features)
5. [Complete 13-Step Lab Walkthrough](#complete-13-step-lab-walkthrough)
6. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
7. [How to Run & Deploy](#how-to-run--deploy)
8. [Technology Stack](#technology-stack)

---

## Overview & Learning Objectives

Running an Active Directory domain with only a single Domain Controller creates a **single point of failure**. If that server goes offline, users cannot authenticate, DNS resolution fails, and Group Policies stop applying. Deploying an **Additional Domain Controller (ADC)** provides **fault tolerance**, **high availability**, and **load balancing** for authentication and directory queries.

By completing this lab, students will learn how to:

* Inspect existing OUs and user accounts on a Primary Domain Controller using **ADUC (`dsa.msc`)**.
* Configure static IPv4 addressing and point the **Preferred DNS Server** to the existing Domain Controller (`192.168.1.1`) using `ncpa.cpl`.
* Join a standalone Windows Server to an existing Active Directory domain (`smec.com`) via `sysdm.cpl` and authenticate as a Domain Administrator.
* Install the **Active Directory Domain Services (AD DS)** role using **Server Manager**.
* Promote a member server to an **Additional Domain Controller (ADC)** with DNS, Global Catalog (GC), and DSRM password configuration.
* Verify multi-master **Active Directory replication** by confirming that existing OUs (`Tech`) and users (`Arun`) automatically appear on the newly promoted ADC.

---

## Lab Environment Specifications

| Component | Server 1 (PDC) | Server 2 (Target ADC) |
| :--- | :--- | :--- |
| **Hostname** | `SMEC-DC1` | `SMEC-DC2` |
| **Operating System** | Windows Server 2025 | Windows Server 2025 |
| **IP Address** | `192.168.1.1` | `192.168.1.2` *(Configured in Lab)* |
| **Subnet Mask** | `255.255.255.0` | `255.255.255.0` *(Configured in Lab)* |
| **Preferred DNS Server** | `192.168.1.1` | `192.168.1.1` *(Points to PDC)* |
| **Active Directory Domain** | `smec.com` | Joins `smec.com` -> Promoted to ADC |
| **Sample OU & User** | `Tech` OU -> `Arun` (`arun@smec.com`) | Replicated automatically from `SMEC-DC1` |

---

## Lab Credentials

| Account / Purpose | Username | Password |
| :--- | :--- | :--- |
| **Domain Administrator** (Domain Join & Sign-In) | `smec\administrator` | `password123` |
| **DSRM Password** (Directory Services Restore Mode) | *(N/A)* | `password123` |

---

## Simulator Interface & Features

The simulator (`lab11.html`) uses a clean, full-screen **Dual-Server Switcher** layout:

1. **Left Sidebar (Server Switcher & Quick Reference):**
   * Switch cleanly between **🖥️ Server 1 (PDC — `SMEC-DC1`)** and **🖥️ Server 2 (ADC — `SMEC-DC2`)**.
   * **Attention Pulse Highlight:** Automatically pulses a yellow border around the Server 1 or Server 2 button whenever the current step requires switching servers.
   * Displays live status updates for `SMEC-DC2` (DHCP -> `192.168.1.2` -> Member Server -> `ADC: smec.com ✓`) along with credentials and a **Reset Lab** button.
2. **Main Workspace Stage:**
   * **Authentic Manual Configuration:** No auto-filled IP fields—students manually type `192.168.1.2`, `255.255.255.0`, and `192.168.1.1` inside `ncpa.cpl`.
   * **Complete Windows Workflows:** Includes `Run` (`dsa.msc`, `ncpa.cpl`, `sysdm.cpl`), `System Properties`, `Windows Security` domain-join prompt, simulated server restarts, `Other user` domain logon screen, **Server Manager**, **Add Roles and Features Wizard**, **🚩 Post-Deployment Notifications Flag**, and the **AD DS Configuration Wizard**.
3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed cleanly below the desktop stage so it never overlaps Start menus, login screens, or wizard buttons.
   * Tracks progress from **Step 1 / 13** to **100% Complete**.

---

## Complete 13-Step Lab Walkthrough

### Phase 1: Verify Primary Domain Controller (`SMEC-DC1`)
1. **Open ADUC on Server 1:** On **SMEC-DC1**, click **Start -> Run**, type `dsa.msc`, and click **OK** (or click the **AD Users & Computers** desktop icon).
2. **Inspect `Tech` OU:** Click the **Tech** OU under `smec.com` to confirm that user **Arun** (`arun@smec.com`) exists on the Primary Domain Controller.

### Phase 2: Configure Static IP & DNS (`SMEC-DC2`)
3. **Open Network Connections:** Switch to **🖥️ Server 2 (ADC)** on the left sidebar. Click **Start -> Run**, type `ncpa.cpl`, and click **OK**.
4. **Open IPv4 Properties:** Right-click **Ethernet** -> **Properties** -> double-click **Internet Protocol Version 4 (TCP/IPv4)**.
5. **Enter Static IP & DNS:** Manually configure:
   * **IP address:** `192.168.1.2`
   * **Subnet mask:** `255.255.255.0`
   * **Preferred DNS server:** `192.168.1.1` *(PDC IP so `SMEC-DC2` can locate `smec.com`)*
   Click **OK** to save.

### Phase 3: Join Domain & Sign In as Domain Admin (`SMEC-DC2`)
6. **Open System Properties:** Click **Start -> Run**, type `sysdm.cpl`, and click **OK**.
7. **Join `smec.com`:** Click **Change...** -> select **Domain** -> type `smec.com` -> click **OK**. Authenticate with `smec\administrator` / `password123`, accept the welcome prompt, and let the server restart.
8. **Domain Admin Sign-In:** On the post-restart login screen, sign in as Domain Administrator using `smec\administrator` and `password123`.

### Phase 4: Install AD DS Role (`SMEC-DC2`)
9. **Launch Server Manager:** Open **Server Manager** and click **+ Add roles and features**.
10. **Install AD DS:** Proceed to **Server Roles**, check **Active Directory Domain Services**, click through to **Confirmation**, and click **Install**. Once succeeded, click **Close**.

### Phase 5: Promote Server to Additional Domain Controller (`SMEC-DC2`)
11. **Open Promotion Wizard:** Click the yellow **🚩 Notifications** flag in the top bar of Server Manager and click **Promote this server to a domain controller**.
12. **Configure & Promote ADC:**
    * **Deployment Configuration:** Select **Add a domain controller to an existing domain** (`smec.com`) and verify credentials (`SMEC\Administrator`).
    * **Domain Controller Options:** Keep **DNS** and **Global Catalog (GC)** checked, enter and confirm the DSRM password (`password123`).
    * **DNS / Additional Options / Paths / Review:** Keep replication source set to `SMEC-DC1.smec.com` and click **Next**.
    * **Prerequisites Check:** Verify all prerequisite checks pass and click **Install**. Wait for AD replication and automatic restart to complete.

### Phase 6: Verify AD Replication (`SMEC-DC2`)
13. **Verify Replicated Objects on ADC:** After restart, open **AD Users & Computers** (`Start -> Run -> dsa.msc` or Desktop icon) on **SMEC-DC2**.
    * Click the **Tech** OU to confirm user **Arun** (`arun@smec.com`) automatically replicated from `SMEC-DC1`.
    * Click **Domain Controllers** OU to confirm both **SMEC-DC1** and **SMEC-DC2** are listed as active Global Catalog Domain Controllers, achieving **100% Lab Completion**.

---

## Key Technical Concepts & Best Practices

### Why Must Preferred DNS Point to `192.168.1.1` Before Joining?
Active Directory relies heavily on **DNS SRV records** (such as `_ldap._tcp.dc._msdcs.smec.com`) to locate Domain Controllers. If `SMEC-DC2` uses a public DNS (like `8.8.8.8`) or leaves DNS blank, it will fail to locate `smec.com` when attempting to join the domain or promote to an ADC.

### Multi-Master Replication
Active Directory uses a **multi-master replication model**. Once `SMEC-DC2` is promoted as an Additional Domain Controller, changes made to users, groups, OUs, or GPOs on either `SMEC-DC1` or `SMEC-DC2` are automatically replicated to the other Domain Controller.

### Key ADC Options Explained
| Option | Purpose |
| :--- | :--- |
| **DNS Server** | Hosts an AD-integrated copy of the `smec.com` DNS zone so clients can still resolve names if `SMEC-DC1` goes offline. |
| **Global Catalog (GC)** | Stores a full writable copy of its own domain and a partial read-only replica of other domains in the forest, speeding up logins and directory searches. |
| **DSRM Password** | Local administrator password used exclusively when booting the Domain Controller into **Directory Services Restore Mode** for offline AD database recovery. |

---

## How to Run & Deploy

### Launch Live Online
Open the hosted simulator directly in your browser:
> **https://tibinjohn193-blip.github.io/az-802/lab11.html**

### Run Locally (Offline)
1. Download `lab11.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

---

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server UI styling, sidebar attention indicators, and restart/login animations)
* **Vanilla JavaScript** (13-step validation engine, IPv4 & credential validators, multi-step Server Manager & AD DS promotion wizards, and simulated AD replication state management)