# MicroWIN V4.0 SP9 - Windows 11 Native Bypass 🚀

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A complete, working method to natively extract and run Siemens STEP 7 Micro/WIN V4.0 SP9 on modern 64-bit Windows 11 systems by completely bypassing the broken InstallShield wizard. 

The official installer often fails with "Unable to locate a valid executable" or "current version already installed" errors, and the S7DOS drivers throw fatal assertion loops on modern 64-bit networks. This guide sidesteps the OS checks, forces a native extraction via Linux, and manually patches the Windows Registry to get the IDE and PC/PPI USB drivers running perfectly.

## 🛠️ Prerequisites
*   The original STEP 7 Micro/WIN V4.0 SP9 Update files (`data1.hdr`, `data1.cab`, `data2.cab`).
*   A Linux environment for native extraction (e.g., Arch Linux via Termux).
*   Windows 11 (64-bit).
---
## ⚡ Quick Start (Pre-Configured Release)

If you want to skip the manual extraction process, you can use the pre-configured files provided in the **Releases** section of this repository.

**Step 1: Download the Assets**
Head to the [Releases](../../releases) page and download the following files:
*   `S7-200_Software.zip` (The pre-extracted and flattened IDE)
*   `LanguageFix.reg` (The registry patch)
*   `step7_mw_v4_sp8_9.zip` (Required only if you need the PC/PPI cable drivers)

**Step 2: Place the Files**
Extract `S7-200_Software.zip` directly to the root of your `C:\` drive. 
*   **Critical:** The path must be exactly `C:\S7-200_Software` for the registry fix to work.

**Step 3: Apply the Registry Patch**
Double-click the downloaded `LanguageFix.reg` file. Click **Yes** when Windows asks if you want to merge it into the registry. This prevents the software from crashing on startup.

**Step 4: Install Cable Drivers (Optional but Recommended)**
If you need to connect to a PLC or microcontroller via a PC/PPI cable:
1. Extract `step7_mw_v4_sp8_9.zip`.
2. Navigate to `Disk1\COMM` and run `setup.exe` as Administrator.
3. **Note:** Windows 11 will throw Fatal/Assertion errors regarding `siem_isotrans` because it lacks legacy 32-bit network stacks. Click **Ignore / Yes / OK** on all prompts to bypass the network failure and force the USB drivers to install. 
4. Restart your PC.

**Step 5: Launch**
Navigate to `C:\S7-200_Software\Program_Executable_FILES`. Right-click **`microwin.exe`** and select **Run as administrator**. 

---

## 🚀 The Bypass Procedure

### Step 1: Native Extraction (OS Bypass)
The Windows installer relies on binary header checks and proprietary InstallShield wrapping. We bypass this entirely using `unshield` on Linux.

1. Transfer `data1.hdr`, `data1.cab`, and `data2.cab` to your Linux environment (e.g., your Termux/Arch setup). All three files **must** be in the same directory.

2. Install the `unshield` package:

```bash
sudo pacman -Syu unshield  # Arch Linux
```
 * Run the extraction:
```
unshield -d S7-200_Software x data1.cab
```
 * Transfer the extracted S7-200_Software folder to your Windows 11 C:\ drive (C:\S7-200_Software).

Step 2: Flattening the DLLs:

unshield extracts files into separate subdirectories, breaking the application's local pathway mapping.

 * Open C:\S7-200_Software on Windows 11.
 * Search the folder for *.dll.
 * Copy all .dll files found in the search results.
 * Paste them directly into C:\S7-200_Software\Program_Executable_FILES. (If prompted to overwrite duplicates, select "Skip").

Step 3: S7DOS USB Driver Installation (Handling the Fatal Error):

You must install the S7DOS stack for your PC/PPI programming cable to communicate with the PLC or devices like the ESP8266.
 * Navigate to the COMM folder in your original installation media (Disk1\COMM).
 * Run setup.exe as Administrator.
 * CRITICAL: The installer will throw an Assertion Error and a Fatal Error (0x80070002 regarding siem_isotrans / sntienx.dll). This is because modern Windows 11 lacks the legacy 32-bit ISO Transport network stack.
 * Ignore and Bypass: Click Ignore, Yes, or OK on all error prompts to force the installer to push through. The USB/Serial communication drivers will successfully drop onto the disk before the network stack fails.
 * Restart Windows 11.

Step 4: The Language Registry Patch
Without an official installation, the IDE lacks its language mapping and will silently crash immediately after the splash screen.

 * Create a file named LanguageFix.reg.
 * Add the following code:
```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Siemens\MicroSystems\STEP 7-MicroWIN]
"Path"="C:\\S7-200_Software\\Program_Executable_FILES"

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Siemens\MicroSystems\STEP 7-MicroWIN\General]
"Language"=dword:00000000
```
 * Run LanguageFix.reg and merge it into your Windows Registry.

Step 5: Launch:

Navigate to C:\S7-200_Software\Program_Executable_FILES, right-click microwin.exe, and select Run as administrator.

The software will now open directly into the main workspace.
Created by mikey-7x.

---

📜 License

This project is licensed under the 
[MIT License](LICENSE).
You are free to use, modify, and distribute it with proper attribution.

---
