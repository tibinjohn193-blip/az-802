# Volume Shadow Copy (VSS) Backup & File Recovery Lab — Windows Server 2025 Simulator

### 🕒 Point-in-Time File Recovery with Shadow Copies — Interactive Lab (`lab22.html`)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab22.html>

> ### ⚠️ CRITICAL WARNING — PRODUCTION ENVIRONMENT
>
> **NEVER manually delete real production files or folders on a live server to test backups!**
>
> In this interactive lab simulator, we delete a sample file inside `C:\Data` **solely to simulate accidental user deletion** so you can safely practice recovering files using **Previous Versions**. In production environments, always test restore procedures using dedicated test folders or non-production staging servers.

An interactive, single-file web-based **Windows Server 2025 Simulator** designed for hands-on **Volume Shadow Copy Service (VSS)** backup and recovery training. This lab guides learners step-by-step through creating a data directory (`C:\Data`) with sample files, enabling **Shadow Copies** on `Local Disk (C:)`, configuring automatic backup schedules and on-demand snapshots (**Create Now**), simulating accidental file deletion, and restoring the deleted file via the **Previous Versions** tab.

## Table of Contents

1. [What is Volume Shadow Copy (VSS)?](#what-is-volume-shadow-copy-vss)
2. [Shadow Copies Tab vs. Previous Versions Tab](#shadow-copies-tab-vs-previous-versions-tab)
3. [Lab Environment Specifications](#lab-environment-specifications)
4. [Simulator Interface & Features](#simulator-interface--features)
5. [Complete 10-Step Lab Walkthrough](#complete-10-step-lab-walkthrough)
6. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
7. [How to Run & Deploy](#how-to-run--deploy)
8. [Technology Stack](#technology-stack)

## What is Volume Shadow Copy (VSS)?

**Volume Shadow Copy Service (VSS)**—also known as **Shadow Copies for Shared Folders**—is a built-in Windows Server technology that creates **point-in-time snapshots** (copies) of entire NTFS volumes, even while files are open and in use.

### Why Use Shadow Copies?
* **Instant Accidental Deletion Recovery:** Quickly restore files or folders that users accidentally deleted without needing to pull from slow tape or off-site backups.
* **Version Rollback:** Recover earlier versions of documents that were accidentally overwritten or corrupted.
* **Scheduled & On-Demand Snapshots:** Windows Server automatically takes snapshots on a customizable schedule (by default at **7:00 AM** and **12:00 PM**, Monday through Friday) and allows administrators to trigger immediate snapshots using **Create Now**.

## Shadow Copies Tab vs. Previous Versions Tab

A common point of confusion in Windows Server administration is where to **configure** Shadow Copies versus where to **restore** files:

| Location | Tab Name | Primary Purpose |
| ----- | ----- | ----- |
| **Drive Level:** Right-click `Local Disk (C:)` $\rightarrow$ **Properties** | **Shadow Copies** | **Configure & Manage VSS:** Enable/Disable Shadow Copies, set storage limits, customize the automatic backup **Schedule...**, and take manual snapshots (**Create Now**). |
| **Folder Level:** Right-click `C:\Data` $\rightarrow$ **Properties** *(or **Restore previous versions**)* | **Previous Versions** | **Recover Deleted/Modified Files:** Browse point-in-time snapshots of that specific folder and click **Open** or **Restore...** to bring back lost files without affecting the rest of the drive. |

## Lab Environment Specifications

| Component | Details |
| ----- | ----- |
| **Server Hostname** | `SMEC-DC1` (`192.168.1.1`) — Windows Server 2025 |
| **Active Directory Domain** | `smec.com` |
| **Protected Volume** | `Local Disk (C:)` (NTFS — 100 GB Capacity) |
| **Test Data Folder** | `C:\Data` |
| **Sample Files** | At least 2 `.txt` files (e.g., `report.txt`, `accounts.txt`) |
| **Default VSS Schedule** | Weekly (Mon–Fri) at `7:00 AM & 12:00 PM` *(Customizable in Settings)* |
| **VSS Storage Limit** | `10240 MB` (10 GB) on `C:\` |

## Simulator Interface & Features

The simulator (`lab22.html`) provides a self-contained Windows Server 2025 desktop environment:

1. **Left Sidebar (Server Card, Progress Checklist & Warnings):**
   * Displays live volume VSS status (`Shadow Copies (C:): Disabled` $\rightarrow$ `Enabled ✓`).
   * **Live Lab Progress Card:** Tracks all 6 milestones (`Create C:\Data & Files`, `Enable Shadow Copies`, `Schedule & Create Now`, `Simulate File Deletion`, `Restore Previous Version`, and `Verify Recovered File`) in real time.
   * Features a dedicated **⚠️ Production Warning** card, a **"What is Shadow Copy (VSS)?"** quick-reference card, and a **Reset Lab** button.

2. **Main Workspace Stage:**
   * **Authentic File Explorer (`This PC` $\rightarrow$ `C:\` $\rightarrow$ `C:\Data`):** Right-click context menus for creating folders/text files, opening drive properties, deleting files, and restoring previous versions.
   * **Local Disk (`C:`) Properties (`Shadow Copies` Tab):** Includes **Enable** (automatically captures the initial snapshot just like real Windows Server), **Settings...**, **Schedule...**, and **Create Now** with a live timestamped snapshot table.
   * **Delete Simulation Warning Dialog:** Displays a clear production safety warning before simulating accidental deletion.
   * **Folder Properties (`Previous Versions` Tab):** Lists all captured snapshots with file contents, supporting **Open** and **Restore...** with confirmation dialogs.

3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed cleanly below the desktop stage so it never overlaps Start menus, Explorer windows, or modal dialogs.
   * Tracks progress from **Step 1 / 10** to **100% Complete**.

## Complete 10-Step Lab Walkthrough

### Phase 1: Create Test Folder & Sample Files (`C:\Data`)

1. **Open This PC:** Click **This PC (File Explorer)** on the Desktop (or **Start** $\rightarrow$ **Run** $\rightarrow$ `explorer`).
2. **Create `Data` Folder:** Open **Local Disk (`C:`)** $\rightarrow$ **Right-click** empty white space $\rightarrow$ **New** $\rightarrow$ **Folder** $\rightarrow$ name it `Data`.
3. **Add Sample Text Files:** Open **`Data`** (`C:\Data`) $\rightarrow$ **Right-click** empty space $\rightarrow$ **New** $\rightarrow$ **Text Document** to create at least **2 files** (e.g., `report.txt` and `accounts.txt`).

### Phase 2: Enable Shadow Copies, Review Schedule & Take Snapshot (`Local Disk (C:)`)

4. **Open Drive Shadow Copies Tab:** Click **💻 This PC** in the Explorer toolbar $\rightarrow$ **Right-click Local Disk (`C:`)** $\rightarrow$ **Properties** $\rightarrow$ click the **Shadow Copies** tab.
5. **Enable Shadow Copies:** Click **Enable** $\rightarrow$ click **Yes** on the confirmation prompt. Windows Server immediately takes the initial Shadow Copy snapshot and activates the default schedule (`7:00 AM` & `12:00 PM`, Mon–Fri).
6. **Review Schedule & Create On-Demand Snapshot:**
   * Click **Settings...** $\rightarrow$ **Schedule...** to inspect or customize the automatic backup frequency and times $\rightarrow$ click **OK** $\rightarrow$ **OK**.
   * Click **Create Now** to capture an immediate on-demand snapshot, then click **OK** to close Drive Properties.

### Phase 3: Simulate Accidental Deletion & Recover via Previous Versions

7. **Simulate File Deletion:** Open `C:\Data` $\rightarrow$ **Right-click** any text file $\rightarrow$ click **🗑️ Delete** $\rightarrow$ review the production warning and click **Delete (Simulate Accident)**.
8. **Open Folder Previous Versions:** Click **↑ Up** to return to `C:\` $\rightarrow$ **Right-click** the **`Data`** folder $\rightarrow$ click **🕒 Restore previous versions / Properties**.
9. **Restore from Shadow Copy:** In the **Previous Versions** tab, click a captured Shadow Copy row $\rightarrow$ click **Restore...** $\rightarrow$ click **Restore** on the confirmation popup $\rightarrow$ click **OK**.
10. **Verify Recovered File:** Open `C:\Data` in File Explorer and click the restored file to confirm its contents are intact, achieving **100% Lab Completion**.

## Key Technical Concepts & Best Practices

* **Shadow Copies Are NOT a Full Backup Solution:** Because Shadow Copies are stored locally on the server's disk (typically on the same volume or another local volume), they **cannot** protect against hardware disk failure, ransomware encrypting the server, or physical disasters. Always pair VSS with off-site / cloud backups (e.g., Azure Backup or Windows Server Backup).
* **Dedicated Storage Volume:** In high-I/O production file servers, Microsoft recommends storing the Shadow Copy storage area on a **separate dedicated volume** (configured under **Settings...**) to avoid disk I/O contention.
* **Snapshot Limit:** Windows Server supports up to **64 Shadow Copies** per volume; once the limit or allocated storage space is reached, the oldest snapshot is automatically deleted (FIFO).

## How to Run & Deploy

### Launch Live Online

Open the hosted simulator directly in your browser:

> **https://tibinjohn193-blip.github.io/az-802/lab22.html**

### Run Locally (Offline)

1. Download `lab22.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server 2025 File Explorer & Properties dialog styling, live progress badges)
* **Vanilla JavaScript** (10-step validation engine, real-time timestamped VSS snapshot state management, simulated accidental deletion warning, and Previous Versions restore workflow)