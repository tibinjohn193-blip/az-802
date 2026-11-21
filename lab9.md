# GPO Account Lockout Policy Lab — Windows Server 2025 & Client Simulator

An interactive, single-file web-based **Windows Server 2025 & Domain-Joined Client Simulator** designed for hands-on Active Directory and Group Policy training. This lab walks learners step-by-step through configuring an **Account Lockout Policy** inside the **Default Domain Policy**, testing account lockout on a client workstation, and unlocking the user account in Active Directory Users and Computers (ADUC).

<div align="center">

### 📦 GPO Account Lockout Policy — Interactive Windows Server 2025 & Client Lab

[![Click to Launch Interactive Lab](https://img.shields.io/badge/▶_CLICK_TO_START-INTERACTIVE_LAB-28a745?style=for-the-badge)](https://tibinjohn193-blip.github.io/az-802/lab9.html)



</div>
---

## Table of Contents

1. [Overview & Learning Objectives](#overview--learning-objectives)
2. [Lab Environment Specifications](#lab-environment-specifications)
3. [Lab Credentials](#lab-credentials)
4. [Simulator Interface & Features](#simulator-interface--features)
5. [Complete 17-Step Lab Walkthrough](#complete-17-step-lab-walkthrough)
6. [Key Technical Concepts & Best Practices](#key-technical-concepts--best-practices)
7. [How to Run & Deploy](#how-to-run--deploy)
8. [Technology Stack](#technology-stack)

---

## Overview & Learning Objectives

Brute-force and password-spraying attacks target domain user accounts by repeatedly guessing passwords. Configuring an **Account Lockout Policy** via Group Policy protects Active Directory accounts by temporarily locking them after a defined number of consecutive failed logon attempts.

By completing this lab, students will learn how to:

* Verify user accounts inside Organizational Units (OUs) using **Active Directory Users and Computers (`dsa.msc`)**.
* Understand why **Account Policies** (Password and Account Lockout policies) must be configured in the **Default Domain Policy** (`gpmc.msc`).
* Configure **Account lockout threshold**, **Account lockout duration**, and **Reset account lockout counter after**, including handling Windows' **Suggested Value Changes** prompt.
* Force immediate Group Policy refresh on the Domain Controller using `gpupdate /force`.
* Test and observe real-world account lockout behavior on a domain-joined Windows client (`PC-ARUN`).
* Manually unlock a locked-out user account in ADUC (`Account` tab) and verify restored sign-in access.

---

## Lab Environment Specifications

| Component | Details |
| :--- | :--- |
| **Domain Controller (Server)** | `SMEC-DC1` (`192.168.1.100`) — Windows Server 2025 |
| **Client Workstation** | `PC-ARUN` (`192.168.1.101`) — Domain Joined to `smec.com` |
| **Active Directory Domain** | `smec.com` |
| **Target Organizational Unit** | `Sales` OU |
| **Target GPO** | `Default Domain Policy` (linked at domain root `smec.com`) |
| **Lockout Threshold** | `3` invalid logon attempts |
| **Lockout Duration** | `30 minutes` |
| **Reset Lockout Counter After** | `30 minutes` |

---

## Lab Credentials

| Account / Role | Username | Password | Notes |
| :--- | :--- | :--- | :--- |
| **Client Domain User** | `smec\arun` (`arun@smec.com`) | `Password123` | Used to test failed logins and final unlocked sign-in |
| **Server Admin** | `SMEC\Administrator` | *(Pre-logged in)* | Used on `SMEC-DC1` for ADUC, GPMC, and CMD |

---

## Simulator Interface & Features

The simulator (`gpo_account_lockout_policy_lab_v3.html`) uses a clean, full-screen **Machine Switcher** layout so windows and text are never cramped or clipped:

1. **Left Sidebar (Machine Switcher):**
   * Switch seamlessly between **🖥️ Server (`SMEC-DC1`)** and **💻 Client (`PC-ARUN`)**.
   * **Attention Pulse Highlight:** Automatically pulses the yellow border on the Server or Client button whenever the current step requires switching machines.
   * Displays quick-reference lab parameters (`smec.com`, `Default Domain Policy`, threshold `3`, and Arun's password) plus a **Reset Lab** button.
2. **Main Workspace Stage:**
   * Full-width interactive Windows Server desktop with Start Menu, **Run** dialog (`dsa.msc`, `gpmc.msc`, `cmd`), **ADUC** (with authentic User Properties `General` and `Account` tabs), **Group Policy Management**, **Group Policy Management Editor**, **Suggested Value Changes** modal, and **Command Prompt**.
   * Full-width Windows Client lock screen with live failed-attempt counter, authentic Windows lockout error messages, and desktop welcome screen upon unlock.
3. **Dedicated Bottom Lab Guide Bar:**
   * Sits cleanly in the flex layout below the desktop stage so it never overlaps Start menus, login screens, or policy buttons.
   * Tracks progress from **Step 1 / 17** to **100% Complete**.

---

## Complete 17-Step Lab Walkthrough

### Phase 1: Verify Target User in ADUC (Server)

1. **Open Run:** On **SMEC-DC1**, click the **Start** button and open **Run**.
2. **Launch ADUC:** Type `dsa.msc` and click **OK**.
3. **Verify User Arun:** Expand `smec.com`, click the **Sales** OU to confirm user **Arun** (`Active`) exists, then close ADUC.

### Phase 2: Configure Account Lockout Policy in Default Domain Policy (Server)

4. **Open Run Again:** Click **Start** and open **Run**.
5. **Launch GPMC:** Type `gpmc.msc` and click **OK** to open **Group Policy Management**.
6. **Edit Default Domain Policy:** Expand `Forest: smec.com > Domains > smec.com`. Right-click **Default Domain Policy** and select **Edit...**.
7. **Navigate to Account Lockout Policy:** In the **Group Policy Management Editor**, expand:
   ```text
   Computer Configuration
    └── Policies
         └── Windows Settings
              └── Security Settings
                   └── Account Policies
                        └── Account Lockout Policy
   ```
8. **Open Threshold Setting:** Double-click **Account lockout threshold** in the right pane.
9. **Define Threshold:** Check **Define this policy setting**, enter `3` invalid logon attempts, and click **Apply** or **OK**.
10. **Accept Suggested Value Changes:** Windows automatically prompts with the **Suggested Value Changes** dialog setting:
    * **Account lockout duration:** `30 minutes`
    * **Reset account lockout counter after:** `30 minutes`
    Click **OK** to accept and apply all three settings.
11. **Close GPO Windows:** Close both **Group Policy Management Editor** and **Group Policy Management** windows.

### Phase 3: Force Group Policy Update (Server)

12. **Open Command Prompt:** Click **Start -> Run**, type `cmd`, and click **OK**.
13. **Run `gpupdate /force`:** Type the following command and press **Enter**:
    ```cmd
    gpupdate /force
    ```
    Confirm both Computer Policy and User Policy updates complete successfully.

### Phase 4: Test Account Lockout (Client)

14. **Trigger Lockout on `PC-ARUN`:**
    * Click **💻 Client** in the left sidebar.
    * Enter any **incorrect password** (e.g., `wrong1`, `wrong2`, `wrong3`) for **Arun** **3 times**.
    * On the 3rd failed attempt, the counter shows `Failed Attempts: 3 / 3 (LOCKED OUT)` and displays the authentic Windows error:
      > *"The referenced account is currently locked out and may not be logged on to."*

### Phase 5: Unlock Account & Verify Sign-In (Server & Client)

15. **Open Arun's Properties in ADUC:**
    * Switch back to **🖥️ Server** in the left sidebar.
    * Open **ADUC** (`Start -> Run -> dsa.msc`), click the **Sales** OU (notice Arun's status is `Locked Out 🔒`), and double-click **Arun**.
16. **Unlock the Account:**
    * On the **Account** tab, check **Unlock account** and click **OK**.
    * Confirm Arun's status updates to `Active (Unlocked)`.
17. **Verify Login on Client:**
    * Switch back to **💻 Client** in the left sidebar.
    * Sign in as **Arun** using the correct password `Password123` to reach the desktop and achieve **100% Lab Completion**.

---

## Key Technical Concepts & Best Practices

### Why Must Account Lockout Policy Be Configured in the Default Domain Policy?
In Active Directory, **Account Policies** (Password Policy, Account Lockout Policy, and Kerberos Policy) are evaluated at the **domain root** by the Domain Controllers. If you link an Account Lockout GPO to an individual OU (such as `Sales`), it will **not** apply to domain user accounts—it would only affect local SAM accounts on computers inside that OU.

> **Enterprise Note:** If an organization needs different lockout or password rules for specific users or groups (for example, stricter settings for Admins), administrators use **Fine-Grained Password Policies (Password Settings Objects / PSOs)** in the *Active Directory Administrative Center (ADAC)* rather than separate OU-linked GPOs.

### Understanding the Three Lockout Settings

| Setting | Configured Value | Purpose |
| :--- | :--- | :--- |
| **Account lockout threshold** | `3` invalid attempts | Number of consecutive bad passwords before the account locks. Setting this to `0` disables account lockout. |
| **Account lockout duration** | `30 minutes` | How long the account remains locked before automatically unlocking. Setting this to `0` means an administrator must manually unlock it. |
| **Reset account lockout counter after** | `30 minutes` | Time window after a bad password attempt before the bad-attempt counter resets to `0`. Must be less than or equal to the lockout duration. |

---

## How to Run & Deploy

## 🚀 Access the Interactive Lab
You can run this simulation directly in your web browser without downloading any files:

**[▶ Start Lab 9: Active Directory Group Policy - Account Lockout policy](https://tibinjohn193-blip.github.io/az-802/lab9.html)**

---

## Technology Stack

* **HTML5** (Self-contained single-file architecture)
* **CSS3** (Responsive flexbox layout, Windows-style dialogs, and sidebar attention indicators)
* **Vanilla JavaScript** (Step-validation engine, interactive MMC trees, simulated CMD parser, and live lockout/unlock state management)
