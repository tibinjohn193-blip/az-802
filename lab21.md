# IIS FTP Server Hosting with Authentication Lab — Windows Server 2025 & Client Simulator

### 📂 Authenticated Windows Server FTP Hosting — Interactive Lab (`lab21.html`)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab21.html>

An interactive, single-file web-based **Windows Server 2025 & Windows 11 Client Simulator** designed for hands-on **File Transfer Protocol (FTP)** administration training in IIS. This lab guides learners step-by-step through verifying an Active Directory user (`Arun` in the `Tech` OU), creating a dedicated `C:\FTP` directory with sample text files, hosting an authenticated FTP site in **IIS Manager (`inetmgr`)** using **No SSL** and **Basic Authentication**, configuring a domain-subnet IP on a Windows 11 client, connecting via `ftp://192.168.1.1` in File Explorer, logging in as `Arun`, and opening the hosted files.

## Table of Contents

1. [What is FTP (File Transfer Protocol)?](#what-is-ftp-file-transfer-protocol)

2. [FTP Authentication & SSL Options in IIS](#ftp-authentication--ssl-options-in-iis)

3. [Lab Environment Specifications](#lab-environment-specifications)

4. [Lab Credentials](#lab-credentials)

5. [Simulator Interface & Features](#simulator-interface--features)

6. [Complete 11-Step Lab Walkthrough](#complete-11-step-lab-walkthrough)

7. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)

8. [How to Run & Deploy](#how-to-run--deploy)

9. [Technology Stack](#technology-stack)

## What is FTP (File Transfer Protocol)?

**FTP (File Transfer Protocol)** is a standard client-server network protocol used to transfer, upload, download, and manage files between a client computer and a server over a TCP/IP network.

* **Default Control Port:** **TCP Port 21** (used for authentication and FTP commands).
* **Default Data Port:** **TCP Port 20** (in Active mode) or a dynamic passive port range (in Passive mode).
* **IIS FTP Service:** Windows Server includes an integrated **FTP Server** role service inside **Web Server (IIS)**, allowing administrators to host FTP directories and enforce Active Directory user authentication and Read/Write permissions directly from **IIS Manager**.

## FTP Authentication & SSL Options in IIS

### Authentication Modes

| Mode | Behavior | Best Use Case |
| ----- | ----- | ----- |
| **Anonymous** | Allows anyone to connect without entering a username or password. | Public download portals (Read-only). |
| **Basic** *(Used in this Lab)* | Prompts clients for a valid Windows/Active Directory **Username and Password** (`Arun` / `password123`). | Controlled internal file sharing where specific AD users need Read/Write access. |

### SSL Settings

| Setting | Behavior | Notes |
| ----- | ----- | ----- |
| **No SSL** *(Used in this Lab)* | Allows plain unencrypted FTP connections without an SSL/TLS certificate. | Ideal for isolated lab testing and internal learning environments. |
| **Allow SSL** | Accepts both encrypted (FTPS) and unencrypted FTP connections. | Transitional environments migrating to FTPS. |
| **Require SSL** | Enforces TLS encryption (FTPS) using a server certificate for all logins and transfers. | **Recommended for production** whenever Basic Authentication is used. |

## Lab Environment Specifications

| Component | Server (`SMEC-DC1`) | Client (`CLIENT-PC`) |
| ----- | ----- | ----- |
| **Operating System** | Windows Server 2025 | Windows 11 |
| **IP Address** | `192.168.1.1` | `192.168.1.10` *(Any `192.168.1.2`–`.254` configured in Lab)* |
| **Subnet Mask** | `255.255.255.0` | `255.255.255.0` *(Configured in Lab)* |
| **Preferred DNS Server** | `192.168.1.1` | `192.168.1.1` *(Configured in Lab)* |
| **Active Directory Domain** | `smec.com` | Connects over `192.168.1.0/24` subnet |
| **FTP Physical Path** | `C:\FTP` (with 2+ `.txt` files) | Accessed via `ftp://192.168.1.1` |
| **FTP Binding & SSL** | `192.168.1.1:21` (**No SSL**) | File Explorer / Edge Handoff |
| **Authorization** | Specified user: `Arun` (**Read & Write**) | Authenticated via **Log On As** prompt |

## Lab Credentials

| Account / Role | Username | Password | Notes |
| ----- | ----- | ----- | ----- |
| **Authorized AD FTP User** | `Arun` *(or `smec\Arun` / `arun@smec.com`)* | `password123` | Located in `smec.com > Tech` OU |
| **Server Admin** | `SMEC\Administrator` | *(Pre-logged in)* | Used on `SMEC-DC1` for ADUC & IIS Manager |

## Simulator Interface & Features

The simulator (`lab21.html`) uses a full-screen **Server / Client Switcher** layout:

1. **Left Sidebar (Machine Switcher & Live Checklist):**
   * Switch cleanly between **🖥️ Server (`SMEC-DC1`)** and **💻 Client PC (`Win 11`)**.
   * **Attention Pulse Highlight:** Automatically pulses a yellow border around **Client PC** once the FTP site is hosted on the server.
   * **Live Lab Progress Card:** Tracks all 6 milestones (`Verify AD User`, `Create C:\FTP & Files`, `Add FTP Site`, `Client IP`, `FTP Login`, and `Access Server File`) in real time.

2. **Main Workspace Stage:**
   * **ADUC (`dsa.msc`):** Inspect `smec.com > Tech` OU to verify user `Arun`.
   * **Authentic Server File Explorer:** Right-click empty space in `C:\` $\rightarrow$ **New** $\rightarrow$ **Folder** (`FTP`), and right-click inside `C:\FTP` $\rightarrow$ **New** $\rightarrow$ **Text Document** to create sample files.
   * **IIS Manager (`inetmgr`):** Complete 3-step **Add FTP Site** wizard (*Site Information*, *Binding & SSL*, and *Authentication & Authorization*).
   * **Client Network & FTP Access:** Manual IPv4 setup in `ncpa.cpl`, File Explorer address bar (`ftp://192.168.1.1`), authentic Windows **Log On As** credentials dialog (rejects invalid logins with `530 User cannot log in`), and clickable file preview. Also includes Microsoft Edge handoff to File Explorer.

3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed cleanly below the desktop stage so it never overlaps Start menus, wizards, or login modals.
   * Tracks progress from **Step 1 / 11** to **100% Complete**.

## Complete 11-Step Lab Walkthrough

### Phase 1: Verify AD User & Prepare FTP Directory (`SMEC-DC1` — `192.168.1.1`)

1. **Verify AD User `Arun`:** Open **AD Users & Computers** (Desktop icon or `Start` $\rightarrow$ `Run` $\rightarrow$ `dsa.msc`) and click the **Tech** OU under `smec.com` to confirm user **Arun** (`arun@smec.com`) exists.

2. **Create `C:\FTP` Folder:** Open **File Explorer** $\rightarrow$ **Right-click** empty white space inside `C:\` $\rightarrow$ **New** $\rightarrow$ **Folder** $\rightarrow$ name it `FTP`.

3. **Create Sample Text Files:** Open **`C:\FTP`** $\rightarrow$ **Right-click** empty space $\rightarrow$ **New** $\rightarrow$ **Text Document** to create at least **2 text files** (e.g., `file1.txt` and `file2.txt`).

### Phase 2: Host Authenticated FTP Site in IIS (`SMEC-DC1`)

4. **Open Add FTP Site Wizard:** Open **IIS Manager** (Desktop icon or `Start` $\rightarrow$ `Run` $\rightarrow$ `inetmgr`) $\rightarrow$ right-click **Sites** $\rightarrow$ click **📂 Add FTP Site...**.

5. **Configure FTP Site Settings:**
   * **Step 1 (Site Information):** FTP site name: `FTP`, Physical path: `C:\FTP` $\rightarrow$ click **Next**.
   * **Step 2 (Binding and SSL):** IP Address: `192.168.1.1`, Port: `21`, SSL: select **No SSL** $\rightarrow$ click **Next**.
   * **Step 3 (Authentication & Authorization):**
     * Check **Basic** authentication.
     * Under *Allow access to*, select **Specified users** and enter `Arun`.
     * Check **Read** and **Write** permissions, then click **Finish**.

### Phase 3: Configure Client IP & Access FTP Files (`CLIENT-PC`)

6. **Switch to Client PC & Open Network Connections:** Click **💻 Client PC** in the left sidebar. Open **Start** $\rightarrow$ **Run**, type `ncpa.cpl`, and click **OK**. Right-click **Ethernet** $\rightarrow$ **Properties** $\rightarrow$ **Internet Protocol Version 4 (TCP/IPv4)**.

7. **Configure Client Static IP:** Assign an IP in the server's subnet:
   * **IP address:** `192.168.1.10` *(any valid IP in `192.168.1.2`–`192.168.1.254`)*
   * **Subnet mask:** `255.255.255.0`
   * **Preferred DNS server:** `192.168.1.1`
   Click **OK** to save.

8. **Open Client File Explorer:** Click **File Explorer (This PC)** on the Client Desktop.

9. **Connect to FTP Server:** Click the File Explorer address bar, type `ftp://192.168.1.1`, and press **Enter**.

10. **Authenticate as `Arun`:** In the **Log On As** dialog, enter:
    * **User name:** `Arun` *(or `smec\Arun`)*
    * **Password:** `password123`
    Click **Log On**.

11. **Open Hosted FTP File:** Double-click any text file displayed inside `ftp://192.168.1.1` to open and read its contents, achieving **100% Lab Completion**.

## Key Technical Concepts & Best Practices

* **Why Use Windows File Explorer Instead of Modern Browsers for FTP?** Modern web browsers (Chrome, Edge, Firefox) removed built-in FTP rendering support for security reasons and instead hand off `ftp://` links to the operating system's native **File Explorer** or dedicated FTP clients (like FileZilla or WinSCP).
* **NTFS Permissions Still Apply:** In real Windows Server environments, authorizing `Arun` inside IIS FTP Authorization Rules is only half the requirement—the user must also have matching **NTFS Read/Write permissions** on the physical `C:\FTP` folder.
* **Production Security Tip:** Because **Basic Authentication** sends credentials in Base64 (cleartext) when **No SSL** is used, always enable **Require SSL (FTPS)** with a valid TLS certificate—or use **SFTP**—in production networks.

## How to Run & Deploy

### Launch Live Online

Open the hosted simulator directly in your browser:

> **https://tibinjohn193-blip.github.io/az-802/lab21.html**

### Run Locally (Offline)

1. Download `lab21.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server 2025 & Windows 11 UI styling, sidebar attention indicators)
* **Vanilla JavaScript** (11-step validation engine, ADUC viewer, authentic File Explorer context menus, 3-step IIS FTP Site wizard, Client IPv4 validator, and authenticated FTP `Log On As` simulation)