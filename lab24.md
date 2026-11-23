# Remote Access Routing — Static & Default Routing Lab (RRAS) — Windows Server 2025 Simulator

### 🔀 Windows Server RRAS LAN Routing, Static & Default Routes — Interactive Lab (`lab24.html`)

**Live URL:** <https://tibinjohn193-blip.github.io/az-802/lab24.html>

An interactive, single-file web-based **Windows Server 2025 & Windows 11 Client Simulator** designed for hands-on **Routing and Remote Access Service (RRAS)** administration training. This lab guides learners step-by-step through installing the **Remote Access** role with the **Routing** role service in Server Manager, enabling **LAN routing** in `rrasmgmt.msc`, configuring an IPv4 **Static Route** (`192.168.2.0/24`) and testing connectivity from a Client PC, and then configuring an IPv4 **Default Route** (`0.0.0.0 / 0.0.0.0` $\rightarrow$ `192.168.1.1`) to verify automatic routing from any other IP subnet.

## Table of Contents

1. [What is Routing and Remote Access (RRAS)?](#what-is-routing-and-remote-access-rras)

2. [Static Routing vs. Default Routing (`0.0.0.0`)](#static-routing-vs-default-routing-0000)

3. [Lab Environment Specifications](#lab-environment-specifications)

4. [Simulator Interface & Features](#simulator-interface--features)

5. [Complete 12-Step Lab Walkthrough](#complete-12-step-lab-walkthrough)

6. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)

7. [How to Run & Deploy](#how-to-run--deploy)

8. [Technology Stack](#technology-stack)

## What is Routing and Remote Access (RRAS)?

**Routing and Remote Access Service (RRAS)** is a built-in Windows Server role service (under the **Remote Access** role) that turns a Windows Server machine into a software-based multiprotocol router, VPN server, or Network Address Translation (NAT) gateway.

When **LAN Routing** is enabled in RRAS (`rrasmgmt.msc`), Windows Server forwards IPv4/IPv6 packets between different network subnets based on its routing table, allowing isolated client networks to communicate with the server and other subnets.

## Static Routing vs. Default Routing (`0.0.0.0`)

Both Static Routes and Default Routes are created under **IPv4** $\rightarrow$ **Static Routes** $\rightarrow$ **New Static Route...** in the RRAS console, but they serve different purposes:

| Feature | Static Routing *(Part 1 of Lab)* | Default Routing *(Part 2 of Lab)* | 
| ----- | ----- | ----- | 
| **Destination** | Specific target network ID (e.g., `192.168.2.0`) | **`0.0.0.0`** (Matches *any* destination network) | 
| **Network Mask** | Specific subnet mask (e.g., `255.255.255.0`) | **`0.0.0.0`** | 
| **Gateway** | Next-hop / Server IP (`192.168.1.1`) | Next-hop / Server IP (`192.168.1.1`) | 
| **Behavior** | Only routes traffic for the explicitly defined subnet (`192.168.2.0/24`). | Acts as a **Gateway of Last Resort** — automatically forwards traffic for any network not explicitly listed in the routing table. | 
| **Best Use Case** | Point-to-point routing to specific known internal branch networks. | Connecting stub networks or routing unknown/multiple client subnets automatically to a central router or internet gateway. | 

## Lab Environment Specifications

| Component | Server (`SMEC-DC1`) | Client (`CLIENT-PC`) | 
| ----- | ----- | ----- | 
| **Operating System** | Windows Server 2025 | Windows 11 | 
| **Active Directory Domain** | `smec.com` | Test Client Workstation | 
| **IP Address** | `192.168.1.1` | Part 1: `192.168.2.10` · Part 2: Any other subnet (e.g., `10.10.10.5`) | 
| **Subnet Mask** | `255.255.255.0` | `255.255.255.0` | 
| **Default Gateway** | — | `192.168.1.1` *(Points to `SMEC-DC1`)* | 
| **Required Role & Service** | **Remote Access** $\rightarrow$ **Routing** | — | 
| **RRAS Mode** | Custom Configuration $\rightarrow$ **LAN routing** | — | 
| **Management Console** | `rrasmgmt.msc` | `ncpa.cpl` & `cmd` (`ping`, `ipconfig`) | 

## Simulator Interface & Features

The simulator (`lab24.html`) uses a full-screen **Server / Client Switcher** layout:

1. **Left Sidebar (Machine Switcher & Live Checklist):**

   * Switch seamlessly between **🖥️ Server (`SMEC-DC1`)** and **💻 Client PC (`Win 11`)**.

   * **Attention Pulse Highlight:** Automatically pulses a yellow border around whichever machine is needed for the current step.

   * **Live Lab Progress Card:** Tracks all 6 milestones (`Install Remote Access`, `Enable RRAS LAN Routing`, `Static Route`, `Client Static Ping Test`, `Default Route`, and `Client Any-IP Ping Test`) in real time.

2. **Main Workspace Stage:**

   * **Server Manager (`Add Roles and Features Wizard`):** Complete 9-step wizard to install **Remote Access** and the **Routing** role service (unlocking the RRAS console upon completion).

   * **Routing and Remote Access Console (`rrasmgmt.msc`):** Includes the **RRAS Server Setup Wizard** (`Custom configuration` $\rightarrow$ `LAN routing`), live red-to-green server status indicator, and the **IPv4 Static Route** dialog supporting both specific Static Routes and Default Routes (`0.0.0.0`).

   * **Client Network & Command Prompt:** Manual IPv4 configuration in `ncpa.cpl` (no auto-fill) and interactive `cmd` supporting `ping 192.168.1.1` and `ipconfig`, showing which RRAS route matched the client's IP.

3. **Dedicated Bottom Lab Guide Bar:**

   * Fixed cleanly below the desktop stage so it never overlaps Start menus, wizards, or Command Prompt windows.

   * Tracks progress from **Step 1 / 12** to **100% Complete**.

## Complete 12-Step Lab Walkthrough

### Phase 1: Install Remote Access & Routing Role Service (`SMEC-DC1`)

1. **Select Remote Access Role:** Open **Server Manager** $\rightarrow$ click **+ Add roles and features** $\rightarrow$ navigate to **Server Roles** $\rightarrow$ check **🔀 Remote Access** $\rightarrow$ click **Next**.

2. **Select Routing Role Service & Install:** Advance to **Role Services**, check **🔀 Routing** $\rightarrow$ click **Next** $\rightarrow$ click **Install** $\rightarrow$ click **Close** once installation succeeds.

### Phase 2: Configure & Enable RRAS LAN Routing (`SMEC-DC1`)

3. **Open RRAS Console:** Open **Routing and Remote Access** (via **Server Manager** $\rightarrow$ **Tools** $\rightarrow$ **Routing and Remote Access**, Desktop icon, or `Start` $\rightarrow$ `Run` $\rightarrow$ `rrasmgmt.msc`).

4. **Enable LAN Routing:** Right-click **🔴 SMEC-DC1 (local)** $\rightarrow$ click **Configure and Enable Routing and Remote Access** $\rightarrow$ select **Custom configuration** $\rightarrow$ check **🔀 LAN routing** $\rightarrow$ click **Finish & Start Service**. Confirm the server icon turns **🟢 green**.

### Phase 3: Configure Static Route & Verify from Client PC

5. **Add IPv4 Static Route (`192.168.2.0/24`):** Expand **IPv4** $\rightarrow$ right-click **Static Routes** $\rightarrow$ **New Static Route...** and enter:

   * **Destination:** `192.168.2.0`

   * **Network mask:** `255.255.255.0`

   * **Gateway:** `192.168.1.1`

   * **Metric:** `1`
     Click **OK**.

6. **Switch to Client PC & Open Network Connections:** Click **💻 Client PC** in the left sidebar. Open **Start** $\rightarrow$ **Run**, type `ncpa.cpl`, and click **OK**. Right-click **Ethernet** $\rightarrow$ **Properties** $\rightarrow$ **Internet Protocol Version 4 (TCP/IPv4)**.

7. **Configure Client Static Network IP:** Manually enter:

   * **IP address:** `192.168.2.10`

   * **Subnet mask:** `255.255.255.0`

   * **Default gateway:** `192.168.1.1`
     Click **OK**.

8. **Verify Static Route with Ping:** Open **Command Prompt** (`cmd`) on Client PC and run:

   ```
   ping 192.168.1.1
   
   ```

   Confirm ping replies succeed via the `192.168.2.0/24` Static Route.

### Phase 4: Configure Default Route (`0.0.0.0`) & Verify from Any IP Subnet

 9. **Add IPv4 Default Route on Server:** Switch back to **🖥️ Server (`SMEC-DC1`)**. In RRAS, right-click **IPv4** $\rightarrow$ **Static Routes** $\rightarrow$ **New Static Route...** and enter:

    * **Destination:** `0.0.0.0`

    * **Network mask:** `0.0.0.0`

    * **Gateway:** `192.168.1.1`

    * **Metric:** `1`
      Click **OK**.

10. **Switch to Client PC & Open IPv4 Settings:** Click **💻 Client PC** in the left sidebar and open `ncpa.cpl` $\rightarrow$ **Ethernet** $\rightarrow$ **Properties** $\rightarrow$ **IPv4**.

11. **Assign Any Other Network IP:** Enter **any** different network IP (e.g., `10.10.10.5` or `172.16.5.20`), Subnet mask `255.255.255.0`, and **Default gateway** `192.168.1.1` $\rightarrow$ click **OK**.

12. **Verify Default Route with Ping:** Open **Command Prompt** on Client PC and run:

    ```
    ping 192.168.1.1
    
    ```

    Observe that the client automatically reaches `192.168.1.1` via the **`0.0.0.0 / 0.0.0.0` Default Route**, achieving **100% Lab Completion**.

## Key Technical Concepts & Best Practices

* **Longest Prefix Match:** When a router has both a specific Static Route (`192.168.2.0/24`) and a Default Route (`0.0.0.0/0`), Windows Server always prefers the **most specific match** first and falls back to `0.0.0.0/0` only when no more-specific route matches.

* **Inspecting the Routing Table via CLI:** On a real Windows Server, you can verify active RRAS static and default routes from an elevated Command Prompt using `route print`.

* **Production Network Design:** In multi-subnet production environments, RRAS routers typically use **two or more Network Interface Cards (NICs)**—one per subnet—so each subnet's Default Gateway resides in its own local broadcast domain.

## How to Run & Deploy

### Launch Live Online

Open the hosted simulator directly in your browser:

> **https://tibinjohn193-blip.github.io/az-802/lab24.html**

### Run Locally (Offline)

1. Download `lab24.html` from this repository.

2. Double-click the file to open it in any modern browser (Chrome, Edge, Firefox, or Safari).

3. Works **100% offline** with zero external dependencies.

## Technology Stack

* **HTML5** (Self-contained single-file architecture)

* **CSS3** (Responsive flexbox layout, Windows Server 2025 & Windows 11 UI styling, sidebar attention indicators)

* **Vanilla JavaScript** (12-step validation engine, Server Manager Remote Access installer, RRAS Setup Wizard, IPv4 Static/Default route manager, and simulated `ping` / `ipconfig` CMD parser)