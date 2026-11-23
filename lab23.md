# Windows Server Backup — Backup Schedule Lab (`C:` $\rightarrow$ `D:`) — Windows Server 2025 Simulator

### 💾 Automated Scheduled Backups with Windows Server Backup — Interactive Lab (`lab23.html`)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab23.html>

An interactive, single-file web-based **Windows Server 2025 Simulator** designed for hands-on **Windows Server Backup (WSB)** administration training. This lab guides learners step-by-step through installing the **Windows Server Backup** feature in Server Manager, creating a source file on `Local Disk (C:)`, configuring an automated **Backup Schedule** (`Custom` $\rightarrow$ `Once a day` at `10:30 AM` $\rightarrow$ `Back up to a volume` on `Backup Disk (D:)`), watching the live simulated server clock advance from `10:29 AM` to `10:30 AM` to automatically trigger the backup job, and verifying the resulting `WindowsImageBackup` folder on `D:\`.

## Table of Contents

1. [What is Windows Server Backup (WSB)?](#what-is-windows-server-backup-wsb)

2. [Backup Configuration & Destination Options](#backup-configuration--destination-options)

3. [Lab Environment Specifications](#lab-environment-specifications)

4. [Simulator Interface & Features](#simulator-interface--features)

5. [Complete 9-Step Lab Walkthrough](#complete-9-step-lab-walkthrough)

6. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)

7. [How to Run & Deploy](#how-to-run--deploy)

8. [Technology Stack](#technology-stack)

## What is Windows Server Backup (WSB)?

**Windows Server Backup (WSB)** is a built-in Windows Server **Feature** (installed via Server Manager or PowerShell) that provides native backup and disaster recovery capabilities for Windows Server environments. Administrators can manage backups graphically through the **Windows Server Backup MMC console (`wbadmin.msc`)** or via command line (`wbadmin`) and PowerShell.

### Backup Schedule vs. Backup Once

| Option | Behavior | Best Use Case |
| ----- | ----- | ----- |
| **Backup Schedule...** *(Used in this Lab)* | Automatically runs recurring backups once or multiple times per day at specified times without manual intervention. | Ongoing daily server protection for critical files, folders, System State, or Bare Metal Recovery. |
| **Backup Once...** | Performs a single, one-time manual backup (either using scheduled settings or different custom options). | Ad-hoc snapshots right before major OS updates, schema changes, or application upgrades. |

## Backup Configuration & Destination Options

### 1. Backup Configuration Types

* **Full server (recommended):** Backs up all server data, applications, volumes, and System State for full Bare Metal Recovery.
* **Custom** *(Used in this Lab)*: Allows administrators to selectively choose specific volumes, folders, System State, or individual files (such as `C:\company_data.txt`).

### 2. Backup Destination Types

| Destination Type | Behavior & Trade-offs |
| ----- | ----- |
| **Dedicated hard disk (recommended)** | Dedicates an entire physical disk exclusively to backups. Windows reformats the disk and hides it from File Explorer for better performance and security. |
| **Back up to a volume** *(Used in this Lab)* | Stores backups inside a `WindowsImageBackup` folder on an existing NTFS volume (such as `Backup Disk (D:)`). Preserves existing data on `D:\` while allowing you to browse the backup folder directly. |
| **Shared network folder** | Stores backups on a remote SMB network share (`\\Server\Share`). Note: Only keeps the **most recent** backup copy on a network share (overwrites previous versions). |

## Lab Environment Specifications

| Component | Details |
| ----- | ----- |
| **Server Hostname** | `SMEC-DC1` (`192.168.1.1`) — Windows Server 2025 |
| **Active Directory Domain** | `smec.com` |
| **Required Feature** | **Windows Server Backup** *(Installed via Server Manager $\rightarrow$ Features)* |
| **Source Drive & File** | `Local Disk (C:)` — e.g., `C:\company_data.txt` *(Created in Lab)* |
| **Destination Drive** | `Backup Disk (D:)` (100 GB NTFS Volume) |
| **Backup Configuration** | `Custom` (Selected file on `C:\`) |
| **Scheduled Time** | `Once a day` at `10:30 AM` |
| **Simulated Server Clock** | Starts at `10:29:45 AM`, counts down live to `10:30:00 AM` to auto-trigger backup |
| **Management Console** | `wbadmin.msc` (Windows Server Backup) |

## Simulator Interface & Features

The simulator (`lab23.html`) provides a self-contained Windows Server 2025 desktop environment:

1. **Left Sidebar (Server Card, Live Clock & Progress Checklist):**
   * **🕒 Live Server System Clock:** Displays the simulated server time (`10:29:45 AM` $\rightarrow$ `10:30:00 AM`), synchronized with the taskbar clock, and includes a **⏩ Fast-Forward to 10:30 AM** button once the schedule is active.
   * **Live Lab Progress Card:** Tracks all 5 milestones (`Install WSB Feature`, `Create File on C:\`, `Configure Schedule`, `Auto-Run at 10:30 AM`, and `Verify Backup on D:\`) in real time.
   * Includes quick-reference lab parameters and a **Reset Lab** button.

2. **Main Workspace Stage:**
   * **Server Manager (`Add Roles and Features Wizard`):** 7-step wizard where **Windows Server Backup** is installed specifically under **Features** (not Server Roles).
   * **This PC / File Explorer (`C:\` & `D:\`):** Right-click context menu on `C:\` to create a text document, plus `D:\WindowsImageBackup` verification after the scheduled backup finishes.
   * **Windows Server Backup (`wbadmin.msc`):** Authentic 8-step **Backup Schedule Wizard** including **Select Items**, **Specify Backup Time**, **Specify Destination Type**, **Select Destination Volume**, the authentic Windows volume performance warning dialog, and live backup progress bars.

3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed cleanly below the desktop stage so it never overlaps Start menus, wizards, or File Explorer windows.
   * Tracks progress from **Step 1 / 9** to **100% Complete**.

## Complete 9-Step Lab Walkthrough

### Phase 1: Install Feature & Create Source File

1. **Install Windows Server Backup Feature:** Open **Server Manager** $\rightarrow$ click **+ Add roles and features** $\rightarrow$ advance to **Features** $\rightarrow$ check **💾 Windows Server Backup** $\rightarrow$ click **Next** $\rightarrow$ **Install** $\rightarrow$ **Close**.

2. **Create Source File on `C:\`:** Open **This PC (`C:` & `D:`)** $\rightarrow$ open **Local Disk (`C:`)** $\rightarrow$ **Right-click** empty white space $\rightarrow$ **New** $\rightarrow$ **Text Document** (e.g., `company_data.txt`).

### Phase 2: Configure Backup Schedule (`wbadmin.msc`)

3. **Open Windows Server Backup:** Click **Windows Server Backup** on the Desktop (or **Server Manager** $\rightarrow$ **Tools** $\rightarrow$ **Windows Server Backup**, or `Start` $\rightarrow$ `Run` $\rightarrow$ `wbadmin.msc`).

4. **Launch Backup Schedule Wizard:** In the right **Actions (Local Backup)** pane, click **🕒 Backup Schedule...**.

5. **Select Custom Configuration & Source File:**
   * Click **Next** on *Getting Started*.
   * Select **Custom** $\rightarrow$ click **Next**.
   * Click **Add Items...** $\rightarrow$ check your file (`C:\company_data.txt`) under `Local Disk (C:)` $\rightarrow$ click **OK** $\rightarrow$ click **Next**.

6. **Specify Backup Time (`10:30 AM`):** Keep **Once a day** selected, ensure the time is set to **`10:30 AM`** (matching the `10:29 AM` simulated clock), and click **Next**.

7. **Select Destination Volume (`D:\`) & Finish:**
   * Select **Back up to a volume** $\rightarrow$ click **Next**.
   * Click **Add...** $\rightarrow$ check **Backup Disk (`D:`)** $\rightarrow$ click **OK** $\rightarrow$ click **Next**.
   * Read the Windows Server Backup volume performance notice and click **OK**.
   * Review the **Confirmation** summary $\rightarrow$ click **Finish** $\rightarrow$ click **Close**.

### Phase 3: Watch Auto-Execution & Verify on `D:\`

8. **Wait for `10:30 AM` Auto-Backup:** Watch the **Server System Clock** count from `10:29:50 AM` to `10:30:00 AM` (or click **⏩ Fast-Forward to 10:30 AM** in the left sidebar). Observe the scheduled backup automatically start and complete inside **Windows Server Backup**.

9. **Verify Backup on `D:\`:** Open **This PC** $\rightarrow$ open **Backup Disk (`D:`)** $\rightarrow$ click **`WindowsImageBackup`** to confirm your `C:\` file was backed up to `D:\`, achieving **100% Lab Completion**.

## Key Technical Concepts & Best Practices

* **Why Does Windows Warn About Backing Up to a Volume?** Storing backups on a shared local volume (`D:\`) that also holds live application data can reduce disk I/O performance by up to 200% during shadow copy operations. For high-throughput production servers, Microsoft recommends using a **dedicated backup disk** or an off-site/cloud target (such as Azure Backup).
* **Follow the 3-2-1 Backup Rule:** Keep **3** copies of your data on **2** different media types, with at least **1** copy stored **off-site** (e.g., cloud or remote datacenter) to protect against fire, theft, or ransomware.
* **Command-Line Equivalent (`wbadmin`):** Administrators can also create or inspect backup schedules from an elevated Command Prompt using `wbadmin enable backup` and `wbadmin get versions`.

## How to Run & Deploy

### Launch Live Online

Open the hosted simulator directly in your browser:

> **https://tibinjohn193-blip.github.io/az-802/lab23.html**

### Run Locally (Offline)

1. Download `lab23.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server 2025 MMC & Wizard styling, live clock card, progress badges)
* **Vanilla JavaScript** (9-step validation engine, Server Manager Feature installer, 8-step Backup Schedule Wizard, live simulated clock countdown with fast-forward, and `D:\WindowsImageBackup` verification)