# 🛠️ Windows 11 & 10 Native Repair Scripts & Command Generators

Welcome to the official **AxeeTech** open-source Windows troubleshooting repository. 

Instead of installing bloated third-party "PC cleaner" software, every script in this repository uses **100% native Microsoft Windows binaries** (`reagentc`, `diskpart`, `powercfg`, `manage-bde`, `DISM`, and `reg.exe`) tested on live **Windows 11 (24H2 / 25H2 / 26H2)** and **Windows 10** systems.

> ⚡ **Prefer a Visual Point-and-Click Builder?**  
> Use our interactive browser-based generators on **[AxeeTech.com](https://axeetech.com/)** to customize drive letters, partition numbers, and `.REG` files before downloading.

---

## 📂 Quick Navigation & Interactive Web Tools

| Windows Issue / Error | Native CLI Tool Used | Interactive Web Builder & Full Guide |
| :--- | :--- | :--- |
| **Windows 11 26H2 Known Issues & Rollback (`KB5124010`)** | `wusa` / `DISM` / `pnputil` | **[Windows 11 26H2 Guide & Fixes](https://axeetech.com/windows-11-26h2-release/)** |
| **Windows RE Image Was Not Found (`reagentc` / `0x80070643`)** | `reagentc` + `diskpart` | **[WinRE Image Not Found: 6 Fixes](https://axeetech.com/windows-re-image-was-not-found/)** |
| **Missing Power Plans in Windows 10 & 11 (`Modern Standby S0`)** | `powercfg` + `reg.exe` | **[Restore Missing Power Plans Guide](https://axeetech.com/missing-power-plans-in-windows-10-and-11/)** |
| **BitLocker Stuck on "Waiting for Activation" (Home & Pro)** | `manage-bde` | **[BitLocker Waiting for Activation Fixes](https://axeetech.com/bitlocker-waiting-for-activation/)** |
| **Windows + V Clipboard History Not Working** | `rdpclip` + `cbdhsvc` | **[Windows + V Clipboard History Fixes](https://axeetech.com/windows-v-not-working/)** |
| **Windows 11 Taskbar & Start Menu Freezing** | `taskkill` + `AppxPackage` | **[Windows 11 Taskbar Freezing Fixes](https://axeetech.com/windows-11-taskbar-freezing/)** |
| **Cannot Delete `Windows.old` ("Access Denied")** | `takeown` + `icacls` + `cleanmgr` | **[Safe Ways to Delete Windows.old](https://axeetech.com/delete-windows-old/)** |
| **Exact Partition Size Calculator (No `99.9 GB` Bug)** | Cylinder-Aligned NTFS Math | **[Interactive Partition Size Calculator](https://axeetech.com/windows-partition-size-calculator/)** |
| **Windows 11 Registry (`.REG`) & Debloat Customizer** | `regedit` (`HKCU` / `HKLM`) | **[Interactive .REG File Customizer](https://axeetech.com/windows-11-registry-tweaks-generator/)** |

---

## 1. Fix "Windows RE Image Was Not Found" (`reagentc /enable`)

When enabling the Windows Recovery Environment fails with **Windows RE image was not found** or **0x80070643**, run the following commands in an **Elevated Command Prompt (Run as Administrator)**:

```bat
:: Step 1: Check current WinRE status
reagentc /info

:: Step 2: Reset and point reagentc to the default System32 Recovery path
reagentc /disable
reagentc /setreimage /path C:\Windows\System32\Recovery
reagentc /enable
reagentc /info
Need to shrink C: and recreate a larger 1024 MB GPT Recovery Partition (de94bba4-06d1-4d40-a16a-bfd50179d6ac)?

👉 Use our interactive Diskpart script builder on the Windows RE Image Was Not Found Guide.

2. Restore Missing Power Plans & Bypass Modern Standby (S0)
Laptops with S0 Low Power Idle (Modern Standby) hide the High Performance and Ultimate Performance power plans in Control Panel. Run these commands as Administrator to unlock them:

DOS
:: Step 1: Bypass Modern Standby S0 lock (requires reboot to display in Control Panel)
reg add "HKLM\System\CurrentControlSet\Control\Power" /v PlatformAoAcOverride /t REG_DWORD /d 0 /f

:: Step 2: Duplicate Ultimate Performance & High Performance GUIDs
powercfg -duplicatescheme e9a42b02-d5df-448d-aa00-03f14749eb61
powercfg -duplicatescheme 8c5e7fda-e8bf-4a96-9a85-a6e23a8c635c
powercfg -duplicatescheme a1841308-3541-4fab-bc81-f71556f20b4a
powercfg -duplicatescheme 381b4222-f694-41f0-9685-ff5bb260df2e

:: Step 3: List all available power schemes
powercfg /list
👉 Full breakdown & rollback steps: Missing Power Plans in Windows 10 and 11.

3. Clear BitLocker "Waiting for Activation" (Windows 11 Home & Pro)
Windows 11 24H2/25H2/26H2 enables Device Encryption in a suspended state by default, displaying an exclamation icon on C: in Disk Management.

Option A: Turn Off Pre-Encryption Completely
DOS
manage-bde -status C:
manage-bde -off C:
Option B: Complete Activation & Export Recovery Key to Desktop
DOS
manage-bde -protectors -add C: -RecoveryPassword
manage-bde -protectors -get C: > "%UserProfile%\Desktop\BitLocker-Recovery-Key.txt"
manage-bde -on C:
👉 Full guide for Home & Pro editions: BitLocker Waiting for Activation: 6 Fixes.

4. Force Delete C:\Windows.old (Fixes "Access Denied" / TrustedInstaller)
If Disk Cleanup (cleanmgr) leaves behind stub files inside C:\Windows.old, run these three commands in an Elevated Command Prompt:

DOS
takeown /F "C:\Windows.old" /A /R /D Y
icacls "C:\Windows.old" /T /C /grant Administrators:F SYSTEM:F
rd /S /Q "C:\Windows.old"
👉 See all 5 safe cleanup methods: Delete Windows.old & Free Up Disk Space.

5. Fix Windows + V Clipboard History & Frozen Taskbar
DOS
:: Re-enable Clipboard History Policy in Registry
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v AllowClipboardHistory /t REG_DWORD /d 1 /f
reg add "HKCU\Software\Microsoft\Clipboard" /v EnableClipboardHistory /t REG_DWORD /d 1 /f

:: Restart Clipboard Monitor & Windows Explorer Shell
taskkill /F /IM rdpclip.exe 2>nul
start rdpclip.exe
taskkill /F /IM SearchHost.exe 2>nul
taskkill /F /IM StartMenuExperienceHost.exe 2>nul
taskkill /F /IM explorer.exe & start explorer.exe
👉 Read the complete guides: Windows + V Not Working | Windows 11 Taskbar Freezing.

🎮 Other Open Web Utilities by AxeeTech
Alongside our Windows diagnostic scripts, we maintain fast, client-side calculators for gamers and creators:

All AxeeTech Interactive Calculators & Tools

DnD 5e Point Buy Ability Score Calculator

Minecraft 2D & 3D Shape Plotter

V-Bucks to USD Converter & Valorant Points (VP) to USD Calculator

About AxeeTech & Our Hardware Testing Standards

📜 License
Released under the MIT License. Free to inspect, modify, and use for personal or enterprise system administration. Maintained by AxeeTech.


---

## Step 3: Add 1 Actual `.bat` File to the Repo So GitHub Marks It as a Real Code Project

When you add at least one real `.bat` or `.ps1` file alongside `README.md`, GitHub automatically tags your repository with the **`Batchfile` / `PowerShell` language bar**, which helps it rank in GitHub's internal search and Google's code search.

1. In your repository, click **"Add file" -> "Create new file"**.
2. Name the file: **`AxeeTech-Windows-11-Repair-Menu.bat`**
3. Paste this clean, interactive CLI menu script inside and click **Commit changes**:

```bat
@echo off
title AxeeTech Windows 10/11 Native Repair Utility (https://axeetech.com)
color 0B

:: Check for Administrator Privileges
net session >nul 2>&1
if %errorLevel% neq 0 (
    echo [!] ERROR: Please Right-Click this script and select "Run as administrator".
    echo.
    pause
    exit /b
)

:MENU
cls
echo ===========================================================================
echo   AXEETECH WINDOWS 10 ^& 11 NATIVE REPAIR UTILITY (https://axeetech.com)
echo ===========================================================================
echo.
echo   [1] Check ^& Re-Enable Windows Recovery Environment (WinRE)
echo   [2] Unlock Hidden Power Plans (Ultimate Performance + S0 Bypass)
echo   [3] Check BitLocker / Device Encryption Status on C:
echo   [4] Fix Windows+V Clipboard History ^& Restart Explorer Taskbar
echo   [5] Run DISM RestoreHealth + SFC System File Checker
echo   [6] Open AxeeTech Interactive Web Tools in Browser
echo   [0] Exit
echo.
set /p choice="Select an option [0-6]: "

if "%choice%"=="1" goto WINRE
if "%choice%"=="2" goto POWER
if "%choice%"=="3" goto BITLOCKER
if "%choice%"=="4" goto CLIPBOARD
if "%choice%"=="5" goto DISMFIX
if "%choice%"=="6" goto WEBSITE
if "%choice%"=="0" exit /b
goto MENU

:WINRE
echo.
reagentc /info
reagentc /setreimage /path C:\Windows\System32\Recovery
reagentc /enable
reagentc /info
pause
goto MENU

:POWER
echo.
reg add "HKLM\System\CurrentControlSet\Control\Power" /v PlatformAoAcOverride /t REG_DWORD /d 0 /f
powercfg -duplicatescheme e9a42b02-d5df-448d-aa00-03f14749eb61
powercfg -duplicatescheme 8c5e7fda-e8bf-4a96-9a85-a6e23a8c635c
powercfg /list
echo.
echo [OK] Power plans unlocked! Reboot required if S0 Modern Standby was active.
pause
goto MENU

:BITLOCKER
echo.
manage-bde -status C:
pause
goto MENU

:CLIPBOARD
echo.
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\System" /v AllowClipboardHistory /t REG_DWORD /d 1 /f
reg add "HKCU\Software\Microsoft\Clipboard" /v EnableClipboardHistory /t REG_DWORD /d 1 /f
taskkill /F /IM rdpclip.exe 2>nul
start rdpclip.exe
taskkill /F /IM explorer.exe & start explorer.exe
echo [OK] Clipboard and Explorer shell restarted!
pause
goto MENU

:DISMFIX
echo.
DISM /Online /Cleanup-Image /RestoreHealth
sfc /scannow
pause
goto MENU

:WEBSITE
start https://axeetech.com/category/tools/
goto MENU
