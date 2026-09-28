# Lab 8: Active Directory Group Policy - Blocking USB Drives

## 📖 Overview
This interactive simulation lab is part of the Windows Server (AZ-800/AZ-802) study series. It demonstrates how to enhance endpoint security by restricting users from accessing USB flash drives and other removable storage devices using Group Policy Objects (GPO).

In this scenario, you act as a System Administrator for the `smec.com` domain. Your task is to prevent the Sales Department from accessing external USB drives to prevent data exfiltration or malware infections. You will configure this policy on the Domain Controller and verify the restriction on a domain-joined client PC.

## 🧠 Key Concepts
* **Group Policy (GPO):** A hierarchical framework that allows network administrators to centrally manage, configure, and secure user and computer settings across an Active Directory environment.
* **Removable Storage Access Policy:** A specific category in Group Policy used to grant or deny read/write/execute access to external drives (like USBs, CDs/DVDs, and portable hard drives).
* **OU-Level Application:** Applying the policy only to the "Sales" Organizational Unit (OU) ensures that only users within this department (like Arun) are restricted, while other departments remain unaffected.

## 🖥️ Lab Environment
* **Server PC:** SMEC-DC1 (IP: 192.168.1.100 | Domain: smec.com)
* **Client PC:** PC-ARUN (IP: 192.168.1.101)
* **Target User:** Arun 
* **Target OU:** Sales Department

## 🎯 Learning Objectives
* Access Active Directory Users and Computers (ADUC) to verify organizational structures.
* Launch Group Policy Management via the Run dialog (`gpmc.msc`).
* Create and link a new Group Policy Object (GPO) named `Block_USB`.
* Navigate Administrative Templates to configure the "All Removable Storage classes: Deny all access" policy.
* Force a Group Policy update (`gpupdate /force`) on the server.
* Simulate connecting a USB drive on a client machine and verify the "Access is denied" restriction via File Explorer.

## 🚀 Access the Interactive Lab
You can run this simulation directly in your web browser without downloading any files:

**[▶ Start Lab 8: GPO Block USB Drives](https://tibinjohn193-blip.github.io/az-802/lab8.html)**

### How to use:
1. Click the link above to open the lab.
2. Follow the interactive **Lab Guide** located at the bottom of the screen to complete the tasks.
3. The simulation features a split-screen view:
   * **Left Side:** The Server interface where you configure the policies.
   * **Right Side:** The Client interface where you sign in, insert a virtual USB, and test the policy.

## 📋 Step-by-Step Walkthrough
1. **Verify User:** On the Server, open `dsa.msc` to verify the user "Arun" exists in the "Sales" OU.
2. **Open GPMC:** Open `gpmc.msc` to access Group Policy Management.
3. **Create GPO:** Right-click the Sales OU and create a new GPO named `Block_USB`.
4. **Edit Policy:** Edit the GPO, navigate to *User Configuration > Policies > Administrative Templates > System > Removable Storage Access*, and set **All Removable Storage classes: Deny all access** to **Enabled**.
5. **Close Windows:** Close the Group Policy Management Editor and GPMC windows.
6. **Apply Policy:** Open the Run dialog on the Server, type `cmd`, and execute `gpupdate /force`.
7. **Test on Client:** Sign in as Arun on the Client PC, click the button to insert the USB drive, open File Explorer, and try to access `USB Drive (E:)`. You will receive an "Access is denied" error message.

---
*Built for educational purposes and AZ-802 certification practice.*
