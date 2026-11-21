# DNS Stub Zone Setup Lab (`tech.com`) — Windows Server 2025 Simulator

[![Launch Interactive Lab](https://img.shields.io/badge/Click_to_Launch-Lab_18:_DNS_Stub_Zone-4f46e5?style=for-the-badge&logo=windows&logoColor=white)](https://tibinjohn193-blip.github.io/az-802/lab18.html)

### 📌 Lightweight DNS Name Server Tracking — Interactive Stub Zone Lab (`lab18.html`)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab18.html>

An interactive, single-file web-based **Windows Server 2025 Dual-Server Simulator** designed for hands-on Domain Name System (DNS) administration training. This lab walks students step-by-step through verifying an existing Primary DNS Zone (`tech.com`) on an Active Directory server (`SMEC-DC1`), configuring restricted Zone Transfers and Alternate DNS, setting up IPv4 and installing the **DNS Server** role on a second server (`SMEC-DNS2`), creating and loading a **Stub Zone**, observing that only **SOA** and **NS** records are replicated (no Host `A` records), and verifying Stub DNS query referrals with `ping` and `nslookup`.

## Table of Contents

1. [What is a DNS Stub Zone (Stub DNS)?](#what-is-a-dns-stub-zone-stub-dns)

2. [Primary Zone vs. Secondary Zone vs. Stub Zone](#primary-zone-vs-secondary-zone-vs-stub-zone)

3. [Lab Environment Specifications](#lab-environment-specifications)

4. [Simulator Interface & Features](#simulator-interface--features)

5. [Complete 11-Step Lab Walkthrough](#complete-11-step-lab-walkthrough)

6. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)

7. [How to Run & Deploy](#how-to-run--deploy)

8. [Technology Stack](#technology-stack)

## What is a DNS Stub Zone (Stub DNS)?

A **Stub Zone** is a lightweight, **read-only** copy of a DNS zone that contains **only the records required to identify the authoritative DNS servers** for that zone. Unlike a Secondary Zone, a Stub Zone does **not** copy regular host records (such as `www`, `mail`, or `ftp`).

### What Records Are Stored in a Stub Zone?

* **Start of Authority (`SOA`):** Contains administrative information and refresh timers for the zone.

* **Name Server (`NS`):** Lists the authoritative DNS servers responsible for the zone (e.g., `smec-dc1.smec.com.`).

*(Note: Full host records like `www.tech.com` remain exclusively on the Primary DNS server `192.168.1.1`.)*

### Why Use a Stub Zone?

* **Automatic Name Server Updates:** If the authoritative DNS servers for `tech.com` ever change, the Stub Zone automatically updates its `NS` list from the Master server without manual intervention (unlike static Conditional Forwarders).

* **Low Bandwidth & Storage Footprint:** Because it does not replicate hundreds or thousands of Host (`A`) records, Stub Zones consume minimal network bandwidth and disk space.

* **Efficient Cross-Zone Resolution:** When a client asks `SMEC-DNS2` (`192.168.1.22`) for `www.tech.com`, `SMEC-DNS2` checks its Stub Zone `NS` record and refers/forwards the query directly to `SMEC-DC1` (`192.168.1.1`) to retrieve the IP address.

## Primary Zone vs. Secondary Zone vs. Stub Zone

| Feature | Primary Zone (`SMEC-DC1`) | Secondary Zone | Stub Zone (`SMEC-DNS2`) |
| ----- | ----- | ----- | ----- |
| **Editability** | **Read / Write** | Read-Only | **Read-Only** |
| **Records Stored** | All zone records (`SOA`, `NS`, `A`, `MX`, `CNAME`, etc.) | Full copy of all zone records | **Only `SOA` & `NS` records** (No Host `A` records) |
| **Fault Tolerance if Primary Fails** | Authoritative source | Continues resolving cached host records | **Cannot** resolve host records if Primary is unreachable |
| **Bandwidth Usage** | N/A | Higher (transfers full zone) | **Minimal** (transfers only authoritative server records) |
| **Best Use Case** | Master DNS zone management | High availability & load balancing | Keeping NS lists synchronized across networks/mergers |

## Lab Environment Specifications

| Component | Server 1 (AD DC & Primary DNS) | Server 2 (Target Stub DNS) |
| ----- | ----- | ----- |
| **Hostname** | `SMEC-DC1` | `SMEC-DNS2` |
| **Operating System** | Windows Server 2025 | Windows Server 2025 |
| **IP Address** | `192.168.1.1` | `192.168.1.22` *(Configured in Lab)* |
| **Subnet Mask** | `255.255.255.0` | `255.255.255.0` *(Configured in Lab)* |
| **Preferred DNS Server** | `192.168.1.1` | `192.168.1.1` *(Configured in Lab)* |
| **Alternate DNS Server** | `192.168.1.22` *(Configured in Lab)* | `192.168.1.22` *(Configured in Lab)* |
| **Active Directory Domain** | `smec.com` | Standalone / DNS Server |
| **Lab DNS Zone** | `tech.com` (**Primary** — contains `www` $\rightarrow$ `192.168.1.1`) | `tech.com` (**Stub** — `SOA` & `NS` only) |

## Simulator Interface & Features

The simulator (`lab18.html`) uses a full-screen **Dual-Server Switcher** layout:

1. **Left Sidebar (Server Switcher & Live Checklist):**

   * Switch cleanly between **🖥️ Server 1 (AD + Primary — `SMEC-DC1`)** and **📌 Server 2 (Stub DNS — `SMEC-DNS2`)**.

   * **Live Lab Progress Card:** Tracks all 6 milestones (`Verify Primary`, `Allow Transfer`, `S1 Alt DNS`, `S2 IP & DNS Role`, `Create Stub Zone`, and `Test Query`) in real time.

   * **Attention Pulse Highlight:** Automatically pulses a yellow border around Server 2 when it is time to switch servers.

   * Includes a **"What is a Stub Zone?"** quick-reference card and a **Reset Lab** button.

2. **Main Workspace Stage:**

   * **Authentic DNS Manager (`dnsmgmt.msc`):** Includes Zone Transfers allowlist configuration (`Only to the following servers` $\rightarrow$ `Edit...`), **New Zone Wizard (Stub Zone)**, and **Transfer from Master** loading strictly `SOA` and `NS` records.

   * **Manual IPv4 Configuration (`ncpa.cpl`):** Students manually configure Alternate DNS on Server 1 and full IPv4/DNS settings on Server 2.

   * **Server Manager Role Installer:** Complete 8-step **Add Roles and Features Wizard** to install **DNS Server** on Server 2.

   * **Interactive Command Prompt (`cmd`):** Simulates Stub Zone referral to `192.168.1.1` when running `ping www.tech.com` or `nslookup www.tech.com`.

3. **Dedicated Bottom Lab Guide Bar:**

   * Fixed cleanly below the desktop stage so it never overlaps Start menus, wizards, or Command Prompt windows.

   * Tracks progress from **Step 1 / 11** to **100% Complete**.

## Complete 11-Step Lab Walkthrough

### Phase 1: Verify Primary Zone, Allow Transfers & Set Alternate DNS (`SMEC-DC1` — `192.168.1.1`)

1. **Open DNS Manager:** On **Server 1**, open **DNS Manager** (Desktop icon, **Server Manager** $\rightarrow$ **Tools** $\rightarrow$ **DNS**, or `Start` $\rightarrow$ `Run` $\rightarrow$ `dnsmgmt.msc`).

2. **Verify Primary Zone (`tech.com`):** Click **`tech.com (Primary)`** under **Forward Lookup Zones** to confirm the full Primary Zone contains `SOA`, `NS`, and Host (`A`) record `www.tech.com` (`192.168.1.1`).

3. **Allow Zone Transfers to Server 2:**

   * Right-click **`tech.com (Primary)`** $\rightarrow$ **Properties** $\rightarrow$ **Zone Transfers** tab.

   * Check **Allow zone transfers**, select **Only to the following servers**, click **Edit...**, add `192.168.1.22`, click **OK**, then click **Apply** and **OK**.

4. **Set Alternate DNS on Server 1:** Open **Start** $\rightarrow$ **Run** $\rightarrow$ `ncpa.cpl` $\rightarrow$ right-click **Ethernet** $\rightarrow$ **Properties** $\rightarrow$ **Internet Protocol Version 4 (TCP/IPv4)** $\rightarrow$ set **Alternate DNS server** to `192.168.1.22` $\rightarrow$ click **OK**.

### Phase 2: Configure Network & Install DNS Role (`SMEC-DNS2` — `192.168.1.22`)

5. **Switch to Server 2 & Open Network Connections:** Click **📌 Server 2 (Stub DNS)** in the left sidebar. Open **Start** $\rightarrow$ **Run** $\rightarrow$ `ncpa.cpl` $\rightarrow$ right-click **Ethernet** $\rightarrow$ **Properties** $\rightarrow$ **IPv4**.

6. **Enter Static IP & DNS:** Manually configure:

   * **IP address:** `192.168.1.22`

   * **Subnet mask:** `255.255.255.0`

   * **Preferred DNS server:** `192.168.1.1`

   * **Alternate DNS server:** `192.168.1.22`
     Click **OK** to save.

7. **Install DNS Server Role:** Open **Server Manager** $\rightarrow$ **+ Add roles and features** $\rightarrow$ navigate to **Server Roles**, check **🌐 DNS Server** $\rightarrow$ proceed to Confirmation $\rightarrow$ click **Install** $\rightarrow$ **Close**.

### Phase 3: Create Stub Zone, Load Records & Test Resolution (`SMEC-DNS2`)

 8. **Open DNS Manager on Server 2:** Open **DNS Manager** (`Start` $\rightarrow$ `Run` $\rightarrow$ `dnsmgmt.msc` or Desktop icon), then right-click **Forward Lookup Zones** $\rightarrow$ **New Zone...**.

 9. **Create Stub Zone:**

    * Select **📌 Stub zone**.

    * Enter **Zone name:** `tech.com`.

    * Keep **Zone file:** `tech.com.dns`.

    * Enter **Master DNS Server IP:** `192.168.1.1`.

    * Click **Finish**.

10. **Load Stub Zone from Master:** Click **`tech.com (Stub)`**, then right-click **`tech.com`** $\rightarrow$ **Transfer from Master**. Observe that **only** `SOA` and `NS` records are loaded — **no Host (`A`) records** (`www.tech.com`) are stored on Server 2.

11. **Test Stub DNS Resolution:** Open **Command Prompt** (`cmd`) on Server 2 and run:

    ```
    ping www.tech.com
    ```

    *(or `nslookup www.tech.com`)*

    Confirm that `SMEC-DNS2` uses its Stub Zone `NS` record to refer the query to `SMEC-DC1` (`192.168.1.1`) and successfully resolves `www.tech.com`, achieving **100% Lab Completion**.

## Key Technical Concepts & Best Practices

* **Stub Zone vs. Conditional Forwarder:** Both direct DNS queries for a specific domain to another DNS server, but a **Conditional Forwarder** relies on static IP addresses you enter manually. A **Stub Zone** dynamically refreshes its list of authoritative Name Servers (`NS`) from the Master server, making it far easier to maintain in large or changing networks.

* **Not for Redundancy:** Because a Stub Zone does not hold a full copy of Host (`A`) records, it is **not** a substitute for a Secondary Zone when you need DNS fault tolerance or failover.

## How to Run & Deploy

### Launch Live Online

Open the hosted simulator directly in your browser:

> **https://tibinjohn193-blip.github.io/az-802/lab18.html**

### Run Locally (Offline)

1. Download `lab18.html` from this repository.

2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).

3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)

* **CSS3** (Responsive flexbox layout, Windows Server 2025 UI styling, live progress badges)

* **Vanilla JavaScript** (11-step validation engine, Zone Transfer IP allowlist modal, Stub New Zone Wizard, Server Manager role installer, and simulated Stub referral CMD parser)