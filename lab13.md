# Child Domain Controller Setup Lab (`sales.smec.com`) — Windows Server 2025 Simulator

<div align="center">

### 🌿 Active Directory Forest Hierarchy — Interactive Child Domain Lab (`lab13.html`)

[![Click to Launch Interactive Lab](https://img.shields.io/badge/▶_CLICK_TO_START-INTERACTIVE_LAB_(lab13.html)-10b981?style=for-the-badge&logo=windows&logoColor=white)](https://tibinjohn193-blip.github.io/az-802/lab13.html)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab13.html>

</div>

An interactive, single-file web-based **Windows Server 2025 Dual-Server Simulator** designed for hands-on Active Directory Domain Services (AD DS) training. This lab walks students step-by-step through verifying the forest root parent domain (`smec.com`), configuring static IPv4 and Parent DNS on a standalone server (`SALES-DC1`), installing AD DS, promoting the server to create the **`sales.smec.com`** child domain, signing in as the new Child Domain Administrator, and verifying the automatic **Two-Way Transitive Trust** in Active Directory Domains and Trusts (`domain.msc`).

## Table of Contents

1. [What is a Child Domain?](#what-is-a-child-domain)
2. [Child Domain vs. Additional DC (ADC) vs. Organizational Unit (OU)](#child-domain-vs-additional-dc-adc-vs-organizational-unit-ou)
3. [Lab Environment Specifications](#lab-environment-specifications)
4. [Lab Credentials](#lab-credentials)
5. [Simulator Interface & Features](#simulator-interface--features)
6. [Complete 12-Step Lab Walkthrough](#complete-12-step-lab-walkthrough)
7. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
8. [How to Run & Deploy](#how-to-run--deploy)
9. [Technology Stack](#technology-stack)

## What is a Child Domain?

A **Child Domain** is a separate Active Directory domain created directly underneath an existing **Parent Domain** within the same Active Directory **Forest** and **Domain Tree**. It shares a contiguous DNS namespace with its parent — for example, creating a child named `sales` under the parent domain `smec.com` automatically forms the Fully Qualified Domain Name (FQDN) **`sales.smec.com`**.

### Key Characteristics of a Child Domain

* **Contiguous DNS Namespace:** You only enter the child prefix (`sales`) during setup — you do **not** add `.com` or `.in`. Windows Server automatically appends the parent domain (`smec.com`) to create `sales.smec.com`.
* **Dedicated Domain Controller & AD Database:** Unlike an Organizational Unit (OU), a child domain has its own independent Active Directory domain partition, its own Domain Controllers (`SALES-DC1`), and its own Domain Admins group (`SALES\Domain Admins`).
* **Shared Forest Schema & Configuration:** Both `smec.com` and `sales.smec.com` belong to the same Forest, meaning they share the same Active Directory **Schema**, **Configuration partition**, and **Global Catalog**.
* **Automatic Two-Way Transitive Trust:** When a child domain is created, Active Directory automatically establishes a **two-way transitive Kerberos trust** between `smec.com` $\leftrightarrow$ `sales.smec.com`, allowing users in either domain to access authorized resources across the forest without manual trust setup.
* **Replication Boundary & Autonomy:** Separating large divisions, regional offices, or acquisitions into a child domain keeps domain-level AD replication traffic isolated and allows independent password policies and administrative boundaries.

## Child Domain vs. Additional DC (ADC) vs. Organizational Unit (OU)

| Feature | Child Domain (`sales.smec.com`) | Additional DC (`SMEC-DC2`) | Organizational Unit (`Sales` OU) |
| :--- | :--- | :--- | :--- |
| **Domain Namespace** | New subdomain (`sales.smec.com`) | Same domain (`smec.com`) | Logical container inside `smec.com` |
| **AD Database Partition** | Separate domain partition | Replicated copy of `smec.com` | Stored inside `smec.com` partition |
| **Dedicated Domain Controller** | **Yes** (Required) | **Yes** (Redundancy for same domain) | No |
| **Must Join Parent Domain First?** | **No** (Promoted directly from Workgroup) | **Yes** (or via existing domain credentials) | N/A |
| **Trust Relationship** | Automatic Two-Way Transitive Trust | Same domain (no trust needed) | Same domain |
| **Best Use Case** | Separate administrative/replication boundary or regional division | High availability & load balancing | Organizing users, computers, and GPOs |

## Lab Environment Specifications

| Component | Server 1 (Parent / Forest Root DC) | Server 2 (Target Child DC) |
| :--- | :--- | :--- |
| **Hostname** | `SMEC-DC1` | `SALES-DC1` |
| **Operating System** | Windows Server 2025 | Windows Server 2025 |
| **IP Address** | `192.168.1.1` | `192.168.1.2` *(Configured in Lab)* |
| **Subnet Mask** | `255.255.255.0` | `255.255.255.0` *(Configured in Lab)* |
| **Preferred DNS Server** | `192.168.1.1` | `192.168.1.1` *(Points to Parent DC)* |
| **Domain Role** | Forest Root / Parent DC (`smec.com`) | Workgroup $\rightarrow$ Child DC (`sales.smec.com`) |
| **NetBIOS Domain Name** | `SMEC` | `SALES` |

## Lab Credentials

| Account / Purpose | Username | Password |
| :--- | :--- | :--- |
| **Enterprise Administrator** (Required to create Child Domain) | `smec\administrator` | `password123` |
| **DSRM Password** (Directory Services Restore Mode) | *(N/A)* | `password123` |
| **Child Domain Administrator** (Post-Promotion Sign-In) | `sales\administrator` | `password123` |

## Simulator Interface & Features

The simulator (`lab13.html`) uses a full-screen **Dual-Server Switcher** interface:

1. **Left Sidebar (Server Switcher & Concept Reference):**
   * Switch seamlessly between **🏢 Server 1 (Parent DC — `SMEC-DC1`)** and **🌿 Server 2 (Child DC — `SALES-DC1`)**.
   * **Attention Pulse Highlight:** Automatically pulses a yellow border around the Server 1 or Server 2 button when the current step requires switching servers.
   * Displays live status updates for `SALES-DC1` (`DHCP` $\rightarrow$ `192.168.1.2` $\rightarrow$ `Child DC: sales.smec.com ✓`), a **"What is a Child Domain?"** quick-reference card, credentials, and a **Reset Lab** button.

2. **Main Workspace Stage:**
   * **Authentic Manual IP & Parent DNS Setup:** No auto-filled IP inputs — students manually enter `192.168.1.2`, `255.255.255.0`, and `192.168.1.1` in `ncpa.cpl`.
   * **Realistic AD DS Child Domain Wizard:** Includes **Add a new domain to an existing forest** $\rightarrow$ **Child Domain**, live FQDN preview (`sales.smec.com`), Enterprise Admin credentials prompt (`Change...`), DNS Delegation, NetBIOS (`SALES`), and Prerequisites Check.
   * **Post-Restart Child Domain Verification:** Includes the `SALES\Administrator` logon screen, **Active Directory Domains and Trusts (`domain.msc`)**, and **ADUC (`dsa.msc`)**.

3. **Dedicated Bottom Lab Guide Bar:**
   * Sits cleanly below the desktop stage so it never overlaps Start menus, login screens, or wizard controls.
   * Tracks progress from **Step 1 / 12** to **100% Complete**.

## Complete 12-Step Lab Walkthrough

### Phase 1: Verify Parent Domain (`SMEC-DC1`)

1. **Open AD Domains & Trusts:** On **SMEC-DC1**, click the **AD Domains & Trusts** desktop icon (or **Start** $\rightarrow$ **Run** $\rightarrow$ `domain.msc`).
2. **Verify Forest Root:** Click **`smec.com`** in the left tree to confirm the Parent Domain is online and ready.

### Phase 2: Configure Static IP & Parent DNS (`SALES-DC1`)

3. **Open Network Connections:** Switch to **🌿 Server 2 (Child DC)** in the left sidebar. Click **Start** $\rightarrow$ **Run**, type `ncpa.cpl`, and click **OK**.
4. **Open IPv4 Properties:** Right-click **Ethernet** $\rightarrow$ **Properties** $\rightarrow$ double-click **Internet Protocol Version 4 (TCP/IPv4)**.
5. **Enter Static IP & Parent DNS:** Manually configure:
   * **IP address:** `192.168.1.2`
   * **Subnet mask:** `255.255.255.0`
   * **Preferred DNS server:** `192.168.1.1` *(Parent DC IP so `SALES-DC1` can resolve `smec.com`)*
   Click **OK** to save.

### Phase 3: Install AD DS Role (`SALES-DC1`)

6. **Launch Server Manager:** Open **Server Manager** and click **+ Add roles and features**.
7. **Install AD DS:** Navigate to **Server Roles**, check **Active Directory Domain Services**, click through to **Confirmation**, and click **Install**. Once complete, click **Close**.

### Phase 4: Promote Server to Child Domain Controller (`SALES-DC1`)

8. **Open Promotion Wizard:** Click the yellow **🚩 Notifications** flag in Server Manager and select **Promote this server to a domain controller**.
9. **Configure Child Domain & Enterprise Admin:**
   * Select **Add a new domain to an existing forest**.
   * Set **Select domain type** to **Child Domain**.
   * Enter **Parent domain name:** `smec.com`.
   * Enter **New domain name:** `sales` *(do not add `.com` — verify the preview shows `sales.smec.com`)*.
   * Click **Change...** to supply **Enterprise Admin** credentials (`smec\administrator` / `password123`), then click **Next**.
10. **Complete Wizard & Install:**
    * Keep **DNS Server** and **Global Catalog (GC)** checked and set the DSRM password (`password123`).
    * Keep **Create DNS delegation** checked on **DNS Options**.
    * Verify the NetBIOS name (`SALES`), default paths, and Review Options.
    * On **Prerequisites Check**, verify all checks pass and click **Install**. Wait for creation and automatic restart.

### Phase 5: Sign In & Verify Child Domain Trust (`SALES-DC1`)

11. **Sign In to Child Domain:** After restart, sign in to the new Child Domain as `sales\administrator` with password `password123`.
12. **Verify Child Domain & Trust:** Open **AD Domains & Trusts** (`domain.msc`) on **SALES-DC1** and click **`sales.smec.com`** nested under **`smec.com`** to verify the Child Domain hierarchy and automatic **Two-Way Transitive Trust**, achieving **100% Lab Completion**.

## Key Technical Concepts & Best Practices

### Do You Need to Join the Parent Domain (`smec.com`) Before Creating a Child Domain?

**No.** A standalone **Workgroup** server can be promoted directly into a Child Domain Controller without joining the parent domain first. As long as its **Preferred DNS server** points to the Parent DC (`192.168.1.1`) and you supply **Enterprise Admin** credentials (`smec\administrator`) during the AD DS Configuration Wizard, Windows Server contacts the **Domain Naming Master** FSMO role on `SMEC-DC1`, registers `sales.smec.com`, creates the DNS delegation, and establishes the two-way transitive trust automatically.

### Why Are Enterprise Admin Credentials Required?

Creating a new domain modifies the **Forest-wide Configuration Partition** and requires contacting the **Domain Naming Master** FSMO role. Standard Domain Admins do not have forest-level permissions — only members of the **Enterprise Admins** group in the forest root domain (`smec.com`) can add a Child Domain or Tree Domain.

## How to Run & Deploy

### Launch Live Online

Open the hosted simulator directly in your browser:

> **https://tibinjohn193-blip.github.io/az-802/lab13.html**

### Run Locally (Offline)

1. Download `lab13.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server 2025 UI styling, sidebar attention indicators)
* **Vanilla JavaScript** (12-step validation engine, IPv4 & Enterprise Admin credential validators, live FQDN preview, AD DS Child Domain promotion wizard, and Domains & Trusts verification)