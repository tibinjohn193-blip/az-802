# GPO Software Installation Policy Lab — Google Chrome MSI Deployment Simulator

<div align="center">

### 📦 GPO Software Deployment — Interactive Windows Server 2025 & Client Lab

[![Click to Launch Interactive Lab](https://img.shields.io/badge/▶_CLICK_TO_START-INTERACTIVE_LAB-28a745?style=for-the-badge)](https://tibinjohn193-blip.github.io/<REPO-NAME>/gpo_software_installation_chrome_lab_v4.html)

*(Replace `<REPO-NAME>` and the HTML filename in the link above with your actual GitHub repository and uploaded filename once GitHub Pages is enabled.)*

</div>

An interactive, single-file web-based **Windows Server 2025 & Domain-Joined Client Simulator** designed for hands-on Active Directory and Group Policy software deployment training. This lab walks students through preparing an enterprise `.msi` package, creating and sharing a network folder with proper **Share + NTFS Security permissions**, copying the UNC path, configuring **Software Installation** in Group Policy (`gpmc.msc`), and verifying automatic deployment on a client workstation.

---

## Table of Contents

1. [Overview & Learning Objectives](#overview--learning-objectives)
2. [Lab Environment Specifications](#lab-environment-specifications)
3. [Lab Credentials & Resources](#lab-credentials--resources)
4. [Simulator Interface & Features](#simulator-interface--features)
5. [Complete 16-Step Lab Walkthrough](#complete-16-step-lab-walkthrough)
6. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
7. [How to Run & Deploy](#how-to-run--deploy)
8. [Technology Stack](#technology-stack)

---

## Overview & Learning Objectives

Manually installing software on hundreds of domain workstations is time-consuming and error-prone. Active Directory **Group Policy Software Installation** allows system administrators to centrally deploy `.msi` packages to targeted users or computers automatically over the network.

By completing this lab, students will learn how to:

* Obtain enterprise `.msi` installers (e.g., Google Chrome Enterprise Standalone MSI).
* Create a dedicated software repository (`C:\Software`) using authentic Windows File Explorer workflows.
* Configure both **Advanced Sharing** permissions and **NTFS Security** permissions so domain clients can read the package over the network.
* Retrieve and use a **UNC Network Path** (`\\SMEC-DC1\Software`) instead of a local drive path.
* Create and link a new GPO (`Install_Chrome`) to a specific Organizational Unit (`Sales` OU).
* Configure **Software installation Properties** (*Default package location* and *Assign* deployment mode) and add a new `.msi` package.
* Force Group Policy updates with `gpupdate /force` and verify automatic installation upon client sign-in.

---

## Lab Environment Specifications

| Component | Details |
| :--- | :--- |
| **Domain Controller (Server)** | `SMEC-DC1` (`192.168.1.100`) — Windows Server 2025 |
| **Client Workstation** | `PC-ARUN` (`192.168.1.101`) — Domain Joined to `smec.com` |
| **Active Directory Domain** | `smec.com` |
| **Target Organizational Unit** | `Sales` OU |
| **Software Package** | `GoogleChromeStandaloneEnterprise64.msi` (Pre-downloaded on Server Desktop) |
| **Local Share Folder** | `C:\Software` |
| **UNC Network Path** | `\\SMEC-DC1\Software` |
| **Target GPO** | `Install_Chrome` (Linked to `Sales` OU) |
| **Deployment Method** | **Assigned** (under `User Configuration`) |

---

## Lab Credentials & Resources

| Item | Value | Notes |
| :--- | :--- | :--- |
| **Official Chrome MSI Download** | `https://chromeenterprise.google/download/` | Reference link displayed in the lab sidebar |
| **Client Domain User** | `smec\arun` (`Arun`) | Member of `Sales` OU on `PC-ARUN` |
| **Client Password** | `Password123` | Used to sign in and trigger GPO software installation |
| **Server Admin** | `SMEC\Administrator` | *(Pre-logged in on `SMEC-DC1`)* |

---

## Simulator Interface & Features

The simulator (`gpo_software_installation_chrome_lab_v4.html`) features a clean, full-screen **Machine Switcher** architecture:

1. **Left Sidebar (Machine Switcher & Reference):**
   * Switch cleanly between **🖥️ Server (`SMEC-DC1`)** and **💻 Client (`PC-ARUN`)**.
   * **Attention Pulse Highlight:** Automatically highlights the Client or Server button when the lab requires switching machines.
   * Includes the official Chrome Enterprise MSI download link, UNC path, GPO name, client password, and a **Reset Lab** button.
2. **Main Workspace Stage:**
   * **Authentic File Explorer:** Right-click empty space to create `New -> Folder` (`Software`), right-click to `Copy`/`Paste` the `.msi` file, and right-click the folder for `Properties`.
   * **Realistic Permissions Dialogs:** Separate **Sharing** (*Advanced Sharing -> Permissions*) and **Security** (*Edit -> Add -> Everyone -> Full control*) workflows, plus selectable **Network Path** (`Ctrl+C` or right-click `Copy`).
   * **GPMC & GPME:** Create GPO, edit `User Configuration -> Policies -> Software Settings -> Software installation`, configure **Properties** (*Default package location* + *Assign*), and add **New -> Package...**.
   * **Client Auto-Install Simulation:** Signing in as `Arun` displays the GPO software installation screen and deploys Google Chrome to the desktop and Start menu.
3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed cleanly below the desktop stage so it never obscures Start menus, Explorer windows, or dialog buttons.
   * Tracks progress from **Step 1 / 16** to **100% Complete**.

---

## Complete 16-Step Lab Walkthrough

### Phase 1: Prepare the Software Folder (Server)

1. **Copy Chrome MSI:** On **SMEC-DC1**, right-click `GoogleChromeStandaloneEnterprise64.msi` on the Desktop and click **Copy**.
2. **Open Local Disk (C:):** Open **This PC** on the Desktop (or `Start -> File Explorer`) and double-click **Local Disk (C:)**.
3. **Create `Software` Folder:** Right-click empty white space inside `C:\` → select **New → Folder** → type `Software` and press **Enter**.
4. **Paste Chrome MSI:** Double-click the `Software` folder → right-click empty space inside `C:\Software` → click **Paste**.

### Phase 2: Configure Sharing, NTFS Security & Copy UNC Path (Server)

5. **Open Folder Properties:** Navigate back up to `Local Disk (C:)`, right-click the **Software** folder, and select **Properties**.
6. **Configure Advanced Sharing:**
   * On the **Sharing** tab, click **Advanced Sharing...**.
   * Check **Share this folder** (`Software`), click **Permissions**, select **Everyone**, allow **Full Control**, and click **OK** twice.
7. **Configure NTFS Security Permissions:**
   * Switch to the **Security** tab → click **Edit...** → click **Add...** → type `Everyone` and click **OK**.
   * Check **Full control** under Allow → click **OK**, then click **Close** on *Software Properties*.
8. **Re-open Properties & Copy Network Path:**
   * Right-click the **Software** folder again → select **Properties**.
   * On the **Sharing** tab, select the **Network Path** (`\\SMEC-DC1\Software`), press **Ctrl+C** (or right-click it → **Copy**), then close Properties and File Explorer.

### Phase 3: Create & Configure Software Installation GPO (Server)

9. **Open Group Policy Management:** Click **Start → Run**, type `gpmc.msc`, and click **OK**.
10. **Create & Link GPO:** Expand `Forest: smec.com > Domains > smec.com`. Right-click the **Sales** OU → **Create a GPO in this domain, and Link it here...** → name it `Install_Chrome` → click **OK**.
11. **Edit the GPO:** Right-click **Install_Chrome** and click **Edit...** to open the **Group Policy Management Editor**.
12. **Open Software Installation Properties:** Navigate to:
    ```text
    User Configuration
     └── Policies
          └── Software Settings
               └── Software installation
    ```
    Right-click **Software installation** and select **Properties**.
13. **Set Default Package Location & Assign Mode:**
    * Paste `\\SMEC-DC1\Software` into **Default package location** (using `Ctrl+V`, right-click `Paste`, or **Browse...**).
    * Under **New packages**, select **Assign**.
    * Click **Apply** and **OK**.
14. **Add New Package & Update Policy:**
    * Right-click **Software installation** → **New → Package...**.
    * The dialog opens automatically at `\\SMEC-DC1\Software`. Select `GoogleChromeStandaloneEnterprise64.msi` and click **Open** (it is automatically set to **Assigned**).
    * Close GPME and GPMC, open **Start → Run**, type `cmd`, and run:
      ```cmd
      gpupdate /force
      ```

### Phase 4: Verify Automatic Deployment (Client)

15. **Sign In on Client (`PC-ARUN`):**
    * Click **💻 Client** in the left sidebar.
    * Sign in as **Arun** using password `Password123`.
    * Watch Windows apply the `Install_Chrome` Group Policy and install Google Chrome from `\\SMEC-DC1\Software`.
16. **Verify Google Chrome:** Click the newly deployed **🌐 Google Chrome** icon on the Client Desktop (or Start Menu) to confirm installation and achieve **100% Lab Completion**.

---

## Key Technical Concepts & Best Practices

### Why Use `.msi` Instead of `.exe`?
Group Policy Software Installation natively supports **Windows Installer (`.msi`)** packages because they support silent, unattended, and standardized installation, repair, upgrade, and removal. Standard `.exe` installers cannot be added directly to GPO Software Installation without being repackaged or deployed via startup scripts.

### Why Must You Use a UNC Path (`\\SMEC-DC1\Software`)?
If you browse to a local path like `C:\Software\GoogleChromeStandaloneEnterprise64.msi`, client computers will look for that file on *their own* `C:\` drive and the installation will fail. Using the **UNC (Universal Naming Convention) path** (`\\SMEC-DC1\Software`) ensures every client PC locates the installer over the network from `SMEC-DC1`.

### Share Permissions vs. NTFS Security Permissions
When a user accesses a folder over the network, Windows evaluates **both** Share permissions and NTFS Security permissions and applies the **most restrictive** combination. Configuring both tabs ensures clients have the required read/execute access to pull the `.msi` file. *(Note: In strict production environments, `Authenticated Users` or `Domain Computers` with Read access is preferred over `Everyone` with Full Control.)*

### Assigned vs. Published Software Deployment

| Method | Available Under | Behavior |
| :--- | :--- | :--- |
| **Assigned (User)** | User Configuration | Advertises the app to the user at logon (shortcuts appear on Desktop/Start Menu) and installs automatically when launched or at logon. |
| **Assigned (Computer)** | Computer Configuration | Automatically installs the software for all users of that computer on the next system reboot. |
| **Published** | User Configuration only | Makes the software optional—users can manually install it from *Control Panel → Programs and Features → Install a program from the network*. |

---

## How to Run & Deploy

### Run Locally
1. Download `gpo_software_installation_chrome_lab_v4.html`.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** — no external libraries, servers, or build tools required.

### Deploy on GitHub Pages
1. Upload the HTML file to your GitHub repository (e.g., as `lab10.html` or `gpo_software_installation_chrome_lab_v4.html`).
2. Go to **Repository Settings → Pages**.
3. Under **Build and deployment**, select **Deploy from a branch** (`main` / `root`) and click **Save**.
4. Update `<REPO-NAME>` and the filename in the launch button URL at the top of this README so students can launch it with one click.

---

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server UI styling, sidebar attention indicators)
* **Vanilla JavaScript** (16-step validation engine, authentic File Explorer context menus, Share/NTFS permission modals, MMC tree views, simulated CMD parser, and client deployment simulation)