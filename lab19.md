# IIS Website Hosting Lab (`www.tech.com`) — Windows Server 2025 Simulator

[![Launch Interactive Lab](https://img.shields.io/badge/🚀_Launch_Interactive_Lab-lab19.html-0078D7?style=for-the-badge)](https://tibinjohn193-blip.github.io/az-802/lab19.html)

### 🌍 Windows Server IIS Web Hosting, DNS, HTTP Redirection & FTP — Interactive Lab (`lab19.html`)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab19.html>

An interactive, single-file web-based **Windows Server 2025 Simulator** designed for hands-on **Internet Information Services (IIS)** web hosting and DNS integration training. This lab walks students step-by-step through creating a DNS Forward Lookup Zone (`tech.com`) and Host (`A`) record (`www.tech.com`), installing the **Web Server (IIS)** role with **HTTP Redirection** and **FTP Server** role services, creating a website directory (`C:\inetpub\site1`) and HTML page (`a.html`) via authentic **File Explorer + Notepad (`Save As`)** workflows, hosting the site in **IIS Manager (`inetmgr`)**, configuring the **Default Document**, and verifying the live site in **Microsoft Edge**.

## Table of Contents

1. [What is IIS (Internet Information Services)?](#what-is-iis-internet-information-services)
2. [Key IIS Role Services Installed in This Lab](#key-iis-role-services-installed-in-this-lab)
3. [Lab Environment Specifications](#lab-environment-specifications)
4. [Simulator Interface & Features](#simulator-interface--features)
5. [Complete 11-Step Lab Walkthrough](#complete-11-step-lab-walkthrough)
6. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
7. [How to Run & Deploy](#how-to-run--deploy)
8. [Technology Stack](#technology-stack)

## What is IIS (Internet Information Services)?

**IIS (Internet Information Services)** is Microsoft's extensible web server software built into Windows Server. It allows organizations to host websites, web applications, APIs, and file transfer services over **HTTP**, **HTTPS**, and **FTP** protocols.

### Why Use IIS on Windows Server?

* **Host Header Virtual Hosting:** Host multiple independent websites (e.g., `www.tech.com`, `intranet.smec.com`) on a single server IP address (`192.168.1.1`) and Port `80` by binding each site to a unique **Host Name**.
* **Modular Role Services:** Administrators install only the features they need—such as **Static Content**, **Default Document**, **HTTP Redirection**, and **FTP Server**—keeping the attack surface minimal.
* **Tight Windows & AD Integration:** Seamlessly integrates with Active Directory authentication, NTFS folder permissions, and Windows DNS.

## Key IIS Role Services Installed in This Lab

| Role Service | Category | Purpose |
| ----- | ----- | ----- |
| **Default Document** | Common HTTP Features | Automatically serves a designated landing page (e.g., `a.html`, `index.html`) when a user visits a root domain (`http://www.tech.com`) without specifying a filename. |
| **Static Content** | Common HTTP Features | Enables IIS to serve static web files such as `.html`, `.css`, `.js`, and images directly from disk (`C:\inetpub\site1`). |
| **HTTP Redirection** | Common HTTP Features | Allows administrators to redirect client requests from one URL or path to another destination URL (e.g., HTTP $\rightarrow$ HTTPS or old domain $\rightarrow$ new domain). |
| **FTP Server / FTP Service** | FTP Server | Enables File Transfer Protocol (Port `21`) so authorized web developers and admins can securely upload and manage website files on the server. |
| **IIS Management Console** | Management Tools | Installs the graphical **IIS Manager (`inetmgr`)** interface for configuring sites, bindings, application pools, and security settings. |

## Lab Environment Specifications

| Component | Details |
| ----- | ----- |
| **Server Hostname** | `SMEC-DC1` (`192.168.1.1`) — Windows Server 2025 |
| **Active Directory Domain** | `smec.com` |
| **New DNS Forward Lookup Zone** | `tech.com` (Standard Primary — `tech.com.dns`) |
| **DNS Host (`A`) Record** | `www.tech.com` $\rightarrow$ `192.168.1.1` |
| **IIS Role Services** | Web Server (IIS), **HTTP Redirection**, **FTP Server (FTP Service)**, IIS Management Console |
| **Website Physical Path** | `C:\inetpub\site1` |
| **Website HTML File** | `a.html` *(Created via New Text Document $\rightarrow$ Notepad $\rightarrow$ File $\rightarrow$ Save As)* |
| **IIS Site Binding** | `http` | IP: `192.168.1.1` | Port: `80` | Host name: `www.tech.com` |
| **Default Document** | `a.html` *(Added to top of Default Document list)* |
| **Live Test URL** | `http://www.tech.com` |

## Simulator Interface & Features

The simulator (`lab19.html`) replicates an end-to-end Windows Server 2025 desktop environment:

1. **Left Sidebar (Server Card & Live Progress Checklist):**
   * Displays live role status (`Roles: AD DS, DNS` $\rightarrow$ `Roles: AD DS, DNS, IIS + FTP`).
   * **Live Lab Progress Card:** Tracks all 7 milestones (`DNS Zone`, `Host A`, `IIS + Redirect + FTP`, `inetpub\site1\a.html`, `IIS Add Website`, `Default Doc`, and `Browser Live Test`) in real time.
   * Includes quick-reference lab parameters and a **Reset Lab** button.

2. **Main Workspace Stage:**
   * **DNS Manager (`dnsmgmt.msc`):** Complete **New Zone Wizard** (`tech.com`) and **New Host (`A`)** dialog (`www.tech.com`).
   * **Server Manager (`Add Roles and Features Wizard`):** 9-step wizard featuring the **Role Services** tree where students explicitly check **HTTP Redirection** and **FTP Server / FTP Service**.
   * **Authentic File Explorer + Notepad Workflow:** Right-click inside `C:\inetpub` $\rightarrow$ **New $\rightarrow$ Folder** (`site1`), right-click inside `site1` $\rightarrow$ **New $\rightarrow$ Text Document**, double-click `New Text Document.txt` to open **Notepad**, type content (`Welcome`), and click **File $\rightarrow$ Save As...** to save as `a.html` (automatically showing the Microsoft Edge HTML icon).
   * **IIS Manager (`inetmgr`):** Full three-pane layout (*Connections*, *Feature Tiles*, and *Actions*), **Add Website** binding dialog, and **Default Document** manager.
   * **Microsoft Edge Browser:** Renders the user's actual typed HTML/text content at `http://www.tech.com` and simulates real IIS errors (`403.14 Forbidden` if Default Document is missing, `NXDOMAIN` if DNS is missing).

3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed cleanly below the desktop stage so it never obscures Start menus, Explorer windows, or IIS dialogs.
   * Tracks progress from **Step 1 / 11** to **100% Complete**.

## Complete 11-Step Lab Walkthrough

### Phase 1: Configure DNS for `www.tech.com` (`dnsmgmt.msc`)

1. **Open DNS Manager:** Click **DNS Manager** on the Desktop (or **Start** $\rightarrow$ **Run** $\rightarrow$ `dnsmgmt.msc`).
2. **Create Primary Zone (`tech.com`):** Right-click **Forward Lookup Zones** $\rightarrow$ **New Zone...** $\rightarrow$ select **Primary zone** $\rightarrow$ enter Zone Name `tech.com` $\rightarrow$ keep `tech.com.dns` $\rightarrow$ click **Finish**.
3. **Add Host (`A`) Record:** Click **`tech.com`**, right-click it $\rightarrow$ **New Host (A or AAAA)...** $\rightarrow$ enter Name `www` (FQDN: `www.tech.com`) and IP address `192.168.1.1` $\rightarrow$ click **Add Host** $\rightarrow$ **Done**.

### Phase 2: Install IIS Web Server, HTTP Redirection & FTP Server

4. **Select Web Server (IIS) Role:** Open **Server Manager** $\rightarrow$ click **+ Add roles and features** $\rightarrow$ navigate to **Server Roles** $\rightarrow$ check **🌍 Web Server (IIS)** $\rightarrow$ click **Next**.
5. **Select Role Services & Install:** On the **Role Services** page:
   * Under *Common HTTP Features*, check **🔀 HTTP Redirection**.
   * Check **📂 FTP Server** (and **FTP Service**).
   * Click **Next** $\rightarrow$ **Install** $\rightarrow$ **Close** once installation succeeds (`C:\inetpub` is automatically created).

### Phase 3: Create Website Folder & HTML File (`File Explorer` + `Notepad`)

6. **Create `site1` Folder:** Open **File Explorer** $\rightarrow$ open **`inetpub`** (`C:\inetpub`) $\rightarrow$ **Right-click** empty white space $\rightarrow$ **New $\rightarrow$ Folder** $\rightarrow$ name it `site1`.
7. **Create `a.html` via Notepad:**
   * Open **`site1`** (`C:\inetpub\site1`).
   * **Right-click** empty space $\rightarrow$ **New $\rightarrow$ Text Document**.
   * Double-click `New Text Document.txt` to open **Notepad**, type `Welcome` (or custom HTML).
   * Click **File $\rightarrow$ Save As...**, enter `a.html`, and click **Save**.

### Phase 4: Host Website & Set Default Document in IIS Manager (`inetmgr`)

8. **Open IIS Manager:** Click **IIS Manager** on the Desktop (or **Server Manager $\rightarrow$ Tools $\rightarrow$ IIS Manager**, or `Start -> Run -> inetmgr`).
9. **Add Website:** Right-click **Sites** $\rightarrow$ **Add Website...** and configure:
   * **Site name:** `site1`
   * **Physical path:** `C:\inetpub\site1`
   * **Type / IP / Port:** `http` / `192.168.1.1` / `80`
   * **Host name:** `www.tech.com`
   Click **OK**.
10. **Configure Default Document:** Click **`site1`** under **Sites** $\rightarrow$ double-click **Default Document** $\rightarrow$ click **➕ Add...** in the right **Actions** pane $\rightarrow$ enter `a.html` $\rightarrow$ click **OK**.

### Phase 5: Verify Live Website in Microsoft Edge

11. **Browse Website:** Open **Microsoft Edge** on the Desktop, type `http://www.tech.com` in the address bar, and press **Enter** to view your hosted page and achieve **100% Lab Completion**.

## Key Technical Concepts & Best Practices

### Why Do You Get `HTTP Error 403.14 - Forbidden` Without a Default Document?

By default, IIS looks for standard filenames such as `Default.htm`, `index.htm`, `index.html`, or `iisstart.htm`. Because our page is named `a.html`, visiting `http://www.tech.com` without adding `a.html` to **Default Document** causes IIS to block directory listing with a **403.14 Forbidden** error. Adding `a.html` tells IIS to serve it automatically at the root URL.

### Host Header Name Resolution (`DNS` + `IIS Binding`)

Hosting a website by domain name requires **both** layers to match:
1. **DNS (`www.tech.com` $\rightarrow$ `192.168.1.1`):** Tells the browser which server IP to contact.
2. **IIS Binding (`Host name: www.tech.com` on Port `80`):** Tells IIS which specific website (`site1` vs. `Default Web Site`) should handle the incoming HTTP request.

## How to Run & Deploy

### Launch Live Online

Open the hosted simulator directly in your browser:

> **https://tibinjohn193-blip.github.io/az-802/lab19.html**

### Run Locally (Offline)

1. Download `lab19.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server 2025 UI styling, SVG Microsoft Edge icon, live progress badges)
* **Vanilla JavaScript** (11-step validation engine, DNS New Zone/Host wizards, IIS Role Services installer, authentic File Explorer context menus + Notepad `Save As` workflow, IIS Manager, and simulated Edge browser)