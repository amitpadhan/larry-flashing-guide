# OnePlus Nord CE 3 Lite 5G Stock Recovery & Unbrick Guide
### Device: OnePlus Nord CE 3 Lite 5G (`CPH2467` / `larry`)
### Purpose: Restore 100% Stock OxygenOS if flashing fails, bootloops, or you wish to revert.

---

## 1. Complete Recovery Download Links Directory

| Tool / Firmware Component | Purpose | Direct Download Link |
| :--- | :--- | :--- |
| **Android SDK Platform Tools** | `adb` and `fastboot` command line | [Google Platform-Tools](https://developer.android.com/tools/releases/platform-tools) |
| **Payload Dumper Tool** | Extracts stock `.img` files from OxygenOS `.zip` | [payload-dumper-go Releases](https://github.com/ssut/payload-dumper-go/releases) |
| **Official Stock OxygenOS 15 Full Zip** | Full stock firmware package | [Oxygen Updater (Google Play)](https://play.google.com/store/apps/details?id=com.arjanvlek.oxygenupdater) <br> *(or [OnePlus Community ROM Mirrors](https://community.oneplus.com/))* |
| **Qualcomm USB Driver (9008)** | PC Driver for emergency EDL mode | [Qualcomm HS-USB QDLoader 9008 Driver](https://developer.qualcomm.com/) |
| **Oplus / EDL Flasher (Linux/Win)** | Raw block flasher for unbricking | [bkerler edl tool (GitHub)](https://github.com/bkerler/edl) |

---

## Table of Contents
1. [Overview & Triage (Which state is your phone in?)](#2-overview--triage-which-state-is-your-phone-in)
2. [Scenario A: Soft Brick / Fastboot Accessible (Fastest & Safest)](#scenario-a-soft-brick--fastboot-accessible-fastest--safest)
3. [Scenario B: Bootloop in Recovery / Stock Recovery Sideload](#scenario-b-bootloop-in-recovery--stock-recovery-sideload)
4. [Scenario C: Hard Brick / Black Screen (Qualcomm EDL 9008 Mode)](#scenario-c-hard-brick--black-screen-qualcomm-edl-9008-mode)
5. [How to Relock the Bootloader (Return to 100% Factory State)](#5-how-to-relock-the-bootloader-return-to-100-factory-state)
6. [Critical Safety Rules & Common Traps](#6-critical-safety-rules--common-traps)

---

## 2. Overview & Triage (Which state is your phone in?)

| State / Symptom | Where can you boot? | Recommended Recovery Method |
| :--- | :--- | :--- |
| **Bootloop to Fastboot** | Fastboot / Bootloader screen | **Scenario A** (Fastboot stock images flash) |
| **Stuck in Recovery Mode** | Lineage / Stock Recovery | **Scenario B** (Recovery format & sideload) |
| **Black screen / Qualcomm Port** | PC detects `Qualcomm HS-USB QDLoader 9008` | **Scenario C** (EDL Unbrick tool) |

---

## Scenario A: Soft Brick / Fastboot Accessible (Fastest & Safest)

Use this method if the phone can still enter Fastboot mode (holding **Volume Up + Volume Down + Power**).

### Step-by-Step Restoration:

1. **Extract Stock Images from OxygenOS payload.bin:**
   - Download your stock OxygenOS full zip using [Oxygen Updater](https://play.google.com/store/apps/details?id=com.arjanvlek.oxygenupdater).
   - Extract `payload.bin` from inside the zip file.
   - Run [payload-dumper-go](https://github.com/ssut/payload-dumper-go/releases):
     ```bash
     payload-dumper-go payload.bin
     ```
   - It will output all stock partition `.img` files (`boot.img`, `dtbo.img`, `vbmeta.img`, `vendor_boot.img`, `system.img`, `vendor.img`, `product.img`, `my_stock.img`, etc.) into an `extracted_...` folder.

2. **Boot the phone into Fastboot Mode:**
   - Power off the phone.
   - Hold **Volume Up + Volume Down + Power** until the Fastboot screen appears.
   - Verify connection on PC:
     ```bash
     fastboot devices
     ```

3. **Flash Core Boot Partitions:**
   ```bash
   fastboot flash boot boot.img
   fastboot flash dtbo dtbo.img
   fastboot flash vbmeta vbmeta.img
   fastboot flash vendor_boot vendor_boot.img
   ```

4. **Reboot into FastbootD (User Space Fastboot for Dynamic Partitions):**
   ```bash
   fastboot reboot fastboot
   ```
   *(The screen will change to a screen with options including "Enter Fastboot Mode" or language selection).*

5. **Flash Dynamic Logical Partitions:**
   ```bash
   fastboot flash system system.img
   fastboot flash system_ext system_ext.img
   fastboot flash product product.img
   fastboot flash vendor vendor.img
   fastboot flash odm odm.img
   ```

6. **Wipe User Data & Reboot:**
   ```bash
   fastboot -w
   fastboot reboot
   ```
   *Your phone will reboot directly into the official OxygenOS setup wizard.*

---

## Scenario B: Bootloop in Recovery / Stock Recovery Sideload

If you still have Lineage Recovery (or flashed stock `vendor_boot.img`), you can perform a clean factory reset or sideload the stock firmware.

1. Boot into Recovery:
   - Power off phone, hold **Volume Up + Volume Down + Power**, choose **Recovery Mode**.
2. Perform a clean wipe:
   - Select **Factory Reset** > **Format data / factory reset** > confirm **Format data**.
3. Re-sideload firmware:
   - Select **Apply update** > **Apply from ADB**.
   - On PC terminal:
     ```bash
     adb -d sideload OxygenOS_Full_OTA.zip
     ```
4. Once completed, tap **Reboot system now**.

---

## Scenario C: Hard Brick / Black Screen (Qualcomm EDL 9008 Mode)

If your screen is completely black, does not turn on, vibrates repeatedly, or your PC shows **`Qualcomm HS-USB QDLoader 9008`** in Device Manager / `lsusb`:

### 1. Identify EDL Port:
- **On Windows:** Open Device Manager > Ports (COM & LPT) > Look for `Qualcomm HS-USB QDLoader 9008`.
- **On Linux:** Run `lsusb` in terminal and check for `05c6:9008 Qualcomm, Inc. Gobi Wireless Modem / Software Download Mode`.

### 2. Enter EDL Mode Manually (if stuck in a loop):
1. Unplug phone from PC.
2. Hold **Volume Up + Volume Down + Power** for 15–20 seconds until the phone completely turns off.
3. Hold **Volume Up + Volume Down** together, and while holding them, **plug the USB cable into the PC**.
4. The PC will chime and recognize the EDL 9008 device.

### 3. Flash via OPlus / Qualcomm Flasher:
1. Download the dedicated **CPH2467 OFP / EDL Flash Package** from the OnePlus community firmware repository.
2. Use **OplusFlashTool** (or [bkerler edl](https://github.com/bkerler/edl) python tool on Linux).
3. Select your model (`CPH2467`), load the OFP package, and click **Start**.
4. The tool will write raw block images directly to flash memory.
5. Once complete, unplug and hold **Power** for 10 seconds to boot the stock phone.

---

## 5. How to Relock the Bootloader (Return to 100% Factory State)

> ⚠️ **CRITICAL WARNING:** **NEVER** run `fastboot flashing lock` while a Custom ROM (LineageOS) or custom recovery is installed. Doing so will permanently hard-brick the device!  
> **Only** relock the bootloader after you have fully flashed stock OxygenOS and verified that the stock OS boots and runs normally.

### Steps to Relock:
1. Ensure the phone is running **100% unmodified stock OxygenOS**.
2. Enable USB debugging in Developer Options.
3. Reboot to bootloader:
   ```bash
   adb -d reboot bootloader
   ```
4. Verify connection:
   ```bash
   fastboot devices
   ```
5. Execute bootloader lock command:
   ```bash
   fastboot flashing lock
   ```
6. On the phone screen, use the **Volume Buttons** to select **LOCK THE BOOTLOADER** and press the **Power Button** to confirm.
7. The phone will perform a final factory reset and reboot back to factory condition with Knox/TEE/SafetyNet fully certified.

---

## 6. Critical Safety Rules & Common Traps

| Trap / Action | Why it's dangerous | What to do instead |
| :--- | :--- | :--- |
| **Downgrading to Android 13/14 roll-back package from OxygenOS 15** | OxygenOS 15 blows hardware Anti-Rollback (ARB) fuses. Downgrading old bootloaders triggers an unrecoverable hard brick. | Always restore to **OxygenOS 15** stock firmware (`CPH2467_15.0.0.1810` or newer). |
| **Relocking bootloader on Custom ROM** | The device cannot verify the signature of LineageOS and will refuse to boot (`dm-verity corrupted` loop). | Keep bootloader unlocked until stock OxygenOS is verified working. |
| **Flashing firmware from another regional variant (e.g. CPH2513)** | The modem and radio partitions differ and will corrupt IMEI / baseband. | Only flash firmware specifically for **`CPH2467`** (or European `CPH2465`). |
