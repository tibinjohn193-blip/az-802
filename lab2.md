
# 🖥️ Lab 2: VMware Installation & Virtual Machine Provisioning

Welcome to Lab 2! In this lab, we focus on setting up the foundation for your server environment using VMware Workstation. 

**⚠️ Important Note:** This lab covers only the hypervisor installation, VM creation, and booting process. The actual operating system installation steps (Language selection, Disk partitioning, OOBE) are covered in **Lab 1**. Once this lab successfully boots the ISO, please refer to Lab 1 to continue.

---

## 🧠 What is VMware Workstation?
VMware Workstation Pro is a powerful "Type-2 Hypervisor". Simply put, it is a software application that allows you to run multiple independent operating systems (Virtual Machines) on a single physical computer at the same time.

### Key Features & Uses in IT:
* **Safe Testing Environment (Sandbox):** Test new servers, software, or security configurations safely without harming your real computer.
* **Snapshots:** Save the exact state of a Virtual Machine and easily revert back to it if you make a mistake or break the OS.
* **Network Virtualization:** Create isolated virtual networks (NAT, Bridged, Host-Only) to simulate real-world enterprise infrastructure.
* **Cost Effective Learning:** Practice advanced server administration without buying expensive physical server hardware.

---

## 🔗 Official Download Links
To build this environment on your actual physical machine, you will need to download the following software:

### 1. VMware Workstation Pro (Free for Personal Use)

* **Download Portal:** [l - VMware Desktop Hypervisors](https://www.techspot.com/downloads/189-vmware-workstation-for-windows.html)
* *(Instructions: You must create a free Broadcom account. During the software installation, select "For Personal Use" to activate it without needing a license key).*

### 2. Windows Server 2025 Evaluation ISO
Microsoft provides a free 180-day evaluation ISO image for testing and learning purposes.
* **Download Portal:** [Microsoft Evaluation Center - Windows Server 2025](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2025)
* *(Instructions: Select the **64-bit ISO** option, fill out the basic registration form, and download the `.iso` file to your PC).*

---

## 🛠️ Lab Overview & Steps

This interactive browser lab will guide you through the following phases:

### Phase 1: Installing VMware Workstation
Run the setup file on your desktop and complete the standard installation process, accepting the EULA and leaving default paths.

### Phase 2: Creating a Custom Virtual Machine
Open VMware and create a new VM with the following specific hardware configurations:
* **Configuration Type:** Custom (advanced)
* **Guest OS:** Windows Server 2025 *(Select: I will install the operating system later)*
* **Firmware:** UEFI with Secure Boot
* **Memory (RAM):** 4096 MB (4 GB)
* **Network Connection:** Bridged Networking *(This allows the VM to get an IP directly from your physical router)*
* **Disk Type:** NVMe (60 GB)

### Phase 3: Mounting the ISO & Booting
1. Right-click the newly created VM and select **Settings...**
2. Go to **CD/DVD (SATA)**, select "Use ISO image file", and browse your computer to select the downloaded Windows Server 2025 ISO file.
3. Power on the Virtual Machine.
4. Quickly click inside the black VM screen and press any key on your keyboard when prompted with *"Press any key to boot from CD or DVD..."*

---

## ⏭️ What's Next? (Refer to Lab 1)
Once you press a key, the Windows loader (spinning circle) will appear. **Congratulations! The hardware provisioning phase is complete.** 
    
Since the actual Windows Server 2025 OS installation process is quite detailed, **please switch to [Lab 1: Windows Server 2025 OS Installation](#)** to continue with the setup and configuration of the server operating system.
