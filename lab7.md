# Lab 7: Active Directory Group Policy - Blocking Command Prompt

## 📖 Overview
This interactive simulation lab is part of the Windows Server (AZ-800/AZ-802) study series. It demonstrates how to enhance system security by preventing specific users from accessing the Command Prompt (CMD) using Group Policy Objects (GPO) in an Active Directory environment.

In this scenario, you act as a System Administrator for the `smec.com` domain. Your task is to prevent the Sales Department from running the interactive command prompt or executing batch files. You will configure this policy on the Domain Controller and verify the restriction on a domain-joined client PC.

## 🖥️ Lab Environment
* **Server PC:** SMEC-DC1 (IP: 192.168.1.100 | Domain: smec.com)
* **Client PC:** PC-ARUN (IP: 192.168.1.101)
* **Target User:** Arun 
* **Target OU:** Sales Department

## 🎯 Learning Objectives
* Access Active Directory Users and Computers (ADUC) via the Run dialog (`dsa.msc`).
* Verify Organizational Units (OU) and user accounts in a domain.
* Launch Group Policy Management via the Run dialog (`gpmc.msc`).
* Create and link a new Group Policy Object (GPO) to a specific OU.
* Navigate Administrative Templates to enable the "Prevent access to the command prompt" policy.
* Force a Group Policy update (`gpupdate /force`) on the server.
* Verify the applied restrictions and error prompts on a client machine.

## 🚀 Access the Interactive Lab
You can run this simulation directly in your web browser without downloading any files:

**[▶ Start Lab 7: Active Directory Group Policy - Blocking Command Prompt](https://tibinjohn193-blip.github.io/az-802/lab7.html)**

### How to use:
1. Click the link above to open the lab.
2. Follow the interactive **Lab Guide** overlay on the screen to complete the tasks.
3. The simulation features a split-screen view:
   * **Left Side:** The Server interface where you configure the policies.
   * **Right Side:** The Client interface where you log in as the user and verify the policy enforcement.

## 📋 Step-by-Step Walkthrough
1. **Verify User:** On the Server, open `dsa.msc` to verify the user "Arun" exists in the "Sales" OU.
2. **Open GPMC:** Open `gpmc.msc` to access Group Policy Management.
3. **Create GPO:** Right-click the Sales OU and create a new GPO named `Block_CMD`.
4. **Edit Policy:** Edit the GPO, navigate to *User Configuration > Policies > Administrative Templates > System*, and set **Prevent access to the command prompt** to **Enabled**.
5. **Apply Policy:** Open the Run dialog on the Server, type `cmd`, and run `gpupdate /force`.
6. **Test on Client:** Sign in as Arun on the Client PC, attempt to open `cmd` via the Run dialog, and observe the "disabled by your administrator" error message.

---
*Built for educational purposes and AZ-802 certification practice.*
