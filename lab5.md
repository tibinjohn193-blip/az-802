# 🖥️ Lab 5: Windows 11 Active Directory Domain Join ,add pc to domain

![License](https://img.shields.io/badge/License-MIT-blue.svg)
![Version](https://img.shields.io/badge/Version-1.0-green.svg)
![Tech Stack](https://img.shields.io/badge/Tech-HTML%20%7C%20CSS%20%7C%20JS-orange.svg)

## 📌 Project Overview
This project is a fully interactive, browser-based simulation designed to teach IT students and professionals how to configure a Windows 11 client machine and join it to a Windows Server Active Directory domain. It features an authentic Windows 11 interface, a functional Command Prompt for network testing, and a real-time Network Topology visualizer.

---

## ❓ Why Join a PC to a Domain?
In corporate and enterprise environments, computers are rarely left as standalone workgroups. Joining a PC to an Active Directory (AD) domain provides several critical benefits:

1. **Centralized Management:** IT Administrators can manage thousands of computers from a single server using Group Policy Objects (GPO). They can push software updates, map network drives, and enforce desktop wallpapers automatically.
2. **Single Sign-On (SSO):** Users can log in to any computer on the network using a single set of domain credentials (Username/Password), without needing local accounts created on every machine.
3. **Enhanced Security:** Password policies (length, complexity, expiration) and account lockout rules can be strictly enforced network-wide.
4. **Centralized Resource Access:** Domain-joined PCs can securely and seamlessly access network resources like shared folders, databases, and enterprise printers based on Active Directory permissions.
5. **Easy Auditing & Tracking:** IT teams can monitor logon activities, track connected devices, and revoke access instantly if an employee leaves the organization.

---

## 📝 Step-by-Step Lab Process
This simulator guides the user through the exact real-world procedure required to join a domain. 

### Phase 1: Client IP Configuration
For a PC to find the Domain Controller, it must point to the server's DNS.
* **Step 1:** Open the Windows 11 Start Menu and launch **Run**.
* **Step 2:** Type `ncpa.cpl` and press Enter to open Network Connections.
* **Step 3:** Right-click **Ethernet0** -> **Properties** -> Select **IPv4** -> **Properties**.
* **Step 4:** Set the static IP configuration:
  * IP Address: `192.168.10.2`
  * Subnet Mask: `255.255.255.0`
  * Preferred DNS: `192.168.10.1` (Points to the Domain Controller).
* **Step 5:** Save and close network settings.

### Phase 2: Test Connectivity (Ping)
Always verify network connectivity before attempting a domain join.
* **Step 6:** Open **Run** -> Type `cmd` to open the Command Prompt.
* **Step 7:** Type `ping 192.168.10.1` and press Enter.
* **Step 8:** Wait for the successful reply packets (The Network Topology Widget will light up green indicating a successful connection). Close CMD.

### Phase 3: Domain Join Initiation
* **Step 9:** Open **Run** -> Type `sysdm.cpl` to open System Properties.
* **Step 10:** Click the **Change...** button to rename the computer or change its domain.
* **Step 11:** Select the **Domain:** option and type `smec.com`. Click **OK**.
* **Step 12:** A Windows Security prompt will appear. Enter the Domain Administrator credentials:
  * Username: `Administrator`
  * Password: `Admin@123`
* **Step 13:** Receive the "Welcome to the smec.com domain" success message.
* **Step 14:** Accept the required system restart prompt to complete the lab.

---

## 🚀 Key Simulator Features
- **Authentic Windows 11 UI:** Accurately mimics the centered taskbar, start menu, and fluent design of Windows 11.
- **Live Network Topology Widget:** A visual diagram on the desktop that updates in real-time when a successful ping connects the Client to the Server.
- **Command Prompt Simulation:** A case-insensitive CMD engine built to execute the required `ping` command and simulate real network latency.
- **Strict Execution Logic:** The built-in Lab Guide enforces correct order. You cannot join the domain without setting the IP and testing the ping first.

## 🔗 Access the Lab
[👉 **Click Here to Run Lab 5: Windows 11 Domain Join**](https://tibinjohn193-blip.github.io/az-802/lab5.html)
