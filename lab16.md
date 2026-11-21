# Primary & Secondary DNS with Zone Transfer Lab (`tech.com`) — Windows Server 2025 Simulator

<div align="center">

### 🌐 Windows Server DNS Administration — Interactive Lab (`lab16.html`)

[![Click to Launch Interactive Lab](https://img.shields.io/badge/▶_CLICK_TO_START-INTERACTIVE_LAB_(lab16.html)-0078D7?style=for-the-badge&logo=windows&logoColor=white)](https://tibinjohn193-blip.github.io/az-802/lab16.html)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab16.html>

</div>

An interactive, single-file web-based **Windows Server 2025 Dual-Server Simulator** designed for hands-on **Domain Name System (DNS)** administration training. This lab walks students step-by-step through creating a **Primary Forward Lookup Zone (`tech.com`)**, adding a **Host (`A`) record (`www.tech.com`)**, testing resolution with `ping`, configuring **Alternate DNS** and **Zone Transfer** security restrictions (`Only to the following servers`), installing the **DNS Server** role on a second server (`192.168.1.22`), creating a **Secondary Zone**, executing a **Zone Transfer from Master**, and verifying name resolution on both servers.

---

## Table of Contents

1. [What is DNS?](#what-is-dns)
2. [Types of DNS](#types-of-dns)
   - [Types of DNS Zones](#1-types-of-dns-zones)
   - [Types of Lookup Zones](#2-types-of-lookup-zones)
   - [Common DNS Record Types](#3-common-dns-record-types)
3. [Lab Environment Specifications](#lab-environment-specifications)
4. [Simulator Interface & Features](#simulator-interface--features)
5. [Complete 13-Step Lab Walkthrough](#complete-13-step-lab-walkthrough)
6. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
7. [How to Run & Deploy](#how-to-run--deploy)
8. [Technology Stack](#technology-stack)

---

## What is DNS?

**DNS (Domain Name System)** is the hierarchical naming system that translates human-readable domain names (such as `www.tech.com` or `smec.com`) into machine-readable **IP addresses** (such as `192.168.1.1`). Often called the **"phonebook of the network,"** DNS eliminates the need for users and applications to memorize numeric IP addresses.

### Why is DNS Essential in Windows Server & Active Directory?

* **Name Resolution:** Resolves hostnames to IPv4/IPv6 addresses for web servers, file shares, and internal applications.
* **Active Directory Dependency:** Active Directory relies heavily on DNS (`SRV` records) so domain-joined computers and users can locate Domain Controllers for authentication and Group Policy processing.
* **Standard Ports:** DNS uses **UDP Port 53** for standard name resolution queries and **TCP Port 53** for **Zone Transfers** and large DNS responses.

---

## Types of DNS

In Windows Server DNS administration, DNS is categorized by **Zone Types**, **Lookup Directions**, and **Resource Record Types**.

### 1. Types of DNS Zones

| Zone Type | Storage & Editability | Purpose & Behavior |
| :--- | :--- | :--- |
| **Primary Zone** *(Configured on Server 1)* | **Read / Write** (Stored in a local `.dns` text file, e.g., `tech.com.dns`) | The authoritative master copy of a DNS zone where all records (`A`, `MX`, `CNAME`, etc.) are created, modified, and deleted. |
| **Secondary Zone** *(Configured on Server 2)* | **Read-Only** (Replicated copy from a Master/Primary DNS server) | Provides **fault tolerance** and **load balancing**. Cannot be edited directly; receives updates automatically via **Zone Transfers** from the Primary DNS server. |
| **Stub Zone** | **Read-Only** (Stores only `SOA`, `NS`, and glue `A` records) | Keeps a server aware of the authoritative Name Servers for another zone without copying all host records, improving name resolution across large networks. |
| **Active Directory-Integrated Zone** | **Multi-Master Read / Write** (Stored inside the AD database) | Replicates DNS records automatically through Active Directory replication across all Domain Controllers instead of using traditional file-based zone transfers, and supports **Secure Dynamic Updates**. |

### 2. Types of Lookup Zones

* **Forward Lookup Zone:** Resolves a **Hostname / Domain Name $\rightarrow$ IP Address** (e.g., `www.tech.com` $\rightarrow$ `192.168.1.1`). Used in this lab.
* **Reverse Lookup Zone:** Resolves an **IP Address $\rightarrow$ Hostname / FQDN** using `PTR` records (e.g., `192.168.1.1` $\rightarrow$ `www.tech.com`), commonly used for troubleshooting (`nslookup`) and email spam verification.

### 3. Common DNS Record Types

| Record Type | Full Name | Function |
| :--- | :--- | :--- |
| **`A`** | Host (IPv4) | Maps a hostname to a 32-bit IPv4 address (e.g., `www.tech.com` $\rightarrow$ `192.168.1.1`). |
| **`AAAA`** | Host (IPv6) | Maps a hostname to a 128-bit IPv6 address. |
| **`CNAME`** | Canonical Name | Creates an alias pointing one domain name to another domain name. |
| **`NS`** | Name Server | Identifies the authoritative DNS servers for the zone. |
| **`SOA`** | Start of Authority | Stores administrative info about the zone (Primary server, serial number, refresh/retry timers). |
| **`MX`** | Mail Exchanger | Directs email traffic to the domain's mail servers. |
| **`PTR`** | Pointer | Maps an IP address back to a hostname in a Reverse Lookup Zone. |
| **`SRV`** | Service Locator | Identifies servers hosting specific services (such as Active Directory LDAP and Kerberos). |

---

## Lab Environment Specifications

| Component | Server 1 (Primary DNS) | Server 2 (Secondary DNS) |
| :--- | :--- | :--- |
| **Hostname** | `SMEC-DC1` | `SMEC-DNS2` |
| **Operating System** | Windows Server 2025 | Windows Server 2025 |
| **IP Address** | `192.168.1.1` | `192.168.1.22` *(Configured in Lab)* |
| **Subnet Mask** | `255.255.255.0` | `255.255.255.0` *(Configured in Lab)* |
| **Preferred DNS Server** | `192.168.1.1` | `192.168.1.1` *(Configured in Lab)* |
| **Alternate DNS Server** | `192.168.1.22` *(Configured in Lab)* | `192.168.1.22` *(Configured in Lab)* |
| **Existing Domain** | `smec.com` | Standalone / Member Server |
| **Lab DNS Zone** | `tech.com` (**Primary** — `tech.com.dns`) | `tech.com` (**Secondary** — Transferred from `.1`) |
| **Host (`A`) Record** | `www.tech.com` $\rightarrow$ `192.168.1.1` | Replicated via Zone Transfer |

---

## Simulator Interface & Features

The simulator (`lab16.html`) uses a full-screen **Dual-Server Switcher** layout:

1. **Left Sidebar (Server Switcher & Live Progress Tracker):**
   * Switch cleanly between **🖥️ Server 1 (Primary — `SMEC-DC1`)** and **🖥️ Server 2 (Secondary — `SMEC-DNS2`)**.
   * **Live Lab Progress Card:** Tracks all 6 milestones (`Primary Zone`, `Host A`, `S1 Alt DNS`, `Allow Zone Transfer`, `S2 IP & DNS Role`, and `Secondary Zone Sync`) in real time.
   * **Attention Pulse Highlight:** Automatically pulses a yellow border around Server 2 when it is time to switch servers.
2. **Main Workspace Stage:**
   * **Authentic DNS Manager (`dnsmgmt.msc`):** Includes full New Zone Wizards (Primary and Secondary), New Host (`A`) dialog with live FQDN preview, and Zone Properties with the **Zone Transfers** tab and **Allow Zone Transfers (`Edit...`)** IP list modal.
   * **Manual IPv4 Configuration (`ncpa.cpl`):** No auto-filled IP fields — students manually configure Alternate DNS on Server 1 and full IP/DNS settings on Server 2.
   * **Server Manager Role Installation:** Full 8-step **Add Roles and Features Wizard** to install the **DNS Server** role on Server 2.
   * **Interactive Command Prompt (`cmd`):** Supports `ping www.tech.com` and `nslookup www.tech.com` on both servers.
3. **Dedicated Bottom Lab Guide Bar:**
   * Sits cleanly below the desktop stage so it never obscures Start menus, wizards, or Command Prompt windows.
   * Tracks progress from **Step 1 / 13** to **100% Complete**.

---

## Complete 13-Step Lab Walkthrough

### Phase 1: Configure Primary DNS Zone & Host Record (`SMEC-DC1` — `192.168.1.1`)

1. **Open DNS Manager:** On **Server 1**, click **DNS Manager** on the Desktop (or **Start** $\rightarrow$ **Run** $\rightarrow$ `dnsmgmt.msc`).
2. **Create Primary Zone (`tech.com`):** Right-click **Forward Lookup Zones** $\rightarrow$ **New Zone...** $\rightarrow$ select **Primary zone** $\rightarrow$ enter Zone Name `tech.com` $\rightarrow$ keep zone file `tech.com.dns` $\rightarrow$ click **Finish**.
3. **Add Host (`A`) Record:** Click **`tech.com`**, right-click it $\rightarrow$ **New Host (A or AAAA)...** $\rightarrow$ enter Name `www` (FQDN: `www.tech.com`) and IP address `192.168.1.1` $\rightarrow$ click **Add Host** $\rightarrow$ **Done**.
4. **Verify Primary DNS with Ping:** Open **Command Prompt** (`Start -> Run -> cmd`) and run:
   ```cmd
   ping www.tech.com
   ```
   Confirm ping replies from `192.168.1.1`.

### Phase 2: Configure Alternate DNS & Allow Zone Transfers (`SMEC-DC1`)

5. **Set Alternate DNS on Server 1:** Open **Start** $\rightarrow$ **Run** $\rightarrow$ `ncpa.cpl` $\rightarrow$ right-click **Ethernet** $\rightarrow$ **Properties** $\rightarrow$ **Internet Protocol Version 4 (TCP/IPv4)** $\rightarrow$ set **Alternate DNS server** to `192.168.1.22` $\rightarrow$ click **OK**.
6. **Allow Zone Transfers to Server 2:**
   * Click **Start** $\rightarrow$ **Run**, type `dnsmgmt.msc`, and click **OK**.
   * Right-click **`tech.com`** $\rightarrow$ **Properties** $\rightarrow$ switch to the **Zone Transfers** tab.
   * Check **Allow zone transfers**, select **Only to the following servers**, click **Edit...**, enter `192.168.1.22`, click **Add** $\rightarrow$ **OK**, then click **Apply** and **OK**.

### Phase 3: Configure Network & Install DNS Role (`SMEC-DNS2` — `192.168.1.22`)

7. **Switch to Server 2 & Open Network Connections:** Click **🖥️ Server 2 (Secondary)** in the left sidebar. Open **Start** $\rightarrow$ **Run** $\rightarrow$ `ncpa.cpl` $\rightarrow$ right-click **Ethernet** $\rightarrow$ **Properties** $\rightarrow$ **IPv4**.
8. **Enter Static IP & DNS:** Manually configure:
   * **IP address:** `192.168.1.22`
   * **Subnet mask:** `255.255.255.0`
   * **Preferred DNS server:** `192.168.1.1`
   * **Alternate DNS server:** `192.168.1.22`
   Click **OK** to save.
9. **Install DNS Server Role:** Open **Server Manager** $\rightarrow$ **+ Add roles and features** $\rightarrow$ navigate to **Server Roles**, check **🌐 DNS Server** $\rightarrow$ proceed to Confirmation $\rightarrow$ click **Install** $\rightarrow$ **Close**.

### Phase 4: Create Secondary Zone, Transfer Records & Verify (`SMEC-DNS2`)

10. **Open DNS Manager on Server 2:** Click **DNS Manager** on the Desktop (or `Start -> Run -> dnsmgmt.msc`), then right-click **Forward Lookup Zones** $\rightarrow$ **New Zone...**.
11. **Create Secondary Zone:** Select **Secondary zone** $\rightarrow$ enter Zone Name `tech.com` $\rightarrow$ enter Master DNS Server IP `192.168.1.1` $\rightarrow$ click **Finish**.
12. **Perform Zone Transfer:** Click **`tech.com`** (initially shows *Zone Not Loaded*), then right-click **`tech.com`** $\rightarrow$ **Transfer from Master**. Confirm `www.tech.com` (`192.168.1.1`) replicates from Server 1.
13. **Final Ping Verification:** Open **Command Prompt** (`cmd`) on Server 2 and run:
    ```cmd
    ping www.tech.com
    ```
    Confirm successful name resolution and ping replies to achieve **100% Lab Completion**.

---

## Key Technical Concepts & Best Practices

### Why Restrict Zone Transfers to Specific Servers?

Allowing zone transfers **"To any server"** is a major security risk because an attacker can request a full copy of your internal DNS zone (`AXFR`), exposing every internal server name and IP address on your network. Selecting **"Only to the following servers"** and explicitly listing `192.168.1.22` ensures only your authorized Secondary DNS server can pull zone data.

### Full (`AXFR`) vs. Incremental (`IXFR`) Zone Transfers

* **Full Zone Transfer (`AXFR`):** Transfers the entire zone file when a Secondary DNS server is first configured.
* **Incremental Zone Transfer (`IXFR`):** Transfers only the records that have changed since the last SOA serial number update, saving network bandwidth.

---

## How to Run & Deploy

### Launch Live Online

Open the hosted simulator directly in your browser:

> **https://tibinjohn193-blip.github.io/az-802/lab16.html**

### Run Locally (Offline)

1. Download `lab16.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

---

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server 2025 UI styling, live progress checklist badges)
* **Vanilla JavaScript** (13-step validation engine, Primary/Secondary New Zone Wizards, Zone Transfer IP allowlist modal, Server Manager role installer, and simulated `ping` / `nslookup` CMD parser)