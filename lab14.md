# Tree Domain Controller Setup Lab (`tech.com`) — Windows Server 2025 Simulator

<div align="center">

### 🌳 Active Directory Multi-Tree Forest — Interactive Tree Domain Lab (`lab14.html`)

[![Click to Start Interactive Lab](https://img.shields.io/badge/▶_CLICK_TO_START-INTERACTIVE_LAB_(lab14.html)-7e22ce?style=for-the-badge&logo=windows&logoColor=white)](https://tibinjohn193-blip.github.io/az-802/lab14.html)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab14.html>

</div>

An interactive, single-file web-based **Windows Server 2025 Dual-Server Simulator** designed for hands-on Active Directory Domain Services (AD DS) training. This lab guides students step-by-step through verifying an existing Forest Root Domain (`smec.com`), configuring static IPv4 and Forest Root DNS on a standalone server (`TECH-DC1`), installing AD DS, promoting the server to create a new **Tree Domain (`tech.com`)** inside the `smec.com` forest, signing in as `tech\administrator`, and verifying the automatic **Two-Way Transitive Tree-Root Trust** in Active Directory Domains and Trusts (`domain.msc`).

## Table of Contents

1. [What is a Tree Domain?](#what-is-a-tree-domain)
2. [Tree Domain vs. Child Domain vs. New Forest](#tree-domain-vs-child-domain-vs-new-forest)
3. [Lab Environment Specifications](#lab-environment-specifications)
4. [Lab Credentials](#lab-credentials)
5. [Simulator Interface & Features](#simulator-interface--features)
6. [Complete 12-Step Lab Walkthrough](#complete-12-step-lab-walkthrough)
7. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
8. [How to Run & Deploy](#how-to-run--deploy)
9. [Technology Stack](#technology-stack)

## What is a Tree Domain?

A **Tree Domain** is a new Active Directory domain created inside an **existing Active Directory Forest**, but with a **completely separate (disjoint) DNS namespace**. For example, adding `tech.com` as a new Tree Domain inside the `smec.com` forest creates a second domain tree alongside `smec.com`.

### Why Do Organizations Create a Tree Domain?

Tree Domains are commonly used during **company mergers, acquisitions, or when managing distinct brand identities** that need separate DNS names (such as `smec.com` and `tech.com`) while still sharing centralized IT infrastructure and seamless cross-company resource access.

### Key Characteristics of a Tree Domain

* **Disjoint DNS Namespace:** Unlike a Child Domain (which appends to its parent, such as `sales.smec.com`), a Tree Domain uses an independent root DNS name (`tech.com`). During promotion, you must enter the **full domain name** (`tech.com`).
* **Shared Forest Schema & Configuration:** Even though the DNS names are different, both `smec.com` and `tech.com` belong to the same Forest, sharing a single Active Directory **Schema**, **Configuration partition**, and **Global Catalog**.
* **Automatic Two-Way Transitive Tree-Root Trust:** Active Directory automatically establishes a **two-way transitive Kerberos trust** between the forest root (`smec.com`) and the new tree root (`tech.com`), enabling users in either domain to access authorized resources across the forest seamlessly.
* **Independent Domain Partition & Administration:** `tech.com` maintains its own Domain Controllers (`TECH-DC1`), its own AD domain database, and its own `TECH\Domain Admins` group.

## Tree Domain vs. Child Domain vs. New Forest

| Feature | Tree Domain (`tech.com`) | Child Domain (`sales.smec.com`) | New Forest (`other.com`) |
| ----- | ----- | ----- | ----- |
| **DNS Namespace** | **Disjoint** (`tech.com`) | Contiguous (`sales.smec.com`) | Completely separate |
| **Name Entered in Wizard** | Full FQDN (`tech.com`) | Prefix only (`sales`) | Full FQDN (`other.com`) |
| **Shared Forest & Schema** | **Yes** (`smec.com` forest) | **Yes** (`smec.com` forest) | No (Isolated forest) |
| **Trust Relationship** | Automatic **Tree-Root Trust** (Two-Way Transitive) | Automatic **Parent-Child Trust** (Two-Way Transitive) | None by default (Manual Forest Trust required) |
| **Must Join Forest Root First?** | **No** (Promoted directly from Workgroup) | **No** (Promoted directly from Workgroup) | No |
| **Best Use Case** | Mergers, acquisitions, or separate brand names in one forest | Regional offices or divisions sharing the same brand name | Strict security isolation or completely separate organizations |

## Lab Environment Specifications

| Component | Server 1 (Forest Root DC) | Server 2 (Target Tree DC) |
| ----- | ----- | ----- |
| **Hostname** | `SMEC-DC1` | `TECH-DC1` |
| **Operating System** | Windows Server 2025 | Windows Server 2025 |
| **IP Address** | `192.168.1.1` | `192.168.1.2` *(Configured in Lab)* |
| **Subnet Mask** | `255.255.255.0` | `255.255.255.0` *(Configured in Lab)* |
| **Preferred DNS Server** | `192.168.1.1` | `192.168.1.1` *(Points to Forest Root DC)* |
| **Domain Role** | Forest Root DC (`smec.com`) | Workgroup $\rightarrow$ Tree Root DC (`tech.com`) |
| **NetBIOS Domain Name** | `SMEC` | `TECH` |

## Lab Credentials

| Account / Purpose | Username | Password |
| ----- | ----- | ----- |
| **Enterprise Administrator** (Required to add Tree Domain) | `smec\administrator` | `password123` |
| **DSRM Password** (Directory Services Restore Mode) | *(N/A)* | `password123` |
| **Tree Domain Administrator** (Post-Promotion Sign-In) | `tech\administrator` | `password123` |

## Simulator Interface & Features

The simulator (`lab14.html`) uses a full-screen **Dual-Server Switcher** layout:

1. **Left Sidebar (Server Switcher & Concept Reference):**
   * Switch seamlessly between **🏢 Server 1 (Forest Root — `SMEC-DC1`)** and **🌳 Server 2 (Tree DC — `TECH-DC1`)**.
   * **Attention Pulse Highlight:** Automatically pulses a yellow border around the Server 1 or Server 2 button when the current step requires switching servers.
   * Displays live status updates for `TECH-DC1` (`DHCP` $\rightarrow$ `192.168.1.2` $\rightarrow$ `Tree DC: tech.com ✓`), a **"What is a Tree Domain?"** summary card, credentials, and a **Reset Lab** button.
2. **Main Workspace Stage:**
   * **Authentic Manual IP & DNS Setup:** Students manually enter `192.168.1.2`, `255.255.255.0`, and `192.168.1.1` in `ncpa.cpl`.
   * **Realistic AD DS Tree Domain Wizard:** Includes **Add a new domain to an existing forest** $\rightarrow$ **Tree Domain**, Forest name (`smec.com`), full New domain name (`tech.com`), Enterprise Admin credentials prompt (`Change...`), DNS zone warning, NetBIOS (`TECH`), and Prerequisites Check.
   * **Post-Restart Verification:** Includes the `TECH\Administrator` sign-in screen, **Active Directory Domains and Trusts (`domain.msc`)** displaying both `smec.com` and `tech.com` as parallel trees, and **ADUC (`dsa.msc`)**.
3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed cleanly below the desktop stage so it never obscures Start menus, login screens, or wizard controls.
   * Tracks progress from **Step 1 / 12** to **100% Complete**.

## Complete 12-Step Lab Walkthrough

### Phase 1: Verify Existing Forest Root (`SMEC-DC1`)

1. **Open AD Domains & Trusts:** On **SMEC-DC1**, click the **AD Domains & Trusts** desktop icon (or **Start** $\rightarrow$ **Run** $\rightarrow$ `domain.msc`).
2. **Verify Forest Root:** Click **`smec.com`** in the left tree to confirm the Forest Root Domain is online and ready.

### Phase 2: Configure Static IP & Forest DNS (`TECH-DC1`)

3. **Open Network Connections:** Switch to **🌳 Server 2 (Tree DC)** in the left sidebar. Click **Start** $\rightarrow$ **Run**, type `ncpa.cpl`, and click **OK**.
4. **Open IPv4 Properties:** Right-click **Ethernet** $\rightarrow$ **Properties** $\rightarrow$ double-click **Internet Protocol Version 4 (TCP/IPv4)**.
5. **Enter Static IP & Forest DNS:** Manually configure:
   * **IP address:** `192.168.1.2`
   * **Subnet mask:** `255.255.255.0`
   * **Preferred DNS server:** `192.168.1.1` *(Forest Root DC IP so `TECH-DC1` can locate `smec.com`)*
   Click **OK** to save.

### Phase 3: Install AD DS Role (`TECH-DC1`)

6. **Launch Server Manager:** Open **Server Manager** and click **+ Add roles and features**.
7. **Install AD DS:** Navigate to **Server Roles**, check **Active Directory Domain Services**, click through to **Confirmation**, and click **Install**. Once succeeded, click **Close**.

### Phase 4: Promote Server to Tree Domain Controller (`TECH-DC1`)

8. **Open Promotion Wizard:** Click the yellow **🚩 Notifications** flag in Server Manager and click **Promote this server to a domain controller**.
9. **Configure Tree Domain & Enterprise Admin:**
   * Select **Add a new domain to an existing forest**.
   * Change **Select domain type** to **Tree Domain**.
   * Enter **Forest name:** `smec.com`.
   * Enter **New domain name:** `tech.com` *(full FQDN)*.
   * Click **Change...** to supply **Enterprise Admin** credentials (`smec\administrator` / `password123`), then click **Next**.
10. **Complete Wizard & Install:**
    * Keep **DNS Server** and **Global Catalog (GC)** checked and set the DSRM password (`password123`).
    * On **DNS Options**, click **Next** (since `tech.com` hosts its own separate DNS zone on `TECH-DC1`).
    * Verify the NetBIOS name (`TECH`), default paths, and Review Options.
    * On **Prerequisites Check**, confirm all checks pass and click **Install**. Wait for creation and automatic restart.

### Phase 5: Sign In & Verify Tree Domain Trust (`TECH-DC1`)

11. **Sign In to Tree Domain:** After restart, sign in to the new Tree Domain as `tech\administrator` with password `password123`.
12. **Verify Tree Domain & Trust:** Open **AD Domains & Trusts** (`domain.msc`) on **TECH-DC1** and click **`tech.com`** alongside **`smec.com`** to verify the disjoint Tree Domain and automatic **Two-Way Transitive Tree-Root Trust**, achieving **100% Lab Completion**.

## Key Technical Concepts & Best Practices

### Why Does `tech.com` Not Appear Nested Under `smec.com`?

In **Active Directory Domains and Trusts**, a Child Domain (`sales.smec.com`) is displayed indented *underneath* its parent (`smec.com`). A Tree Domain (`tech.com`), however, is the root of its **own DNS tree**, so it appears at the **same top level** alongside `smec.com` inside the same Forest.

### DNS Delegation Note for Tree Domains

Because `tech.com` is not a subdomain of `smec.com`, the Forest Root DNS server (`192.168.1.1`) cannot automatically create a parent-child DNS delegation for `.com`. Instead, `TECH-DC1` hosts its own authoritative DNS zone for `tech.com`, and AD-integrated forest-wide DNS partitions (`ForestDnsZones`) replicate across both domains so servers and clients can resolve names in both `smec.com` and `tech.com`.

## How to Run & Deploy

### Launch Live Online

Open the hosted simulator directly in your browser:

> **https://tibinjohn193-blip.github.io/az-802/lab14.html**

### Run Locally (Offline)

1. Download `lab14.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server 2025 UI styling, sidebar attention indicators)
* **Vanilla JavaScript** (12-step validation engine, IPv4 & Enterprise Admin credential validators, AD DS Tree Domain promotion wizard, and Domains & Trusts verification)