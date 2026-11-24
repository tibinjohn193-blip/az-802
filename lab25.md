# Windows Server Update Services (WSUS) Full Server & Client Lab — Windows Server 2025 Simulator

### 🔄 Enterprise Patch Management with WSUS, GPO & Client Verification — Interactive Lab (`lab25.html`)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab25.html>

An interactive, single-file web-based **Windows Server 2025 & Windows 11 Client Simulator** designed for hands-on **Windows Server Update Services (WSUS)** administration training. Without skipping a single real-world step, this lab guides learners through creating the `C:\WSUS` content directory, installing the **WSUS** role (WID Connectivity & WSUS Services), running **Post-Installation Tasks** via the Server Manager notification flag, completing the full **10-step WSUS Configuration Wizard**, creating the `Clients` computer group, configuring all **3 required Group Policy (GPO)** settings in `gpmc.msc`, forcing client check-in (`gpupdate /force` & `wuauclt /detectnow`), approving `KB5046617`, and downloading/installing the patch live on the Windows 11 client.

## Table of Contents

1. [What is WSUS (Windows Server Update Services)?](#what-is-wsus-windows-server-update-services)
2. [What is the Use of WSUS? (Key Benefits)](#what-is-the-use-of-wsus-key-benefits)
3. [WSUS Architecture & Key Concepts](#wsus-architecture--key-concepts)
4. [Lab Environment Specifications](#lab-environment-specifications)
5. [Simulator Interface & Features](#simulator-interface--features)
6. [Complete 12-Step Lab Walkthrough](#complete-12-step-lab-walkthrough)
7. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
8. [How to Run & Deploy](#how-to-run--deploy)
9. [Technology Stack](#technology-stack)

## What is WSUS (Windows Server Update Services)?

**Windows Server Update Services (WSUS)** is a built-in Windows Server role that enables IT administrators to centrally download, test, approve, and deploy Microsoft product updates (Windows OS patches, security updates, critical updates, and drivers) across an internal corporate network.

Instead of allowing hundreds or thousands of domain computers to individually connect to the public internet (Microsoft Update) to download the same gigabytes of patches, a single **WSUS Server** downloads the updates once into local storage (e.g., `C:\WSUS`) and distributes them internally over the LAN (HTTP Port `8530` or HTTPS Port `8531`).

## What is the Use of WSUS? (Key Benefits)

* **Saves Internet Bandwidth:** Updates are downloaded only once from Microsoft Update to the WSUS server; all internal clients pull patches at high speed over the local area network (LAN).
* **Centralized Patch Approval & Testing:** Administrators can organize computers into **Computer Groups** (e.g., `Test`, `Clients`, `Servers`), test new updates on a pilot group first, and approve them for production only after confirming stability.
* **Scheduled Maintenance Windows:** Combined with **Group Policy (GPO)**, WSUS controls *when* updates download and install so workstations and servers do not unexpectedly reboot during business hours.
* **Compliance & Reporting:** Provides a centralized console (`wsus.msc`) to track which computers are up to date, which patches are missing, and which updates failed to install.
* **Security for Restricted Networks:** Allows servers and workstations without direct internet access to remain fully patched using an internal intranet update server (`http://192.168.1.1:8530`).

## WSUS Architecture & Key Concepts

| Concept | Description |
| ----- | ----- |
| **Upstream vs. Downstream Server** | An **Upstream Server** synchronizes directly from Microsoft Update (used in this lab). A **Downstream Server** synchronizes from an internal Upstream WSUS server for multi-branch enterprises. |
| **WID vs. SQL Server** | **Windows Internal Database (WID)** is the built-in free database ideal for small-to-medium environments (up to ~1,000 clients). **SQL Server** is used for large-scale or multi-WSUS deployments. |
| **Default WSUS Ports** | **TCP Port `8530`** for HTTP (used in this lab) and **TCP Port `8531`** for HTTPS/SSL (recommended for production). |
| **Client-Side Targeting** | Uses a GPO setting (**Enable client-side targeting**) to automatically place domain computers into a designated WSUS Computer Group (e.g., `Clients`) when they report in. |

## Lab Environment Specifications

| Component | Server (`SMEC-DC1`) | Client (`CLIENT-PC`) |
| ----- | ----- | ----- |
| **Operating System** | Windows Server 2025 | Windows 11 (Domain-joined) |
| **IP Address** | `192.168.1.1` | `192.168.1.10` |
| **Active Directory Domain** | `smec.com` | `smec.com` |
| **WSUS Content Directory** | `C:\WSUS` | — |
| **WSUS Role Services** | WID Connectivity & WSUS Services | — |
| **Intranet Update URL** | `http://192.168.1.1:8530` | Configured via GPO (`WSUS Policy`) |
| **WSUS Computer Group** | `Clients` | Assigned via Client-Side Targeting |
| **Test Security Patch** | `KB5046617` (Windows 11) | Downloaded & Installed via Windows Update |
| **Management Consoles** | `servermanager`, `wsus.msc`, `gpmc.msc` | `cmd` (`gpupdate`, `wuauclt`) & Windows Update |

## Simulator Interface & Features

The simulator (`lab25.html`) uses a full-screen **Server / Client Switcher** layout:

1. **Left Sidebar (Machine Switcher & 9-Milestone Progress Card):**
   * Switch seamlessly between **🖥️ Server (`SMEC-DC1`)** and **💻 Client PC (`Win 11`)**.
   * **Attention Pulse Highlight:** Automatically pulses a yellow border around whichever machine is needed for the active step.
   * **Live Lab Progress Card:** Tracks all 9 milestones (`C:\WSUS`, `Install WSUS Role`, `Post-Install Tasks`, `WSUS Config Wizard`, `Clients Group`, `WSUS GPO`, `Client Scan`, `Approve Update`, and `Client Install`).

2. **Main Workspace Stage:**
   * **File Explorer & Server Manager:** Create `C:\WSUS`, run the 10-step **Add Roles and Features Wizard**, and click the glowing **🚩 1 Notification Flag** to execute **Launch Post-Installation tasks**.
   * **10-Step WSUS Configuration Wizard:** Authentic step-by-step wizard covering Upstream Server, Proxy, **Start Connecting**, Language (`English`), Products (`Windows 11`), Classifications (`Critical` & `Security Updates`), Sync Schedule, and **Begin initial synchronization**.
   * **WSUS Console (`wsus.msc`) & GPMC (`gpmc.msc`):** Create Computer Groups, link and edit `WSUS Policy` with all 3 required policies, inspect registered clients, and approve `KB5046617`.
   * **Client CMD & Windows Update Settings:** Run `gpupdate /force` and `wuauclt /detectnow` (or `usoclient StartScan`), then open **⚙️ Windows Update** to download and install the approved patch from `http://192.168.1.1:8530`.

3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed cleanly below the desktop stage so it never overlaps Start menus, wizards, or Command Prompt windows.
   * Tracks progress from **Step 1 / 12** to **100% Complete**.

## Complete 12-Step Lab Walkthrough

### Phase 1: Prepare Storage & Install WSUS Role (`SMEC-DC1`)

1. **Create `C:\WSUS` Folder:** Open **File Explorer** $\rightarrow$ open **Local Disk (`C:`)** $\rightarrow$ **Right-click** empty space $\rightarrow$ **New** $\rightarrow$ **Folder** $\rightarrow$ name it `WSUS`.
2. **Install WSUS Role:** Open **Server Manager** $\rightarrow$ click **+ Add roles and features** $\rightarrow$ select **🔄 Windows Server Update Services** under *Server Roles* $\rightarrow$ keep **WID Connectivity** & **WSUS Services** checked $\rightarrow$ enter content path `C:\WSUS` $\rightarrow$ click **Install** $\rightarrow$ **Close**.
3. **Run Post-Installation Tasks:** Click the glowing **🚩 1** notification flag in the Server Manager top bar $\rightarrow$ click **⚙️ Launch Post-Installation tasks** and wait for `SUSDB` (WID) and IIS Port `8530` setup to complete.

### Phase 2: Complete WSUS Configuration Wizard & Create Computer Group (`SMEC-DC1`)

4. **Launch WSUS Configuration Wizard:** Click **🔧 Tools** $\rightarrow$ **Windows Server Update Services** (or the Desktop icon).
5. **Complete All 10 Wizard Pages:**
   * Upstream Server: **Synchronize from Microsoft Update**.
   * Proxy Server: Click **Next**.
   * Connect to Upstream: Click **Start Connecting** $\rightarrow$ **Next**.
   * Languages: **English**.
   * Products: Check **💻 Windows 11**.
   * Classifications: Check **🔴 Critical Updates** & **🛡️ Security Updates**.
   * Sync Schedule: **Synchronize manually**.
   * Finished: Check **Begin initial synchronization** $\rightarrow$ click **Finish**.
6. **Create `Clients` Computer Group:** In the WSUS Console, expand **Computers** $\rightarrow$ right-click **All Computers** $\rightarrow$ **Add Computer Group...** $\rightarrow$ name it `Clients` $\rightarrow$ click **Add**.

### Phase 3: Configure Group Policy (`gpmc.msc`)

7. **Create & Edit `WSUS Policy` GPO:** Open **Group Policy Management (`gpmc.msc`)** $\rightarrow$ right-click **🏢 smec.com** $\rightarrow$ **Create a GPO in this domain, and Link it here...** $\rightarrow$ name it `WSUS Policy` $\rightarrow$ right-click **WSUS Policy** $\rightarrow$ **✏️ Edit...**.
8. **Enable All 3 WSUS Policies** *(under `Computer Configuration -> Policies -> Administrative Templates -> Windows Components -> Windows Update -> Manage updates offered from WSUS`)*:
   * **Configure Automatic Updates:** `Enabled` (Option `4 - Auto download and schedule the install`).
   * **Specify intranet Microsoft update service location:** `Enabled` $\rightarrow$ enter `http://192.168.1.1:8530` in **both** URL boxes.
   * **Enable client-side targeting:** `Enabled` $\rightarrow$ Target group name: `Clients`.

### Phase 4: Client Check-In, Server Approval & Live Patch Install

9. **Apply GPO & Report to WSUS (`CLIENT-PC`):** Switch to **💻 Client PC** $\rightarrow$ open **Command Prompt (`cmd`)** and run:
   ```cmd
   gpupdate /force
   wuauclt /detectnow
   ```
   *(You may also use `usoclient StartScan` on modern Windows 11 systems.)*
10. **Verify Client & Approve `KB5046617` (`SMEC-DC1`):** Switch back to **🖥️ Server** $\rightarrow$ verify `CLIENT-PC.smec.com` appears under **Computers $\rightarrow$ Clients** $\rightarrow$ go to **Updates $\rightarrow$ 🛡️ Security Updates** $\rightarrow$ right-click **`KB5046617`** $\rightarrow$ **✅ Approve...** $\rightarrow$ set **Clients** to **Approved for Install** $\rightarrow$ **OK**.
11. **Check for Updates (`CLIENT-PC`):** Switch to **💻 Client PC** $\rightarrow$ open **⚙️ Windows Update** $\rightarrow$ click **🔄 Check for updates** to detect `KB5046617` from `http://192.168.1.1:8530`.
12. **Install Approved Patch:** Click **⬇️ Download & Install now** to install `KB5046617` from your local WSUS server and achieve **100% Lab Completion**.

## Key Technical Concepts & Best Practices

* **Modern Windows 10/11 Scan Command:** While `wuauclt /detectnow` is widely known in interviews, modern Windows 10/11 and Windows Server 2019/2022/2025 clients use `usoclient StartScan` (Update Session Orchestrator) to trigger an immediate WSUS scan.
* **Use HTTPS (Port `8531`) in Production:** Always bind a TLS/SSL certificate to WSUS in production so update metadata and client reporting are encrypted over Port `8531` instead of cleartext Port `8530`.
* **WSUS Maintenance (Server Cleanup Wizard):** Periodically run the **Server Cleanup Wizard** inside WSUS Options to decline superseded updates and remove expired files so `C:\WSUS` does not fill up disk space.
* **Hybrid Cloud Note (AZ-801):** In modern hybrid environments, Microsoft pairs or replaces on-premises WSUS with **Azure Update Manager** (via Azure Arc) and **Windows Autopatch**.

## How to Run & Deploy

### Launch Live Online

Open the hosted simulator directly in your browser:

> **https://tibinjohn193-blip.github.io/az-802/lab25.html**

### Run Locally (Offline)

1. Download `lab25.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server 2025 & Windows 11 UI styling, notification flag animation, sidebar progress checklist)
* **Vanilla JavaScript** (12-step validation engine, Server Manager Role Installer + Post-Install Tasks, 10-step WSUS Configuration Wizard, GPMC & Policy Editor, simulated Client `cmd`, and Windows Update client)