# Convert Secondary DNS to Primary DNS (Failover Lab) — Windows Server 2025 Simulator

<div align="center">

### 🔄 DNS Disaster Recovery & Failover — Interactive Lab (`lab17.html`)

[![Click to Start Interactive Lab](https://img.shields.io/badge/▶_CLICK_TO_START-INTERACTIVE_LAB_(lab17.html)-0078D7?style=for-the-badge&logo=windows&logoColor=white)](https://tibinjohn193-blip.github.io/az-802/lab17.html)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab17.html>

</div>

---

> ### ⚠️ CRITICAL WARNING — PRODUCTION ENVIRONMENT
>
> **NEVER manually shut down a healthy, working Primary DNS server in a live production environment!**
>
> Converting a **Secondary DNS Zone** to a **Primary DNS Zone** is strictly a **disaster recovery / failover procedure** performed *only* when the Primary DNS server has **crashed, suffered hardware failure, or gone permanently offline**.
>
> In this lab simulator, we manually shut down **Server 1 (`SMEC-DC1`)** *solely to simulate an unexpected server outage* so you can practice the failover steps safely.

---

An interactive, single-file web-based **Windows Server 2025 Dual-Server Simulator** designed for hands-on DNS disaster recovery training. This lab guides students step-by-step through verifying the active Primary Zone (`tech.com`) on **SMEC-DC1**, simulating a Primary DNS server outage, converting the read-only Secondary Zone on **SMEC-DNS2** into a writable **Primary Zone** (`Properties` $\rightarrow$ `General` $\rightarrow$ `Change...`), creating a new Host (`A`) record (`mail.tech.com`) to prove writability, and verifying name resolution via `ping` while Server 1 remains offline.

## Table of Contents

1. [When & Why Do You Convert a Secondary DNS Zone to Primary?](#when--why-do-you-convert-a-secondary-dns-zone-to-primary)
2. [Lab Environment Specifications](#lab-environment-specifications)
3. [Simulator Interface & Features](#simulator-interface--features)
4. [Complete 10-Step Lab Walkthrough](#complete-10-step-lab-walkthrough)
5. [Post-Failover Best Practices & Avoiding Split-Brain DNS](#post-failover-best-practices--avoiding-split-brain-dns)
6. [How to Run & Deploy](#how-to-run--deploy)
7. [Technology Stack](#technology-stack)

## When & Why Do You Convert a Secondary DNS Zone to Primary?

A **Secondary DNS Zone** is a **read-only** copy of a DNS zone replicated from a Primary DNS server. While it continues answering DNS queries if the Primary server goes down, administrators **cannot add, edit, or delete DNS records** on a Secondary server.

If the Primary DNS server (`SMEC-DC1`) fails and cannot be brought back online quickly:
* Existing records (`www.tech.com`) will eventually expire once the SOA **Expire** timer runs out.
* Administrators are blocked from creating new DNS records (`A`, `MX`, `CNAME`, etc.) for the domain.
* Promoting the Secondary Zone on **SMEC-DNS2** to a **Primary Zone** immediately restores full **read/write authority**, updates the Start of Authority (`SOA`) and Name Server (`NS`) records, and keeps `tech.com` operational with zero data loss.

## Lab Environment Specifications

| Component | Server 1 (Original Primary DNS) | Server 2 (Secondary $\rightarrow$ Promoted Primary) |
| :--- | :--- | :--- |
| **Hostname** | `SMEC-DC1` | `SMEC-DNS2` |
| **Operating System** | Windows Server 2025 | Windows Server 2025 |
| **IP Address** | `192.168.1.1` | `192.168.1.22` |
| **Existing Domain** | `smec.com` | Member / DNS Server |
| **Target DNS Zone** | `tech.com` *(Primary — Goes Offline in Step 3)* | `tech.com` *(Secondary $\rightarrow$ Converted to Primary)* |
| **Existing Host (`A`) Record** | `www.tech.com` $\rightarrow$ `192.168.1.1` | Replicated from Server 1 |
| **New Verification Host (`A`)** | *(Offline)* | `mail.tech.com` $\rightarrow$ `192.168.1.22` *(Added in Step 9)* |

## Simulator Interface & Features

The simulator (`lab17.html`) uses a clean, full-screen **Dual-Server Switcher** layout:

1. **Left Sidebar (Server Switcher & Failover Checklist):**
   * Switch cleanly between **🖥️ Server 1 (`SMEC-DC1`)** and **🖥️ Server 2 (`SMEC-DNS2`)**.
   * **Live Status Indicator Dots:** Server 1's green dot turns **red (`OFFLINE (Failed)`)** once the simulated failure occurs.
   * **Failover Checklist Card:** Tracks all 4 core milestones (`Verify Primary on S1`, `Primary Server Failure`, `Convert S2 to Primary`, `Test Writable & Ping`) in real time.
   * Includes a dedicated **⚠️ Production Environment Warning** box and a **Reset Lab** button.

2. **Main Workspace Stage:**
   * **Server Manager (`Tools` $\rightarrow$ `DNS`):** Authentic Server Manager dashboard with the **Tools** dropdown menu on both servers.
   * **Read-Only Protection:** Before conversion, right-clicking `tech.com` on Server 2 shows `New Host (A or AAAA)... (Read-Only)` disabled.
   * **Authentic Zone Conversion Dialogs:** `Properties` $\rightarrow$ `General` tab $\rightarrow$ `Change...` $\rightarrow$ **Change Zone Type** (`Primary zone`) $\rightarrow$ Windows confirmation popup (`Yes`) $\rightarrow$ `Apply` & `OK`.
   * **Interactive Command Prompt (`cmd`):** Verify resolution of `mail.tech.com` and `www.tech.com` while Server 1 is offline.

3. **Dedicated Bottom Lab Guide Bar:**
   * Fixed cleanly below the desktop stage so it never overlaps Start menus, Server Manager, or dialog buttons.
   * Tracks progress from **Step 1 / 10** to **100% Complete**.

## Complete 10-Step Lab Walkthrough

### Phase 1: Verify Primary Zone & Simulate Failure (`SMEC-DC1` — `192.168.1.1`)

1. **Open DNS Manager via Server Manager:** On **Server 1**, open **Server Manager** $\rightarrow$ click **🔧 Tools** $\rightarrow$ **🌐 DNS**.
2. **Verify Primary Zone (`tech.com`):** Click **`tech.com (Primary)`** under **Forward Lookup Zones** to confirm `tech.com` is hosted here as a writable **Primary Zone** containing `www.tech.com` (`192.168.1.1`).
3. **Simulate Primary Server Outage:**
   * Click **Start** $\rightarrow$ **⏻ Shut down (Simulate Failure)**.
   * Read the production warning prompt and click **Shut Down Now** to take `SMEC-DC1` offline.

### Phase 2: Convert Secondary Zone to Primary (`SMEC-DNS2` — `192.168.1.22`)

4. **Switch to Server 2 & Open DNS Manager:** Click **🖥️ Server 2 (Secondary)** in the left sidebar. Open **Server Manager** $\rightarrow$ **🔧 Tools** $\rightarrow$ **🌐 DNS** (or `Start` $\rightarrow$ `Run` $\rightarrow$ `dnsmgmt.msc`).
5. **Open Zone Properties:** Click **`tech.com (Secondary)`** under **Forward Lookup Zones** (note the Read-Only warning), then right-click **`tech.com`** and select **Properties**.
6. **Change Zone Type:** On the **General** tab (currently showing `Type: Secondary`), click the **Change...** button.
7. **Select Primary Zone:** In the **Change Zone Type** dialog, select **Primary zone** $\rightarrow$ click **OK** $\rightarrow$ click **Yes** on the DNS confirmation prompt.
8. **Apply & Save:** Confirm the **General** tab now displays `Type: Primary`, then click **Apply** and **OK**. Notice `tech.com` updates to **`tech.com (Primary ✓)`** and its SOA/NS records point to `smec-dns2.smec.com.`.

### Phase 3: Verify Writability & Name Resolution (`SMEC-DNS2`)

9. **Create a New Host (`A`) Record:** Prove the zone is now writable by right-clicking **`tech.com (Primary ✓)`** $\rightarrow$ **New Host (A or AAAA)...** $\rightarrow$ enter Name `mail` and IP address `192.168.1.22` $\rightarrow$ click **Add Host** $\rightarrow$ **Done**.
10. **Verify Resolution via Ping:** Open **Command Prompt** (`cmd`) on Server 2 and run:
    ```cmd
    ping mail.tech.com
    ```
    Confirm successful DNS resolution and replies from `192.168.1.22` while `SMEC-DC1` remains offline to achieve **100% Lab Completion**.

## Post-Failover Best Practices & Avoiding Split-Brain DNS

If the original Primary DNS server (`SMEC-DC1`) is later repaired and brought back online, **do not leave both servers configured as Primary for `tech.com`** — doing so causes **Split-Brain DNS** (conflicting zone records on two independent masters).

Instead, follow these steps when the old Primary returns:
1. Convert the zone on the repaired server (`SMEC-DC1`) to a **Secondary Zone** pointing to the new Primary (`192.168.1.22`)—or delete the stale zone and recreate it as Secondary—so it pulls all new records (`mail.tech.com`).
2. Update DHCP scopes and static client DNS settings if `192.168.1.22` will remain the permanent Primary DNS server.
3. Re-enable **Zone Transfers** on the new Primary (`SMEC-DNS2`) to keep redundancy active.

## How to Run & Deploy

### Launch Live Online
Open the hosted simulator directly in your browser:
> **https://tibinjohn193-blip.github.io/az-802/lab17.html**

### Run Locally (Offline)
1. Download `lab17.html` from this repository.
2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).
3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows Server 2025 UI styling, live server status dots and failover badges)
* **Vanilla JavaScript** (10-step validation engine, Server Manager Tools dropdown, simulated server outage overlay, Change Zone Type confirmation workflow, and CMD `ping` verification)