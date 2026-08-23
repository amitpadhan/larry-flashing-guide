# Official LineageOS Installation Guide & User Experience
### Device: OnePlus Nord CE 3 Lite 5G
### Codename: `larry` | Supported Models: `CPH2467`, `CPH2465`
### Target Firmware Base: Stock Android 15 (`CPH2467_15.0.0.1810`)

> Source Reference: [LineageOS Wiki - Install LineageOS on OnePlus Nord CE 3 Lite 5G (larry)](https://wiki.lineageos.org/devices/larry/install/variant1)

---

> 🚨 **CRITICAL WARNING FOR OXYGENOS 15 USERS (`CPH2467_15.0.0.1810`):**
> 
> Your phone has **hardware Anti-Rollback (ARB) Qfuse protection** burned into the bootloader.
> 1. **NEVER flash older/archived builds** (e.g. LineageOS 21 or 22 from 2024/2025). You **must only flash the latest active LineageOS 23.2 nightly**.
> 2. **NEVER attempt to downgrade** to Android 13 or 14 rollback packages. It will trigger an instant, unrecoverable hard brick (Qualcomm EDL 9008 mode).
> 3. **NEVER skip Step 4 (`copy-partitions`)**. Inactive slots with mismatched firmware will brick the device.
> 4. **NEVER relock the bootloader** on a custom ROM.

---

## 1. Basic Requirements & Verification

1. **Read all instructions** through at least once before starting.
2. **Setup ADB and Fastboot:** Make sure your computer has the latest [Android SDK Platform-Tools](https://developer.android.com/tools/releases/platform-tools).
3. **Check Model Number (Exact Match Required):**
   - Go to **Settings > About Device > Version**.
   - Ensure your model is **`CPH2467`** (or `CPH2465`).
4. **Stock Firmware Check:**
   - Your device is running stock **Android 15** firmware (`CPH2467_15.0.0.1810`).
5. **Verify Hardware Functions on Stock OS:**
   - Make sure you can place/receive phone calls, send/receive SMS, and use mobile data/VoLTE/VoWiFi before starting.
6. **Remove Accounts (FRP Prevention):**
   - Go to **Settings > Passwords & Accounts** and remove all Google and HeyTap/OnePlus accounts to avoid Factory Reset Protection lockouts.
7. **Backup:**
   - Back up all important data to a PC or external storage. Unlocking the bootloader will completely erase the device.

---

## 2. Complete Download Links Directory

Download each of the required files from these official links and place them in your `platform-tools` folder:

| Component | Exact File | Direct Download Source |
| :--- | :--- | :--- |
| **ADB & Fastboot** | `platform-tools` (Windows / Mac / Linux) | [Official Google Android SDK Platform-Tools](https://developer.android.com/tools/releases/platform-tools) |
| **Kernel Image** | `boot.img` | [Official LineageOS Downloads for larry](https://download.lineageos.org/devices/larry) |
| **Device Tree Overlay** | `dtbo.img` | [Official LineageOS Downloads for larry](https://download.lineageos.org/devices/larry) |
| **Verified Boot** | `vbmeta.img` | [Official LineageOS Downloads for larry](https://download.lineageos.org/devices/larry) |
| **Lineage Recovery** | `vendor_boot.img` | [Official LineageOS Downloads for larry](https://download.lineageos.org/devices/larry) |
| **LineageOS ROM** | `lineage-23.2-*-nightly-larry-signed.zip` | [Official LineageOS Downloads for larry](https://download.lineageos.org/devices/larry) |
| **Slot Sync Tool** | `copy-partitions-20220613-signed.zip` | [Official LineageOS Mirrorbits Direct Download](https://mirrorbits.lineageos.org/tools/copy-partitions-20220613-signed.zip) |
| **Google Apps (Optional)** | `MindTheGapps-16.0.0-arm64-*.zip` | [Official MindTheGapps Repository / Releases](https://wiki.lineageos.org/gapps) <br> *(or [GitHub MindTheGapps Mirror](https://github.com/MindTheGapps/MindTheGapps/releases))* |
| **Camera Port (Optional)** | GCam APK (Google Camera) | [CelsoAzevedo GCam Hub](https://www.celsoazevedo.com/files/android/google-camera/) |

---

## Section 1: Unlocking the Bootloader

> ⚠️ **Warning:** Unlocking the bootloader will erase all data on your device!

1. Enable **Developer Options**:
   - Open **Settings > About Device > Version**, then tap **Build Number** 7 times.
2. Enable Developer Settings:
   - Go to **Settings > Additional Settings > Developer Options**.
   - Enable **OEM Unlocking** and **USB Debugging**.
3. Connect the phone to your PC using a USB cable.
4. On your PC terminal, reboot to the bootloader:
   ```bash
   adb -d reboot bootloader
   ```
   *(Alternatively, power off the phone, then press and hold **Volume Up + Volume Down + Power**).*
5. Verify the device is detected in fastboot mode:
   ```bash
   fastboot devices
   ```
6. Unlock the bootloader:
   ```bash
   fastboot flashing unlock
   ```
7. On the device screen, use the **Volume Buttons** to navigate to **UNLOCK THE BOOTLOADER** and press the **Power Button** to confirm.
8. The device will reset and reboot. Go through initial setup without signing in, and **re-enable USB Debugging** in Developer Options.

---

## Section 2: Flashing Additional Partitions

> ⚠️ **Warning:** This platform requires additional partitions to be flashed for recovery to work properly.

1. Power off the device, and boot it into bootloader mode:
   - Hold **Volume Up + Volume Down + Power** until the fastboot screen appears.
   - Or run: `adb -d reboot bootloader`
2. Flash the downloaded partition images:
   ```bash
   fastboot flash boot boot.img
   fastboot flash dtbo dtbo.img
   fastboot flash vbmeta vbmeta.img
   ```
3. Reboot back to bootloader:
   ```bash
   fastboot reboot bootloader
   ```

---

## Section 3: Installing Lineage Recovery using Fastboot

> ⚠️ **Important:** On this device, recovery is inside `vendor_boot`. Do not attempt to flash to a `recovery` partition.

1. Flash the Lineage Recovery image:
   ```bash
   fastboot flash vendor_boot vendor_boot.img
   ```
2. Reboot into recovery:
   - On the phone, use the **Volume Buttons** to highlight **Recovery Mode** on screen.
   - Press the **Power Button** to select.
3. Verify that your screen shows the **LineageOS Recovery** logo.

---

## Section 4: Ensuring All Firmware Partitions Are Consistent (`copy-partitions`)

> ⚠️ **Warning:** In some cases, the inactive slot can be unpopulated or contain mismatched firmware, which can cause a hard-brick. This step copies the active slot to the inactive slot.

1. On your phone in Lineage Recovery:
   - Select **Apply update**
   - Select **Apply from ADB**
2. On your PC terminal, execute:
   ```bash
   adb -d sideload copy-partitions-20220613-signed.zip
   ```
3. Once completed, on the phone select **Advanced** > **Reboot to recovery**.

---

## Section 5: Installing LineageOS from Recovery

1. Format data and wipe previous system state:
   - In Lineage Recovery, select **Factory Reset**.
   - Select **Format data / factory reset** and confirm.
2. Return to the main menu.
3. Enable ADB sideload on the phone:
   - Select **Apply update** > **Apply from ADB**.
4. Sideload the LineageOS ROM package from your PC:
   ```bash
   adb -d sideload lineage-23.2-xxxxxxxx-nightly-larry-signed.zip
   ```
   *(Replace with the actual name of your downloaded LineageOS `.zip` file).*

> ℹ️ **ADB Output Note:**  
> In many cases, the terminal output will stop at **~47%** and show:  
> `adb: failed to read command: Success` or `Total xfer: 1.00x`.  
> **This is normal.** Check your phone's screen: if it shows `Script succeeded result was [1]` or returns to the menu with status 0, the ROM installation succeeded.

---

## Section 6: Installing Add-Ons (Google Apps / GApps)

> If you want a Google-free experience, skip this section and proceed directly to Section 7.  
> ⚠️ **Warning:** If you want GApps, you **MUST** install them now **BEFORE** booting into LineageOS for the first time.

1. After the ROM installation completes, Recovery will prompt:  
   *"Reboot to recovery to install add-ons?"*  
   - Select **Yes**.
2. When the phone reboots back into recovery:
   - Select **Apply update** > **Apply from ADB**.
3. On your PC terminal, run:
   ```bash
   adb -d sideload MindTheGapps-16.0.0-arm64-xxxxxx.zip
   ```
4. If the phone displays *"Signature verification failed"*:
   - Click **Yes** to continue (this prompt is expected because add-ons are not signed with LineageOS private keys).

---

## Section 7: All Set & First Boot

1. Click the back arrow in the top left corner of the recovery screen.
2. Select **Reboot system now**.
3. Unplug the USB cable.

> ⏱️ **First Boot Duration:** The initial boot may take **5 to 10 minutes**. Let the system finish initialization.

---

## 3. Real User Reviews & In-Depth Experience Analysis

Feedback gathered from long-term daily drivers on **XDA Forums**, **Reddit (r/LineageOS & r/OnePlus)**:

### 🌟 Pros & User Praise
1. **Fluid 120Hz & Zero UI Lag:**
   - Users universally note that the phone feels significantly faster than OxygenOS 15. The Snapdragon 695 chipset no longer throttles on basic animations because background bloatware (HeyTap services, cloud analytics, theme store daemons) is gone.
2. **Superior Battery Life:**
   - Users report **7.5 to 9.5 hours of Screen-On-Time (SoT)** on typical Wi-Fi / 5G mixed usage. Standby overnight drain drops from ~6-8% on stock down to ~1-2% on LineageOS.
3. **Pristine Stock Android Experience:**
   - Complete absence of ads, promotional lock-screen feeds (Glance), or pre-installed junk apps. Clean Material You theming and responsive quick settings.
4. **Hardware Reliability:**
   - 5G dual SIM, VoLTE, VoWiFi, Wi-Fi 5GHz, GPS navigation, 3.5mm headphone jack, and the fast side-mounted fingerprint scanner all function flawlessly.

---

### ⚠️ Known Trade-Offs & Community Workarounds

| Feature | Stock OxygenOS 15 | LineageOS 23.2 | Community Workaround / Solution |
| :--- | :--- | :--- | :--- |
| **Camera Quality** | Proprietary 108MP post-processing | Basic open-source camera | Install **GCam (Google Camera port)** from CelsoAzevedo. Delivers equal or better HDR and night mode than stock. |
| **Fast Charging** | 67W SUPERVOOC animation | Standard USB-PD / Fast Charge | Fast charging works at high speed (~30-35W+ real-world peak), but without the proprietary animated SUPERVOOC lock screen ring. |
| **Banking / UPI Apps** | Passes SafetyNet / Play Integrity | Unlocked bootloader flags | Use standard Magisk + PlayIntegrityFix modules if your specific banking apps enforce hardware keystore checks. |
| **OTA Updates** | Infrequent & often bloated | Clean weekly OTA updates | Built-in LineageOS Updater in Settings allows 1-click seamless background updates without losing data. |
