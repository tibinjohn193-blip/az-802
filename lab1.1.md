
# 🖥️ Lab: Windows Server 2025 Installation (Modern Setup Experience)

Welcome to the interactive simulation for installing **Windows Server 2025 Standard (Desktop Experience)**. Microsoft has introduced a brand-new, modern, white-themed setup interface for Server 2025, replacing the classic blue setup screens used in previous generations. 

This lab allows you to practice the entire installation process safely in your browser without needing a Virtual Machine or an ISO file.

---

## 🎯 Lab Objectives
By completing this lab, you will learn how to:
1. Understand the real-world process of accessing the Boot Menu and booting from an installation media.
2. Navigate the new **Modern Windows Setup Experience**.
3. Select the correct installation type (**Desktop Experience** vs. Server Core).
4. Perform a clean (Custom) installation on unallocated disk space.
5. Complete the Out-of-Box Experience (OOBE) by configuring the local Administrator account.
6. Perform the first login and access Server Manager.

---

## 🥾 Pre-Lab Concept: Accessing the Boot Menu in the Real World
*Note: The interactive simulation starts directly at the setup screen. In the real world, you must first boot your server from the installation media.*

1. Insert your **Windows Server 2025 Bootable USB** (created via Rufus) into the physical server.
2. Power on the server and immediately press the **Boot Menu Key** (Usually `F12`, `F11`, `F8`, or `Esc` depending on the hardware manufacturer like Dell, HP, or Lenovo).
3. Select your **USB Flash Drive** from the Boot Menu.
4. When prompted with *"Press any key to boot from CD or DVD..."*, quickly press **Enter** or the **Spacebar**.
5. The server will load the Windows Preinstallation Environment (WinPE) and display the modern setup screen.

---

## 🛠️ Interactive Lab Steps

Follow these steps within the browser simulation to complete the installation:

1. **Select Language & Time:** Accept the default English (United States) settings.
2. **Setup Option:** Choose **Install Windows Server 2025**. (Do not select Repair).
3. **Product Key:** Click on **"I don't have a product key"** to continue using the evaluation mode.
4. **Select Image:** Carefully choose **Windows Server 2025 Standard (Desktop Experience)**. 
   * *Warning: Selecting the option without "Desktop Experience" will install Server Core (Command-line only).*
5. **Notices and Terms:** Accept the Microsoft Software License Terms.
6. **Select Location:** Select **Drive 0 Unallocated Space** and click Next. Windows will automatically format and partition the drive for you.
7. **Installation:** Wait for the setup to copy files, install features, and reboot the system.
8. **OOBE (Out-of-Box Experience):** Once rebooted, you will be prompted to set up the built-in Administrator account. Set the password to `Admin@123`.
9. **Login:** At the lock screen, simulate `Ctrl + Alt + Delete` by clicking the screen, enter your password, and verify that **Server Manager** launches successfully on the desktop.

---

## 💡 Key Takeaways
* The new setup interface provides clearer navigation on the left pane.
* Choosing **"Desktop Experience"** is crucial if you need a graphical interface (GUI).
* Windows Setup handles the heavy lifting of disk partitioning automatically if you select unallocated space.

[**Launch the Interactive Simulation Now**](https://tibinjohn193-blip.github.io/az-802/lab1.html)
