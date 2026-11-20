# SMEC Hybrid Identity Lab — Windows Server 2025 to Microsoft Entra ID Simulator

An interactive, single-file web-based **Windows Server 2025 & Microsoft Azure (Entra ID) Simulator** designed for hands-on IT administration training. This sandbox guides learners step-by-step through preparing an on-premises Active Directory domain (`smec.com`) and synchronizing it to **Microsoft Entra ID** using **Microsoft Entra Connect**, with zero skipped steps and strict manual progression.

## Table of Contents

1. [Overview & Learning Objectives](#overview--learning-objectives)
2. [Lab Environment Specifications](#lab-environment-specifications)
3. [Lab Credentials](#lab-credentials)
4. [Simulated Applications & Features](#simulated-applications--features)
5. [Complete 14-Step Lab Walkthrough](#complete-14-step-lab-walkthrough)
6. [Key Technical Concepts & Glossary](#key-technical-concepts--glossary)
   - [What is a UPN, UPN Prefix, and UPN Suffix?](#what-is-a-upn-upn-prefix-and-upn-suffix)
   - [What is Microsoft IdFix?](#what-is-microsoft-idfix)
   - [Why Check Port 443 & TLS 1.2?](#why-check-port-443--tls-12)
   - [What is Microsoft Entra Connect?](#what-is-microsoft-entra-connect)
   - [What is Password Hash Synchronization (PHS)?](#what-is-password-hash-synchronization-phs)
   - [Do You Need to Purchase a Custom Domain?](#do-you-need-to-purchase-a-custom-domain)
7. [How to Run & Deploy](#how-to-run--deploy)
8. [Technology Stack](#technology-stack)

---

## Overview & Learning Objectives

This simulator mirrors real-world enterprise Hybrid Identity deployment workflows rather than relying on shortcuts or automated popups. Learners must manually launch tools, type commands, inspect properties, and validate configurations.

By completing this lab, students will learn how to:

* Inspect Organizational Units (OUs) and validate **UPN Prefixes and UPN Suffixes** in Active Directory Users and Computers (`dsa.msc`).
* Download and run **Microsoft IdFix** to detect and remediate directory synchronization errors (such as invalid spaces in UPNs) before cloud migration.
* Verify outbound **Port 443 (HTTPS)** connectivity and **TLS 1.2** compliance using **Windows PowerShell**.
* Navigate the **Microsoft Azure Portal**, search for **Microsoft Entra ID**, verify the Azure Tenant, and verify a custom domain (`smec.com`).
* Download, configure, and run **Microsoft Entra Connect** for a **Full Domain Sync** with **Password Hash Synchronization (PHS)**.
* Confirm synchronized identities and directory sync health inside Microsoft Entra ID.

---

## Lab Environment Specifications

| Component | Details |
| :--- | :--- |
| **Server OS** | Windows Server 2025 Standard |
| **Domain Controller** | `WIN-DC01.smec.com` |
| **Server IP Address** | `192.168.10.10` |
| **On-Premises Forest / Domain** | `smec.com` |
| **Sync Scope** | Full Domain (`OU=Tech`, `OU=HR`, `CN=Users`) |
| **Target Cloud Directory** | Microsoft Entra ID (`smec.onmicrosoft.com`) |
| **Azure Tenant ID** | `9b23f81e-43a0-496a-smec` |
| **Custom Domain DNS TXT Record** | `MS=ms92847102` (for `smec.com`) |

### On-Premises Active Directory Accounts

| Display Name | OU / Container | Role / Description | Initial UPN | Post-IdFix UPN |
| :--- | :--- | :--- | :--- | :--- |
| **User1** | `OU=Tech` | Infrastructure Engineer | `user1@smec.com` | `user1@smec.com` |
| **User2** | `OU=Tech` | Cloud & DevOps Specialist | `user2@smec.com` | `user2@smec.com` |
| **HRUser** | `OU=HR` | HR Coordinator | `hr user@smec.com` *(space error)* | `hruser@smec.com` *(remediated)* |
| **Administrator** | `CN=Users` | Built-in Domain Admin | `Administrator@smec.com` | `Administrator@smec.com` |

---

## Lab Credentials

All login prompts inside the Azure Portal and the Entra Connect Wizard strictly validate these credentials:

| Service / Role | Username | Password |
| :--- | :--- | :--- |
| **Azure / Entra Global Administrator** | `tibinjohn193@gmail.com` | `password123` |
| **AD DS Enterprise Administrator** | `SMEC\Administrator` | `password123` |

---

## Simulated Applications & Features

The simulator uses a **3-column layout**:

1. **Left Panel (Virtual Lab Controller):** Displays server specs, one-click copyable credentials, quick-reference educational notes (UPN Prefix/Suffix & Microsoft IdFix), and quick-launch buttons.
2. **Center Panel (Windows Server 2025 Desktop):** Draggable, resizable windows, taskbar, Start Menu, and desktop icons for:
   * **Run Dialog:** Supports `dsa.msc` and `powershell`.
   * **Active Directory Users and Computers (`dsa.msc`):** Expandable domain tree, OU object viewer, and authentic **User Properties** dialog (`General` and `Account` tabs).
   * **Microsoft Edge:** Includes bookmarks for the official **Microsoft IdFix GitHub Release page** and **Microsoft Azure Portal**, a downloads flyout, Azure Home dashboard, and a working **Azure Global Search Bar**.
   * **Microsoft IdFix (v2.5):** Interactive `Query` and `Apply` workflow showing error detection and remediation.
   * **Windows PowerShell:** Interactive CLI requiring manual entry of network and TLS verification commands.
   * **Server Manager:** Live status dashboard tracking IdFix, Network/TLS, and Entra Connect states.
   * **Microsoft Entra Connect Setup Wizard:** Complete 9-screen wizard with strict field and checkbox validation.
3. **Right Panel (14-Step Interactive Lab Guide):** Slim sidebar (`w-72`) with real-time progress bar, step-by-step instructions, copyable PowerShell commands, and completion tracking.

---

## Complete 14-Step Lab Walkthrough

### Phase 1: On-Premises Pre-Checks

1. **Local AD (`dsa.msc`) & UPN Check:**
   * Open **Run** and type `dsa.msc` (or click **AD Users & Computers**).
   * Expand `smec.com` and select the **`Tech`** OU.
   * Double-click **`User1`** to open **User1 Properties** and inspect the **Account** tab to confirm the UPN Prefix (`user1`) has no spaces and the UPN Suffix (`@smec.com`) is routable. Click **OK** and close ADUC.

2. **Microsoft IdFix Tool Check:**
   * Open **Microsoft Edge** (defaults to `https://github.com/microsoft/idfix/releases`).
   * Click **Download IdFix.exe**, then run it from the Downloads flyout or desktop.
   * Click **Query** to scan `smec.com`. IdFix detects a `character` error on `HRUser` (`hr user@smec.com`).
   * Click **Apply** to remediate the UPN to `hruser@smec.com` and confirm `0 Errors (Clean)`.

3. **Network (Port 443) & TLS 1.2 Check:**
   * Open **Windows PowerShell** (from desktop, Start Menu, or Run -> `powershell`).
   * Manually type and execute both prerequisite commands:
     ```powershell
     Test-NetConnection login.microsoftonline.com -Port 443
     ```
     ```powershell
     [Net.ServicePointManager]::SecurityProtocol
     ```
   * Verify `TcpTestSucceeded : True` and `Tls12` are returned.

### Phase 2: Azure Portal & Domain Verification

4. **Azure Login & Search Entra ID:**
   * In Microsoft Edge, click the **Microsoft Azure Portal** bookmark (`https://portal.azure.com`).
   * Sign in with `tibinjohn193@gmail.com` / `password123`.
   * From the Azure Home Dashboard, type **`Entra`** into the top Azure Global Search Bar and open **Microsoft Entra ID**.

5. **Azure Tenant Verification:**
   * In **Microsoft Entra ID | Overview**, verify the Tenant ID (`9b23f81e-43a0-496a-smec`) and primary domain (`smec.onmicrosoft.com`) by clicking **Verify Tenant**.

6. **`smec.com` Custom Domain Verification:**
   * Navigate to **Custom domain names** in the Entra ID sidebar.
   * Click **Verify Domain** next to `smec.com` (simulating DNS TXT record validation) to change its status to **Verified**.

### Phase 3: Microsoft Entra Connect Configuration

7. **Entra Connect Download & Express Settings Info:**
   * Return to **Overview & Sync** and click **Download Microsoft Entra Connect**.
   * Launch `AzureADConnect.msi`, accept the license terms, and review the **Express Settings Overview** screen.

8. **Full Domain Sync (`smec.com`):**
   * Authenticate with **Entra ID Global Admin** (`tibinjohn193@gmail.com` / `password123`).
   * Authenticate with **AD DS Enterprise Admin** (`SMEC\Administrator` / `password123`).
   * On **Domain and OU Filtering**, select **Sync all domains and OUs (Full Domain: smec.com)**.

9. **Password Hash Synchronization (PHS):**
   * Check **Enable Password Hash Synchronization (PHS)** and click **Next**.

10. **Initial Sync Checkbox Validation:**
    * Check **Start the synchronization process when configuration completes** and click **Next**.

11. **Synchronization Configuration Summary:**
    * Review the summary confirming Pre-Checks (`IdFix Clean + Port 443 / TLS 1.2 OK`), Source Forest (`smec.com`), Full Domain Sync Scope, Verified Custom Domain, PHS, and Initial Sync. Click **Install**.

12. **Initial Synchronization:**
    * Monitor the live synchronization logs as `User1`, `User2`, and the remediated `HRUser` are imported and exported to Entra ID. Click **Exit** when complete.

### Phase 4: Cloud Verification

13. **Azure Portal: Users Verification:**
    * Manually return to **Microsoft Edge** and navigate to **Users > All Users**.
    * Confirm `User1` (`user1@smec.com`), `User2` (`user2@smec.com`), and `HRUser` (`hruser@smec.com`) appear with **On-Premises Sync = Yes**.

14. **Sync Status & Last Sync Check:**
    * Navigate to **Overview & Sync** in Microsoft Entra ID.
    * Confirm Directory Sync Status is **Enabled (Healthy)**, PHS is **Enabled**, and Last Directory Sync is **Less than 1 minute ago**, then click **Verify Sync Status & Last Sync** to achieve **100% Lab Completion**.

---

## Key Technical Concepts & Glossary

### What is a UPN, UPN Prefix, and UPN Suffix?

A **UPN (User Principal Name)** is the email-style login name used to sign in to Active Directory and Microsoft Entra ID (Azure / Microsoft 365).

For example, in `user1@smec.com`:
* **UPN Prefix (`user1`):** The username portion before the `@` symbol. It must not contain spaces or invalid characters.
* **UPN Suffix (`@smec.com`):** The domain portion after the `@` symbol.

#### Default vs. Alternative UPN Suffix
* **Default UPN Suffix:** Automatically matches your internal Active Directory domain name (e.g., `smec.com` or `company.local`).
* **Alternative UPN Suffix:** If your internal AD uses a non-routable name like `company.local`, Azure cannot route `.local` domains and will convert synced logins to `user@tenant.onmicrosoft.com`. To fix this in real environments, administrators open **Active Directory Domains and Trusts (`domain.msc`)**, add their verified public domain (e.g., `smec.com`) as an **Alternative UPN Suffix**, and assign it to users in ADUC (**User Properties -> Account tab**).

---

### What is Microsoft IdFix?

**Microsoft IdFix** (officially downloaded from `https://github.com/microsoft/idfix/releases`) is a free directory synchronization error remediation tool provided by Microsoft.

#### Why do we use IdFix before Cloud Sync?
If on-premises Active Directory contains formatting errors, **Microsoft Entra Connect** will either fail to sync those users or skip them completely. Running IdFix beforehand scans attributes like `userPrincipalName`, `mail`, and `proxyAddresses` to detect and fix:
* **Invalid Characters & Spaces:** e.g., `hr user@smec.com` (remediated to `hruser@smec.com` in this lab).
* **Duplicate Values:** Two users accidentally sharing the same UPN or email address.
* **Format & Top-Level Domain Errors:** Non-routable domains like `.local` or `.corp`.

---

### Why Check Port 443 & TLS 1.2?

Before installing Microsoft Entra Connect on a Windows Server, two network prerequisites must be verified via PowerShell:
* **Outbound TCP Port 443 (HTTPS):** Checked using `Test-NetConnection login.microsoftonline.com -Port 443`. Entra Connect requires outbound HTTPS access to communicate with Azure authentication and sync endpoints.
* **TLS 1.2 Protocol:** Checked using `[Net.ServicePointManager]::SecurityProtocol`. Microsoft Entra ID strictly enforces TLS 1.2 (or higher) for all synchronization traffic; older protocols like TLS 1.0/1.1 are rejected.

---

### What is Microsoft Entra Connect?

**Microsoft Entra Connect** (formerly *Azure AD Connect*) is the Microsoft sync agent installed on an on-premises Windows Server to bridge local **Active Directory Domain Services (AD DS)** with **Microsoft Entra ID**.
* **Express Settings:** The quickest setup option—automatically syncs the full forest/domain, enables Password Hash Synchronization, and installs SQL Server Express LocalDB.
* **Full Domain Sync vs. OU Filtering:** You can either sync the **entire domain (`smec.com`)** so all departments (`Tech`, `HR`, `Users`) are mirrored in the cloud, or use **OU Filtering** to sync only specific Organizational Units.

---

### What is Password Hash Synchronization (PHS)?

**Password Hash Synchronization (PHS)** is the most common hybrid sign-in method in Entra Connect.
* It takes the already-hashed password from on-premises Active Directory, hashes it again using a strong cryptographic algorithm, and syncs that **hash of a hash** to Microsoft Entra ID.
* Plaintext passwords are **never** sent to or stored in Azure.
* Users can sign in to both their local Windows computers and cloud apps (Azure Portal, Microsoft 365, Teams) using the exact same username and password.

---

### Do You Need to Purchase a Custom Domain?

* **No, it is not mandatory for lab practice or basic syncing.** If you do not own a public domain, Entra Connect will still sync your local AD users to Azure, and their cloud sign-in will automatically default to `@<tenant>.onmicrosoft.com` (e.g., `user1@smec.onmicrosoft.com`).
* **When is it required?** In live production companies, organizations purchase a public domain (from registrars like GoDaddy or Cloudflare) and verify it in Entra ID via a DNS `TXT` record so employees can sign in with their branded corporate domain (`user1@smec.com`) everywhere.

---

## How to Run & Deploy

### Run Locally

1. Download `smec_hybrid_identity_lab_v5.html`.
2. Double-click the file to open it directly in any modern web browser (Chrome, Edge, Firefox, or Safari).
3. Ensure an active internet connection is available to load Tailwind CSS and Font Awesome icons from their CDNs.

### Deploy on GitHub Pages

1. Upload `smec_hybrid_identity_lab_v5.html` (or rename it to `index.html`) to a GitHub repository.
2. Go to **Repository Settings > Pages**.
3. Under **Build and deployment**, select **Deploy from a branch** (`main` / `root`) and click **Save**.
4. Share the generated live URL (`https://<username>.github.io/<repo-name>/`) with students.

---

## Technology Stack

* **HTML5** (Single-file architecture — no build step required)
* **Tailwind CSS** (via CDN)
* **Font Awesome 6** (via CDN)
* **Vanilla JavaScript** (State engine, draggable window manager, CLI parser, and strict step validation)