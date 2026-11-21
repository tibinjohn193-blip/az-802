# Active Directory FSMO Role Migration Lab (`ntdsutil`) — Windows Server 2025 Simulator

<div align="center">

### 🔄 FSMO Role Transfer — Interactive Windows Server 2025 Lab (`lab15.html`)

[![Click to Launch Interactive Lab](https://img.shields.io/badge/▶_CLICK_TO_START-INTERACTIVE_LAB_(lab15.html)-0078D7?style=for-the-badge&logo=windows&logoColor=white)](https://tibinjohn193-blip.github.io/az-802/lab15.html)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab15.html>

</div>

An interactive, single-file web-based **Windows Server 2025 Dual-Server Simulator** designed for hands-on Active Directory Domain Services (AD DS) administration and migration training. This lab guides learners step-by-step through querying current **FSMO (Flexible Single Master Operations)** role holders with `netdom query fsmo`, connecting to an Additional Domain Controller (`server2.smec.com`) using **`ntdsutil`**, gracefully transferring all 5 FSMO roles from `server1.smec.com` to `server2.smec.com`, and verifying ownership on both servers.

## Table of Contents

1. [What are FSMO Roles?](#what-are-fsmo-roles)
2. [Transferring vs. Seizing FSMO Roles](#transferring-vs-seizing-fsmo-roles)
3. [Lab Environment Specifications](#lab-environment-specifications)
4. [Simulator Interface & Features](#simulator-interface--features)
5. [Complete 11-Step Lab Walkthrough](#complete-11-step-lab-walkthrough)
6. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
7. [How to Run & Deploy](#how-to-run--deploy)
8. [Technology Stack](#technology-stack)

## What are FSMO Roles?

While Active Directory uses a **multi-master replication model** for most changes (such as creating users or updating passwords), certain critical directory operations cannot safely happen on multiple Domain Controllers at the same time. **FSMO (Flexible Single Master Operations)** roles—also known as **Operations Master roles**—assign these specialized tasks to a single designated Domain Controller.

There are **5 FSMO roles** in total: **2 Forest-wide** roles and **3 Domain-wide** roles.

| FSMO Role | Scope | Primary Responsibility |
| :--- | :--- | :--- |
| **Schema Master** | Forest-wide (1 per forest) | Controls all updates and modifications to the Active Directory schema (object classes and attributes). |
| **Domain Naming Master** | Forest-wide (1 per forest) | Controls adding or removing domains (Child or Tree domains) and application partitions in the forest. |
| **PDC Emulator** | Domain-wide (1 per domain) | Handles password changes, account lockouts, Group Policy editing, legacy authentication, and acts as the domain time source (NTP). |
| **RID Master** | Domain-wide (1 per domain) | Allocates pools of Relative Identifiers (RIDs) to Domain Controllers so every new security principal (user, group, computer) receives a unique SID. |
| **Infrastructure Master** | Domain-wide (1 per domain) | Updates cross-domain object references (such as group memberships referencing users from another domain in the forest). |

## Transferring vs. Seizing FSMO Roles

| Method | When to Use | Data Safety | Command in `ntdsutil` |
| :--- | :--- | :--- | :--- |
| **Transfer** *(Used in this Lab)* | Planned migration, hardware upgrade, or decommissioning when **both DCs are online and healthy**. | **Graceful & Safe** — Synchronizes the latest changes before moving ownership. | `transfer <role>` |
| **Seize** | Emergency recovery when the current FSMO holder has **permanently failed** and will never come back online. | **Forceful** — Immediately takes ownership without syncing from the dead DC. | `seize <role>` |

> **Critical Warning:** If you **seize** a FSMO role from a failed Domain Controller, **never** bring that old Domain Controller back onto the network without wiping it first, as it can cause severe Active Directory conflicts.

## Lab Environment Specifications

| Component | Server 1 (Source Primary DC) | Server 2 (Target Additional DC) |
| :--- | :--- | :--- |
| **FQDN** | `server1.smec.com` | `server2.smec.com` |
| **Operating System** | Windows Server 2025 | Windows Server 2025 |
| **IP Address** | `192.168.1.1` | `192.168.1.2` |
| **DNS Server** | `192.168.1.1` | `192.168.1.1` |
| **Active Directory Domain** | `smec.com` | `smec.com` |
| **Initial FSMO Role Count** | `5 / 5` (All 5 Roles) | `0 / 5` |
| **Final FSMO Role Count** | `0 / 5` | `5 / 5` (All 5 Roles Migrated) |

## Simulator Interface & Features

The simulator (`lab15.html`) uses a full-screen **Dual-Server Switcher** layout:

1. **Left Sidebar (Server Switcher & Live FSMO Tracker):**
   * Switch seamlessly between **🖥️ Server 1 (`server1.smec.com`)** and **🖥️ Server 2 (`server2.smec.com`)**.
   * **Live 5 FSMO Role Tracker:** Dynamically updates each role badge (`server1` $\rightarrow$ `server2 ✓`) in real time as you complete each transfer.
   * **Attention Pulse Highlight:** Automatically pulses a yellow border around **Server 2** when it is time to run the final verification.
   * Includes quick-reference commands and a **Reset Lab** button.

2. **Main Workspace Stage:**
   * **Authentic Administrator Command Prompt:** Supports command history (`Up`/`Down` arrow keys), `cls`, `netdom query fsmo`, and interactive `ntdsutil` sub-prompts (`ntdsutil:`, `fsmo maintenance:`, `server connections:`).
   * **Authentic `?` Help Menu:** Displays the exact Windows Server `fsmo maintenance` transfer and seize command list.
   * **Role Transfer Confirmation Dialog:** Triggers the real Windows `Yes` / `No` confirmation popup for each FSMO role transfer.

3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed cleanly below the desktop stage so it never overlaps Start menus or Command Prompt output.
   * Tracks progress from **Step 1 / 11** to **100% Complete**, dynamically listing any remaining FSMO roles still left to transfer.

## Complete 11-Step Lab Walkthrough

### Phase 1: Query Current FSMO Role Holders (`server1.smec.com`)

1. **Open Command Prompt:** On **Server 1**, click **Start** $\rightarrow$ **Run**, type `cmd`, and click **OK** (or click the **Command Prompt (Admin)** desktop icon).
2. **Query Current FSMO Holders:** Run the following command to verify all 5 FSMO roles are currently held by `server1.smec.com`:
   ```cmd
   netdom query fsmo
   ```

### Phase 2: Connect to Target ADC in `ntdsutil` (`server1.smec.com`)

3. **Launch `ntdsutil`:**
   ```cmd
   ntdsutil
   ```
4. **Enter FSMO Maintenance Mode:** At the `ntdsutil:` prompt, type:
   ```cmd
   roles
   ```
5. **Open Server Connections:** At the `fsmo maintenance:` prompt, type:
   ```cmd
   connections
   ```
6. **Bind to Target ADC (`server2.smec.com`):** At the `server connections:` prompt, type:
   ```cmd
   connect to server server2.smec.com
   ```
7. **Return to `fsmo maintenance:` Prompt:** Type `q` (or `quit`) and press **Enter**.
8. **View Available Transfer Commands:** Type `?` and press **Enter** to inspect all transfer and seize commands.

### Phase 3: Transfer All 5 FSMO Roles to `server2.smec.com`

9. **Transfer Each Role:** Run the following five commands one by one at the `fsmo maintenance:` prompt, clicking **Yes** on each **Role Transfer Confirmation Dialog**:
   ```cmd
   transfer schema master
   transfer naming master
   transfer pdc
   transfer rid master
   transfer infrastructure master
   ```
   Watch the **Live FSMO Role Tracker** in the left sidebar update to `5 / 5 Moved`.

### Phase 4: Exit `ntdsutil` & Verify on Both Servers

10. **Exit & Verify on Server 1:** Type `q` twice to return to `C:\Users\Administrator>`, then run:
    ```cmd
    netdom query fsmo
    ```
    Confirm all 5 roles now list `server2.smec.com`.
11. **Final Verification on Server 2:** Switch to **🖥️ Server 2 (ADC)** in the left sidebar, open **Command Prompt**, and run:
    ```cmd
    netdom query fsmo
    ```
    Confirm `server2.smec.com` reports itself as the owner of all 5 FSMO roles to achieve **100% Lab Completion**.

## Key Technical Concepts & Best Practices

### PowerShell Alternative (Modern Windows Server)
In addition to `ntdsutil`, administrators can move all 5 FSMO roles in a single PowerShell command on Windows Server:
```powershell
Move-ADDirectoryServerOperationMasterRole -Identity "server2.smec.com" -OperationMasterRole SchemaMaster, DomainNamingMaster, PDCEmulator, RIDMaster, InfrastructureMaster
```

### When Do You Migrate FSMO Roles?
* Upgrading from an older Windows Server OS (e.g., Server 2016/2019) to Windows Server 2025 before demoting the old Primary DC.
* Performing planned hardware replacement or datacenter migration.
* Balancing operations master roles across multiple Domain Controllers in large enterprise environments.

## How to Run & Deploy

### Launch Live Online
Open the hosted simulator directly in your browser:
> **https://tibinjohn193-blip.github.io/az-802/lab15.html**

### Run Locally (Offline)
1. Download `lab15.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, authentic Windows CMD styling, live FSMO role tracker badges)
* **Vanilla JavaScript** (11-step validation engine, interactive `ntdsutil` state machine, command history support, and confirmation dialog workflow)