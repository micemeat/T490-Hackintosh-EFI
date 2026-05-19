# Lenovo ThinkPad T490 OpenCore

## Changelog

### 2026-03-31: Running macOS Tahoe without root patches

To avoid bricked installs due to OCLP-Mod incompatibility with macOS 26.4, I've decided to not rely on root patches and adjusted the config accordingly. But this also means: Itlwm is required for WiFi and VooodooHDA is required for audio.

**Config Changes**:

- Updated OpenCore and Drivers to 1.0.8 Nightly
- Kernel/Block: 
	- Disabled Blocking of `com.apple.iokit.IOSkywalkFamily`
- Kernel/Add
	- Disabled `IOSkywalkFamily.kext`
	- Disabled `IO80211FamilyLegacy.kext` 
	- Disabled `AirportItlwm_Sequoia.kext`
	- Enabled `itlwm.kext` for macOS Sequoia+

**Instructions**

- Disable Gatekeeper
- Install [Heliport](https://github.com/sambow23/HeliPort) 
- Build and Install [VoodooHDA Tahoe](https://github.com/chris1111/VoodooHDA-Tahoe) to enable Audio

> [!NOTE]
> 
> If you want to use AirportItlwm for Wifi and AppleALC for Audio, you need to invert the Wifi-related kext settings and apply root patches [OCLP-Plus](https://github.com/YBronst/OCLP-Plus/releases). It's a newer version of OCLP-Mod which has an English GUI.

### 2026-02-18: Getting back some performance
After renewing the thermal paste – I am testing liquid metal now – and running a Geekbench 5 test in Sequoia, I've noticed that the multicore score was about 1.000 points lower than expected. So I generated a new the CPUFriendDataProvider kext and everything was back to normal. Funny thing, this macOS: the newer the OS is, the worse it performs…

**CONFIG Changes:**

- **DeviceProperties**:
	- Removed some unnecessary entries from Audio Device
- **Kernel/Add**:
	- Generated a new `CPUFriendDataProvider.kext` with [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend)

> [!CAUTION]
>
> - DON'T INSTALL MACOS TAHOE 26.4 beta yet! Patching Audio will leave it in a bricked state!

> [!IMPORTANT]
>
> - DON'T INSTALL MACOS TAHOE 26.4 beta yet. Patching Audio will leave it in a bricked state!
> - To install macOS Tahoe, download the full installer and install it on a separate APFS Volume!
> - To enable Audio and Wi-Fi in macOS Tahoe, you need to apply Root Patches with [**OCLP-Mod**](https://github.com/laobamac/OCLP-Mod/releases) in Post-Install. Connect to LAN to download OCLP-Mod. OCLP-Mod also requires acces to the internet to download additional files (Kernel Debugging Kit for example)!
> - Don't update the following kexts since they are slimmed variants for this system only:
> 	- AppleALC
> 	- AirportItlmwm
> 	- Itlwm
> 	- IntelBluetoothFirmware

### 2026-02-05: Fixed Shutdown Stalls, Improved ACPI _OSI Handling, Updated USB Map & Realtek Cardreader Kexts

* Implemented a [**new method**](https://github.com/5T33Z0/OC-Little-Translated/tree/main/Content/01_Adding_missing_Devices_and_enabling_Features/SSDT-OSDW) for cleaner and more reliable `_OSI` handling on macOS → improves shutdown stability and overall system responsiveness.
* Updated ACPI tables to route OSI checks through the new `OSDW` method.
* Restored the previous modified USB port map kext to resolve shutdown stalls caused by internal USB device teardown.

**CONFIG Changes:**

* **ACPI/Add**
	* Added `SSDT-OSDW.aml`
* **Booter/MMIO Whitelist**
	* Removed `MMIO Whitelist` entries → no longer required after OC debug bootlog analysis.
* **Kernel/Add**
	* Added updated `RealtekCardReader.kext` including an important [**PR**](https://github.com/0xFireWolf/RealtekCardReader/pull/61) not merged into the main repo.
	* Reinstated previous modified `USBMap.kext` and set `HS08` (Integrated Camera) port type to internal (`255`).
	* Removed `USBToolbox.kext` and `UTBMap.kext` &rarr; now superfluous.
* **Kernel/Quirks**
	* Disabled `DevirtualiseMmio` &rarr; unnecessary after analysis.
	* Enabled `DisableRtcChecksum` &rarr; Prevents BIOS Checksum errors after recovering from Hibernation Mode 25

> [!IMPORTANT]
>
> - To install macOS Tahoe, download the full installer and install it on a separate APFS Volume!
> - To enable Audio and Wi-Fi in macOS Tahoe, you need to apply Root Patches with [**OCLP-Mod**](https://github.com/laobamac/OCLP-Mod/releases) in Post-Install. Connect to LAN to download OCLP-Mod. OCLP-Mod also requires acces to the internet to download additional files (Kernel Debugging Kit for example)!
> - Don't update the following kexts since they are slimmed variants for this system only:
> 	- AppleALC
> 	- AirportItlmwm
> 	- Itlwm
> 	- IntelBluetoothFirmware

### 2026-01-03: Enabled `AirportItlwm` in macOS Tahoe!

- Updated OpenCore to v1.0.7 (nightly)
- Updated Kexts and Drivers
- Tested with macOS 14.8.4 and macOS Tahoe 26.3 beta 
- **CONFIG Changes**:
	- **DeviceProperties**:
		- Disabled `IOName` spoof, so Broadcom WiFi patches are not triggered in OCLP Mod 3.1.x.
	- **Kernel**:
		- **Add**:
			- Disabled `Itlwm.kext`
			- Removed `MaxKernel` entries for:
				- `IOSkywalkFamily.kext`
				- `IO80211FamilyLegacy.kext`
				- `AirportItlwm_Sequoia.kext`
				- Replaced `AirportItlwm_Sequoia.kext` by version for Ventura so Wi-Fi works
		- **Block**:
			- Removed `MaxKernel` entry from `com.apple.iokit.IOSkywalkFamily`
		- **NVRAM**
			- Added `-amfipassbeta` boot-arg so macOS Tahoe boots
	 	
> [!IMPORTANT]
>
> - To install macOS Tahoe, download the full installer and install it on a separate APFS Volume!
> - To enable Audio and Wi-Fi in macOS Tahoe, you need to apply Root Patches with OCLP Mod in Post-Install. Connect to LAN to download OCLP-Mod. OCLP-Mod also requires acces to the internet to download additional files (Kernel Debugging Kit for example)!
> - Don't update the following kexts since they are slimmed variants for this system only:
> 	- AppleALC
> 	- AirportItlmwm
> 	- Itlwm
> 	- IntelBluetoothFirmware

### 2025-12-30: Fresh for 2026…
- Updated OpenCore to v1.0.7 (nightly)
- Updated Drivers and Kexts
- **ACPI**
	- **Add**
		- Disabled `DMAR.aml`&rarr; not necessary
	- **Delete**
		- Disabled `Drop OEM DMAR Table`&rarr; not necessary
- **KERNEL**:
	- **Add**:
		- Deleted `USBMap.kext`
		- Added `USBToolBox.kext` and `UTBMap.kext` as a replacement to fix issues with macOS Tahoe installation via USB flash drive (&rarr; see[ Issue 68](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore/issues/68))
		- Re-Added slimmed version of `AppleAlC`
		- Compiled new versions of `AirportItlm`, `Itlwm` and `IntelBluetoothFirmware`
		- Added newer version of `RealtekCardReader`from Baio1977’s Fork (it doesn’t fix the underlying issue, though)
	- **Quirks**
		-  Disabled `DisableIOMapper` &rarr; otherwise AppleVTD does not work. Re-enable it, if LAN does not work!
- **NVRAM**
	- Added `-rtsfbeta` to load `RealtekCardReaderFriend.kext` on macOS 12+

> [!NOTE]
> 
> - **macOS Tahoe Notes**
> 	- Don't install macOS 26 via System Update – download the installer and install it on a new APFS volume.
> 	- Apple Root Patches with [**OCLPMod**](https://github.com/laobamac/OCLP-Mod/releases) to enable Audio and Bluetooth (&rarr; [Instruction](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore?tab=readme-ov-file#macos-tahoe-fixes-audio-and-bluetooth))
>  - WiFi is using `itlwm.kext` so you need to install [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) app to connect to WiFi APs.
>	- If LAN does not work, enable Kernel Quirk `DisableIoMapper`.

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-12-20: Maintenance Update
- Updated OpenCore to v1.0.7 (nightly)
- Updated Drivers and Kexts

> [!NOTE]
> 
> - **macOS Tahoe Notes**
> 	- Don't install macOS 26 via System Update – download the installer and install it on a new APFS volume.
> 	- Apple Root Patches with [**OCLPMod**](https://github.com/laobamac/OCLP-Mod/releases) to enable Audio and Bluetooth (&rarr; [Instruction](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore?tab=readme-ov-file#macos-tahoe-fixes-audio-and-bluetooth))
>  - WiFi is using `itlwm.kext` so you need to install [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) app to connect to WiFi APs.
>	- If LAN does not work, enable Kernel Quirk `DisableIoMapper`.

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-11-01: Version Bump
- Updated OpenCore to v1.0.7 (nightly)
- Updated Drivers and Kexts

> [!NOTE]
> 
> - **macOS Tahoe Notes**
> 	- Don't install macOS 26 via System Update – download the installer and install it on a new APFS volume.
> 	- Apple Root Patches with [**OCLPMod**](https://github.com/laobamac/OCLP-Mod/releases) to enable Audio and Bluetooth (&rarr; [Instruction](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore?tab=readme-ov-file#macos-tahoe-fixes-audio-and-bluetooth))
>  - WiFi is using `itlwm.kext` so you need to install [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) app to connect to WiFi APs.
>	- If LAN does not work, enable Kernel Quirk `DisableIoMapper`.

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

<details><summary><strong>Previous Changes</strong> (Toggle me!)</summary>

### 2025-11-01: Theme tweaks
- Updated OpenCore to v1.0.6 (nightly)
- Updated Drivers and Kexts
- GoldenGate Theme:
	- Added `Apple26.icns` for macOS Tahoe 
	- Replaced `Windows.icns` in GoldenGate Theme by the one from GoldenGate Extended v2.0 by [**HJebbour**](https://github.com/HJebbour/GoldenGateExt-OpenCore-Theme)

> [!NOTE]
> 
> - **macOS Tahoe Notes**
> 	- Don't install macOS 26 via System Update – download the installer and install it on a new APFS volume.
> 	- Apple Root Patches with [**OCLPMod**](https://github.com/laobamac/OCLP-Mod/releases) to enable Audio and Bluetooth (&rarr; [Instruction](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore?tab=readme-ov-file#macos-tahoe-fixes-audio-and-bluetooth))
>  - WiFi is using `itlwm.kext` so you need to install [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) app to connect to WiFi APs.
>	- If LAN does not work, enable Kernel Quirk `DisableIoMapper`.

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-09-26: Maintenance Update (tested with macOS Sonoma to Tahoe)

- Updated OpenCore to v1.0.6 (nightly)
- Updated Drivers and Kexts
- Tested with macOS :14.8 (23J19), 15.7.1 (24G309) and 26.1 Beta (25B5042k)

> [!NOTE]
> 
> - **macOS Tahoe Notes**
> 	- Don't install macOS 26 via System Update – download the installer and install it on a new APFS volume.
> 	- Apple Root Patches with [**OCLPMod**](https://github.com/laobamac/OCLP-Mod/releases) to enable Audio and Bluetooth (&rarr; [Instruction](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore?tab=readme-ov-file#macos-tahoe-fixes-audio-and-bluetooth))
>  - WiFi is using `itlwm.kext` so you need to install [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) app to connect to WiFi APs.
>	- If LAN does not work, enable Kernel Quirk `DisableIoMapper`.

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-07-22: Fixed post-sleep performance issues ([Issue #44](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore/issues/44))

Many thanks to [medkintos](https://github.com/medkintos) for figuring this out!

- Updated OpenCore to v1.0.6 (nightly)
- Updated Drivers and Kexts
- **DRIVERS**:
	- Added `DisablePROCHOT.efi` &rarr; disables Bi-Directional Prochot before boot
- **KEXTS**:
	- Added `SimpleMSR.kext` &rarr; disables Bi-Directional Prochot after waking from S3 Sleep
	- Refined USB Port Mapping Kext

> [!NOTE]
> 
> - **macOS Tahoe**
> 	- Don't install macOS 26 via System Update – download the installer and install it on a new APFS volume.
> 	- Bluetooth does not work currently.
> 	- WiFi is using `itlwm.kext` so you need to install [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) app to connect to WiFi APs.
>	- If LAN does not work, enable Kernel Quirk `DisableIoMapper`.

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-06-15: macOS Tahoe Support

- Updated OpenCore and Drivers
- Updated Kexts
- **CONFIG**
	- **DeviceProperties**: 
		- Updated Framebuffer Patch for attaching 2 displays while in docked mode
		- Enabled GUC Firmware for iGPU
	- **Kernel/Add**: 
		- Updated `AdvancedMap` for macOS Tahoe
		- Updated `MinKernel` and `MaxKernel` settings

> [!NOTE]
> 
> - **macOS Tahoe issues**
> 	- Don't install macOS 26 via System Update – download the installer and install it on a new APFS volume.
> 	- Bluetooth does not work currently.
> 	- WiFi is using itlwm.kext so you need to install HeliPort App to connect to WiFi APs.
>	- If LAN does not work, enable Kernel Quirk `DisableIoMapper`.

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext


### 2025-05-31: Fixing Windows boot

- **ACPI/Add**: Disabled modified `DMAR` table without reserved Memory regions &rarr; Windows won't boot, if it is injected.
- **Kernel/Quirks**: Disabled `DiableIoMapperMaping` &rarr; Not required

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **Advancedmap** &rarr; Original source is older
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-05-13: More Refinements

- **Framebuffer Patch**:
	- Disabled `framebuffer-unifiedmem`
	- Added `framebuffer-fbmem`
	- Added `framebuffer-stolenmem`
	- &rarr; No more glitches in Apple Logo during boot
- **Kexts**:
	- Deleted Plugin kext from `IO80211FamilyLegacy.kext` &rarr; not needed for Intel Cards. Another 9 MB saved
	- Added `CPUFriend.kext` and `CPUFriendDataProvider.kext`
	- `CPUFriendDataProvider.kext` Configuration (i5-8265U, MacBookPro15,2 SMBIOS):
		- **Low Frequency Mode** (LFM): `0x08` (800 MHz)
		- **Energy Performance Preference** (EPP): `0x90` (Balanced Power Savings). Matches `MacBookPro15,2` for efficient frequency scaling.
		- **Perf Bias**: `0x05` (Balanced). Aligns with MacBookPro15,2 for balanced performance/efficiency.
		- **MacBook Air Energy-Saving Features**: Disabled. Skipped to ensure SMBIOS compatibilit.

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **Advancedmap** &rarr; Original source is older
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-04-23: Addressing high iGPU utilization and BT issues

- **CONFIG**
	- **DeviceProperties**/PciRoot(0x0)/Pci(0x2,0x0)
		- Deleted `rps-control` property &rarr; reduces overall system temeperature (thanks to [amane1234](https://github.com/amane1234))
		- Deleted other unused/disabled iGPU properties
	- **NVRAM**
		- Re-enabled `bluetoothInternalControllerInfo` and `bluetoothExternalDongleFailed` entries in `Add` and `Delete` section so that Bluetooth works

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **Advancedmap** &rarr; Original source is older
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-04-11: Addressing performance issues (bye, bye YogaSMC)

It has almost been 3 years since the last YogaSMC update – and it just doesn't work properly in macOS Sonoma+ any more (check the reported [issue](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore/issues/44) for details). So in order to not make the Laptop perform worse than it is actually capable of, I decided to disable YogaSMC and everything related to it (CPUFriend, ACPI files) and let macOS handle CPU Power Management instead. This actually makes a difference: the system runs faster and smoother overall. Setback: only the important keyboard shortcuts are currently implemented: Volume up/down, Speaker Mute and Brightness Controls.

You can still use YogaSMC if you want to, but don't ask me for help if your system performs sub-optimal!

#### Changes

- Updated OpenCore and Drivers to version 1.0.5 (nightly)
- Tested with macOS 14.7.5 (23H527) and macOS 15.5 Beta (24F5042g)
- **CONFIG**
	- **ACPI/Add**
		- Disabled `SSDT-ECRW.aml` &rarr; only req. for YogaSMC to work
		- Disabled `SSDT-THINK.aml` &rarr; only req. for YogaSMC to work
		- Added `SSDT-T490-KBRD.aml`
	- **ACPI/Patch**
		- Added Patches for Keyboard Shortcuts
	- **Kernel/Add**
		- Disabeled `YogaSMC.kext`
		- Disabeled `CPUFriend.kext`
		- Disabeled `CPUFriendDataProvider.kext`

#### Deployment

- Delete YogaSMC Prefpane (if installed)
- Delete YogaSMC App and disable the Login Item as well (if installed)
- Install EFI
- Add Serial, SMB, etc.
- Reboot 
- RESET NVRAM
- Apply Root Patch for modern WiFi (Sequoia only)
- For more detailed instructions [check my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)!

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **Advancedmap** &rarr; Original source is older
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-01-17: SDCard Reader in Sonoma and Sequoia… sorta…
In order for the SD Card Reader to work in macOS Sonoma and Sequoia, the Micro SD Card has to be inserted into it *before* booting macOS – otherwise it won't be mounted. And once an SD card is ejected, it won't  mount again, after re-inserting it. These kexts haven't been updated in years, so that's why they don't work as expected no more, I guess.

- Updated OpenCore and Drivers to version 1.0.4 (nightly)
- Tested with macOS Sonoma 14.7.3 and macOS Sequoia 15.3 beta 3
- **CONFIG**
	- **DeviceProperties**
		- Changed `AAPL,slot-name` to `build-in`, where appropriate.
	- **Kernel/Add**
		- `RealtekCardReader` and `RealtekCardReaderFriend` kexts &rarr; Deleted `MaxKernel` settings, so they are loaded in macOS Sequoia
		- Updated `YogaSMC.kext` to v 2.0.0 (compiled it myself). I still have to figure out how to compile the corresponding prefpane – it wasn't in the "build" folder…

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)!

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **Advancedmap** &rarr; Original source is older
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-01-14: Fixed screen wake issue and `AppleVTD`
- Updated OpenCore and Drivers to version 1.0.4 (nightly)
- Tested with macOS Sonoma 14.7.3 and macOS Sequoia 15.3 beta 3
- **CONFIG**
	- **DeviceProperties**
		- Fixed an issue where the external screen would not wake after exiting sleep  
	- **Kernel/Quirks**
		-  Changed `DisableIoMapper` to `false` and `DisableIoMapperMapping` to `true` &rarr; enables `AppleVTD` 
- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **Advancedmap** &rarr; Original source is older
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-01-09: Config Refinements
- Updated OpenCore and Drivers to version 1.0.4 (nightly)
- Tested with macOS Sonoma 14.7.3 and macOS Sequoia 15.3 beta 3
- **CONFIG**
	- **DeviceProperties**:
		- Deleted unused/leftover framebuffer patches
		- Modified BRCM WiFi sppof &rarr; Only `IOName` property is required to trigger "Modern WiFi" root patches in OCLP – and it can stay enabled.

#### How to enable `AirportItlwm` in macOS Sequoia

1. Download [**OpenCore Legacy Patcher**](https://github.com/dortania/OpenCore-Legacy-Patcher/releases) (.pkg).
2. Remove `HeliPort` from Login-Items (if present) – you won't need it any more.
3. Download my EFI folder and extract it.
4. Place it in your EFI partition.
5. Reboot into macOS Sequoia
6. At this stage, WiFi won't work!
7. Install OpenCore legacy patcher and run it
8. Click on "Root Patches"
9. Next, click on "Start Root Patching" to install the required frameworks for "Modern Wi-Fi"
10. Once that's done, reboot
11. Reset NVRAM and start macOS Sequoia.
12. Connect to a WiFi AP via Airport-Utility

More details about this patch can be found [here](https://github.com/5T33Z0/OC-Little-Translated/blob/main/14_OCLP_Wintel/Enable_Features/AirportItllwm_Sequoia.md)

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2025-01-06: `AirportItlwm` in macOS Sequoia! 
- Updated OpenCore and Drivers to version 1.0.4 (nightly)
- Tested with macOS Sonoma 14.7.3 and macOS Sequoia 15.3 beta 3
- I've managed to re-enable AirportItlwm functionaliy in macOS Sequoia by utilizin OCLP's "Modern WiFi" root patching capabilities!
- **CONFIG**:
	- **DeviceProperties**:
		- Added Properties for `#PciRoot(0x0)/Pci(0x14,0x3)` &rarr; spoofs Intel WiFi card as Broadcom BCM4360 in order to trigger "Modern WiFi" root patches in OpenCore Legacy Patcher.
	- **Kernel/Add**:
		- Added `IOSkywalkFamily.kext` 
		- Added `IO80211FamilyLegacy.kext`
		- Disabled `Itlwm` &rarr; no longer needed.
	- **Kernel/Block**:
		- Added Blocking of `com.apple.iokit.IOSkywalkFamily` in macOS Sequoia so it can be downgraded to the older version injected by OpenCore 

#### How to enable `AirportItlwm` in macOS Sequoia

Please read the following instructions carefully.

1. Download OpenCore Legacy Patcher (.pkg).
2. Remove `HeliPort` from Login-Items (if present) – you won't need it any more.
3. Download my EFI folder and extract it.
4. Place it in your EFI partition.
5. Open the `config.plist`.
6. In `DeviceProperties`, remove the leading `#` from this entry: `#PciRoot(0x0)/Pci(0x14,0x3)` to enable the BCM WiFi card spoof.7. Copy over `PlatfornInfo/Generic` data from your existing config (MLB, Serial, ROM, etc.)
8. Save your config and reboot into macOS Sequoia
9. At this stage, WiFi won't work!
10. Install OpenCore legacy patcher and run it
11. Click on "Root Patches"
12. Next, click on "Start Root Patching" to install the required frameworks for "Modern Wi-Fi"
13. Once that's done, OCLP will ask you to reboot – but don't do it yet
14. Mount your EFI, open your `config.plist` and disable the WiFi card spoof again by adding a leading `#` to this device: `PciRoot(0x0)/Pci(0x14,0x3)` to disable it.
15. Save your config and reboot.
16. Reset NVRAM and start macOS Sequoia.
17. Connect to a WiFi AP via Airport-Utility

More details about this patch can be found [here](https://github.com/5T33Z0/OC-Little-Translated/blob/main/14_OCLP_Wintel/Enable_Features/AirportItllwm_Sequoia.md)

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2024-11-20: Sequoia 15.2 Support
- Updated OpenCore and Drivers to version 1.0.3 (nightly)
- Tested with macOS Sonoma 14.7.2 and macOS Sequoia 15.2 beta 2 (24C5079e)
- Updated kexts

Previous version crashed early during 15.2 installation.

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2024-09-18: Added new array `Unload`
- Updated OpenCore and Drivers to version 1.0.3 (nightly)
- Tested with macOS Sonoma 14.7.1 (23H218) and macOS Sequoia 15.1 beta 5

- **CONFIG**:
	- Added Array `UEFI/Unload` &rarr; Refer to OpenCore Documentation for details 
- **KEXTS**:
	- Updated AppleALC to v 1.9.2
	- Updated VoodooPS2, VoodooSMBus and VoodooRMI kexts
 
> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it *after* updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext

### 2024-09-18: Attempting Hibernation Fix
Added kexts and setting to address cmos battery checksum error when using hibernation. See below for details.

- Updated OpenCore to version 1.0.2 (nightly)
- Tested with macOS Sonoma 14.7.1 (23H209) and macOS Sequoia 15.1 beta (24B5046f)
- **KEXTS**:
	- Compiled and added custom build of AdvancedMap (v4.0.1) based on this [PR](https://github.com/notjosh/AdvancedMap/pull/13) which works on both macOS Sonoma and Sequoia
	- Deleted `AdvancedMap_Sonoma` and `AdvancedMap_Sequoia` kexts &rarr; superfluous
	- Added `RTCMemoryFixup` and `HibernationFixup` to address issues with hibernation
- **CONFIG**: 
	- Added boot-arg `rtcfx_exclude=80-AB` to address cmos battery checksum error when using  `hibernatemode 25`
- **RESOURCES**
	- Added `GoldenGate` icons from OCLP &rarr; includes icons for macOS 15
	- Deleted `Chardonnay` and `Syrah` Iconsets since I never use them

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it after updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext
>	- **VooodooSMBus** &rarr; Custom variant
>	- **VoodooRMI** &rarr; Contains `VoodooInput` from a previous nightly build. If you update VoodooRMI, the mouse pointer won't work in macOS Sequoia

### 2024-08-18: OpenCore 1.0.2, housekeeping and USB flash drive

- Updated OpenCore to version 1.0.2 (nightly)
- Tested with macOS Sonoma 14.6.1 and Sequoia beta 6 (24A5320a)
- The issue with mounting FAT32 formatted disks has been resolved in Sequoia beta 6
- **UEFI/Quirks**
	- Re-Enabled `RequestBootVarRooting` &rarr; The quirk causes to show the *internal* EFI disk but not a connected USB flash drive with an EFI folder. If you want to boot from an external USB flash drive, use the BIOS boot menu (press `F12` before OpenCore is loaded).
- Deleted boot-chime (.wav) from Resources/Audio. It contained both the .wav and the .mp3 file.

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - `SecureBootModel` is set to `Disabled`! You can re-enable it after updating to macOS 14.4 or newer. Otherwise the update fails!
> - **Don't update the following Kexts**:
> 	- **AirportItlwm** &rarr; Slimmed kext
> 	- **AppleALC** &rarr; Slimmed kext
> 	- **IntelBluetoothFirmware** &rarr; Slimmed kext
> 	- **Itlwm** &rarr; Slimmed kext
>	- **VooodooSMBus** &rarr; Custom variant
>	- **VoodooRMI** &rarr; Contains VoodooInput from a previous nightly build. If you update the main kext, the mouse pointer won't work in Sequoia

### 2024-08-11: Fixed issue with USB flash drive detection

Tested with macOS 14.6 (23G80) and macOS 15.0 Beta 5 (24A5309e). In Sequoia beta 5, mounting the EFI partition has become difficult. You can use [this script](https://github.com/chris1111/Mount-MS-DOS-Partition) to do it. It requires GateKeeper to be disabled before you can run it. But the method to disable GateKeeper also has changed. You can follow these [Instructions](https://github.com/5T33Z0/OC-Little-Translated/blob/main/14_OCLP_Wintel/Guides/Disable_Gatekeeper.md) to disable it.

- **Kernel Section**
	- Re-arranged kexts by priority (rather cosmetic)
	- Fixed AdvancedMap by using 2 versions of the kext, since the latest version only works with Sequoia:
		- Renamed `AdvancedMap.kext` (v4.0.0) to `AdvancedMap_Sequoia`
		- Added `AdvanceMap_Sonoma.kext` (v3.0.0) for macOS Sonoma
		- Added `MinKernel` and `MaxKernel` settings for both kexts
	- Updated Comments
- **UEFI/Quirks**
	- Disabled `RequestBootVarRooting` &rarr; Fixes USB flash drive not beeing shown in OpenCore's Boot Menu

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - Change `SecureBootModel` to `Disabled` prior to updating to macOS 14.4 or newer! You can enable the setting afterwards.
> - **Don't update the following Kexts**:
> 	- AirportItlwm
> 	- AppleALC
> 	- IntelBluetoothFirmware
> 	- Itlwm
>	- VooodooSMBus
>	- VoodooRMI

### 2024-08-01: YogaSMC update, slimmed kexts

Tested with macOS 14.6 (23G80) and macOS 15.0 Beta 4 (24A5289h)

- Updated YogaSMC &rarr; Make sure to download the YogaSMC App from [here](https://github.com/zhen-zen/YogaSMC/actions/runs/8963283309) and install the updated App and PrefPane!
- Deleted `IntelBTPatcher`&rarr; no longer needed/handled by `BTFirmware.kext` 
- Compiled and ddded slimmed kexts for:
	- **AirportItlwm_Sonoma**: 1,8 instead of 16 MB 	- **AppleALC**: 86 Kb instead of 2,3 MB 
	- **IntelBluetoothFirmware**:	560 KB instead of 11,5 MB
	- **Itlwm**: 1,6 MB instead of 16,1 MB

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - Change `SecureBootModel` to `Disabled` prior to updating to macOS 14.4 or newer! You can enable the setting afterwards.
> - **Don't update the following Kexts**:
> 	- AirportItlwm
>  	- AppleALC
> 	- IntelBluetoothFirmware
>  	- Itlwm
>	- VooodooSMBus
>	- VoodooRMI

### 2024-07-29: Fixed USB Port Mapping

Tested with macOS 14.6 (23G80) and macOS 15.0 Beta 4 (24A5289h)

- Updated OpenCore and Drivers to the latest Nightly build
- Adjusted `USBMap.kext` so the integrated camera works again
- Changed Wifi kext loading: 
	- macOS Sonoma uses `AirportItlwm_Sonoma`
	- macOS Sequoia uses `itlwm.kext`. Connect to Wi-FI via Heloport App!
- Reverted `VoodooRMI.kext` to a previuos build &rarr; Otherwise Trackpad does not work in macOS Sequoia

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - Change `SecureBootModel` to `Disabled` prior to updating to macOS 14.4 or newer! You can enable the setting afterwards.
> - Don't update the following Kexts:
>	- VooodooSMBus
>	- VoodooRMI

### 2024-07-01: Maintanence Update

Tested with macOS 14.6 Beta (23G5052d) and macOS 15.0 Beta 2 (24A5279h)

- Updated `AMFIPass.kext`
- Updated `AppleALC` for Sequoia compatibility
- Removed `-lilubetaall` boot-arg &rarr; No longer required

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - Disable `AirportItlwm`, enable `itlwm` and set `SecureBootModel` to `Disabled` prior to updating to macOS 14.4 or newer! You can restore the settings afterwards.
> - Don't update the following Kexts:
>	- AppleALC	
>	- itlwm
>	- IntelBluetoothFirmware
>	- VooodooSMBus

### 2024-06-25: Fixed Bluetooth in macOS 15.0 Beta 2 (24A5279h)
- **KEXTS**
	- Updated `BlueToolFixup` &rarr; fixed Bluetooth in Sequoia
	- Deleted `IntelBTPatcher`&rarr; no longer needed/handled by `BTFirmware.kext` 

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - Disable `AirportItlwm`, enable `itlwm` and set `SecureBootModel` to `Disabled` prior to updating to macOS 14.4 or newer! You can restore the settings afterwards.
> - Don't update the following Kexts:
>	- AppleALC	
>	- itlwm
>	- IntelBluetoothFirmware
>	- VooodooSMBus

### 2024-06-24: Tweaks for macOS 15.0 Beta 2 (24A5279h)
- **OpenCore**
	- Updated OpenCore to 1.0.1 nightly
	- Updated Drivers
- **CONFIG**
	- **Kernel/Add**	
		- `AirportItlwm_Sonoma`: changed `MaxKernel` setting to `23.9.9` &rarr; loads AirportItlwm_Sonoma in macOS 14.x only
		- `Itlwm`: changed `MinKernel` setting to `24.0.0` &rarr; loads Itlwm in macOS Sequoia
		- Re-enabled `RestrictEVents` so system updates work
	- **Kernel/Quirks** 
		- Enabled `ThirdPartyDrives` Quirk to enable Trim on my SATA M.2 disk. Open System Profiler; look for `Trim` in the `SATA` or `NVMExpress`section to check the `Trim` status. If it's `Yes` already, you don't need this quirk. According to OC documentation, NVME disks don't need it by default.
	- **Misc/Security**
		- Disabled SecureBootModel, so system updates can be installed 

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - Disable AirportItlwm, enable itlwm and set `SecureBootModel` to `Disabled` prior to updating to macOS 14.4 or newer! You can restore the settings afterwards.
> - Don't update the following Kexts:
>	- AppleALC	
>	- itlwm
>	- IntelBluetoothFirmware
>	- VooodooSMBus

### 2024-06-16: macOS 15 Compatibility, new `USBMap.kext`

I've noticed that Bluetooth was no longer working in macOS 14.5 when using the `USBMap.kext`. Since BT works fine when injecting ports via ACPI, I recreated the `USBMap.kext` and also mapped the ports of the Docking Station I have (turns out it has 2 Hubs for USB 2 and 3). 

Bluetooth doesn't work in macOS Sequia 15.0 Beta (24A5264n) yet but this is because the required kexts haven't been updated by the devs yet.

- **OpenCore**
	- Updated OpenCore to 1.0.1 nightly
- **KEXTS**
	- Updated VoodooInput.kext to latest nightly build so the trackpad works
	- Disabled AirportItlmw (incompatible with Sequoia)
	- Enabled Itlwm.kext (use Heliport App to connect to WiFi)
- **NVRAM**
	- Added `-lilubetaall` boot-arg &rarr; required for now to boot macOS 15

> [!IMPORTANT]
> 
> - Reset NVRAM before booting!
> - Disable AirportItlwm, enable itlwm and set `SecureBootModel` to `Disabled` prior to updating to macOS 14.4 or newer! You can restore the settings afterwards.
> - Don't update the following Kexts:
>	- AppleALC	
>	- itlwm
>	- IntelBluetoothFirmware
>	- VooodooSMBus

### 2024-05-25: macOS 14.5 Compatibility
- Updated OpenCore to 1.0.1 nightly
- Updated drivers and kexts
- Tested successfully with macOS Sonoma 14.5 (23F79)
- **ACPI**
	- Added `SSDT-EXT3-LedReset-TP.aml` to stop the Power LED from pulsing after exiting sleep (only required if YogaSMC is not used) 
	- Reverted USB Port Mapping via ACPI due to reported issues
- **KEXTS**
	- Removed `AirportItlwm` variants for Monterey, Ventura and Sononma ≤ 14.3 to declutter the EFI folder and its size
	- Re-Enabled `USBMap.kext` for handling USB ports
- **NVRAM**
	- Added `-rtsfbeta` to load `RealtekCardReaderFriend.kext` on macOS 12+

> [!IMPORTANT]
> 
> - Disable AirportItlwm, enable itlwm and set `SecureBootModel` to `Disabled` prior to updating to macOS 14.4 or newer! You can restore the settings afterwards.
> - Don't update the following Kexts:
>	- AppleALC	
>	- itlwm
>	- IntelBluetoothFirmware
>	- VooodooSMBus (for now)

### 2024-03-08: macOS 14.4 Compatibility
- Updated OpenCore to 0.9.9 nightly
- Updated drivers and kexts
- Tested successfully with macOS Sonoma 14.4 (23E214)
- **KEXTS**
  - Added `AirportItlwm_14.4.kext` &rarr; Required for WiFi to work in macOS 14.4

> [!IMPORTANT]
> 
> - For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
> - macOS 14.4 requires a Clean Install via USB! Updating from 14.3.1 to 14.4 via `System Update` crashes the installer early.
> - Don't update the following Kexts:
>	- AppleALC	
>	- itlwm
>	- IntelBluetoothFirmware
>	- VooodooSMBus (for now)

### 2024-02-23: Maintenance Update & Keyboard tweak
- Updated OpenCore to 0.9.9 nightly
- Updated Drivers and kexts
- Tested successfully with macOS Sonoma 14.3.1 (23D60)
- **KEXTS**
	- **VoodooPS2Keyboard.kex**t
		- Enabled `Use ISO layout keyboard` in `info.plist` &rarr; Fixes function/position of <kbd><</kbd>/ <kbd>></kbd> and `^` key. Otherwise, they are switched around.

> [!IMPORTANT]
> 
> - For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
> - Don't Instal 14.4 beta!
> - Don't update the following Kexts:
>	- AppleALC	
>	- itlwm
>	- IntelBluetoothFirmware
>	- VooodooSMBus (for now)

### 2024-01-09: Fixed ACPI USB Port Map
- Updated OpenCore to 0.9.8 nightly
- Updated Drivers and kexts
- Tested successfully with macOS Monterey to Sonoma 14.3 Beta (23D5043d)
- **ACPI**
	- `SSDT-PORTS`: Corrected port type of `SS01` (Thunderbold) to `0x09` (Type C with Switch) &rarr; Fixes Audio Device (Output) in Windows not being available some odd reason.
- **CONFIG**
	- Kernel/Add: Disabled the following kexts, to let YogaSmC handle it:
		- CPU Friend + DataProvider
		- SMCProcessor + SMCSuperIO
	- Kernel/Patch:
		- Deleted "8 Apples Glitch fix" since it had no effect
		- Added "PCI bus enumeration fix (Sonoma)" &rarr; Fixes internal PCIe devices being displayed as express cards in the menu bar.

> [!IMPORTANT]
> 
> - For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
> - Don't update the following Kexts:
>	- AppleALC	
>	- itlwm
>	- IntelBluetoothFirmware
>	- VooodooSMBus (for now)

### 2023-12-23: ACPI USB port mapping v1.0
- Updated OpenCore to 0.9.8
- Updated Drivers and kexts
- Tested successfully with macOS Monterey to Sonoma 14.3 Beta (23D5033f)
- **ACPI**
	- Added `SSDT-PORTS.aml` &rarr; Contains SMBIOS-indepndant USB Port Mapping.
- **CONFIG**
	- **ACPI/Add**: Added SSDT-PORTS.aml &rarr; Adds acpi-based USB Port Mapping
	- **ACPI/Delete**: Added rule to drop OEM USB port mapping table
	- **Kernel/Add**: Disabled `USBMap_MBP152.kext` &rarr; no longer required

> [!IMPORTANT]
> 
> - For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
> - Don't update the following Kexts:
>	- AppleALC	
>	- itlwm
>	- IntelBluetoothFirmware
>	- VooodooSMBus (for now)

### 2023-12-23: Housekeeping
- Updated OpenCore to 0.9.8
- Updated Drivers and kexts
- Tested successfully with macOS Monterey to Sonoma 14.3 Beta (23D5033f)
- **CONFIG**
	- **DeviceProperties**	
		- Deleted left-over FB patch
		- Switched to Intel HD 630 FB
	- **Misc/Security**
		- Re-enabled `SecureBOotModel` (j132)
	- **PlatformInfo**
		- Disabled `CustomMemory`

> [!IMPORTANT]
> 
> - For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
> - Don't update the following Kexts:
>	- AppleALC	
>	- itlwm
>	- IntelBluetoothFirmware
>	- VooodooSMBus (for now)

### 2023-12-09: Tweaked Hibernation Settings, added `EDID`
- Updated OpenCore to 0.9.7 (commit 6a961f1)
- Updated `Drivers` and `Kexts`
- Tested successfully with macOS Monterey to Sonoma 14.2 (23C64)
- **CONFIG**
	- `DeviceProperties`
		- Added `EDID` (Key `AAPL00,override-no-connect`) &rarr; Allows more resolutions
	- `Kernel/Quirks`
		- Disabled `AppleXcpmCfgLock` &rarr; Not required. The system boots without it.
	- `Misc/Boot`
		- Changed `HibernateMode` from `Auto` to `NVRAM`
		- Enabled `HibernateSkipsPicker` &rarr; Skips Boot Picker when exiting Hibernation for a more seamless wake.

> [!IMPORTANT]
> 
> - For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
> - Don't update the following Kexts:
>	- AppleALC
>	- itlwm
>	- IntelBluetoothFirmware
>	- VooodooSMBus (for now)

### 2023-11-24: Optimized HDMI handshake and other improvements
- Updated OpenCore to 0.9.7 (commit [**c65fb5b**](https://github.com/acidanthera/OpenCorePkg/commit/c65fb5bbfdff42fce67180c4366c9336f01fc744))
- Updated `Drivers` and `Kexts`
- Tested successfully with macOS Monterey to Sonoma 14.2 beta 3
- **ACPI**
    - Deleted `SSDT-EXT3-LedReset-TP`, because a) it does not work and b) **YogaSMC** fixes the pulsing Power Button LED after wake.
- **CONFIG**
    - `ACPI/Patch`
        - Added `_PTS to ZPTS(1,N)` and `_WAK to ZWAK(1,S)` renames because `SSDT-PTSWAK` and `SSDT-EXT` fixes actually require them so that the “Else” portion of the construct is executed if macOS is *not* running! All other T490 EFIs overlooked this!
    - `DeviceProperties`
        - Changed Device-ID of the Intel HD 630 spoof
        - Added Key `disable-agdc` property to Framebuffer-Patch &rarr; Disables Apple Graphics Device Control (AGDC) &rarr; Drastically reduces handshake time when connecting an external display via HDMI. Becasue AGDC causes issues on HDMI when using device-id spoofs.
    - `Kernel/Add`
        - Re-enabled `YogaSMC`. The previous issue (High CPU spikes) were caused by Intel Power Gadget’s `EnergieDriver.kext` which is no longer compatible with macOS 14.2+. So uninstall it, if it’s still present on your system!
- **KEXTS**:
    - Replaced `itlwm.kext`. The previous version didn’t work in 14.2 b3

**NOTES**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
- Don't update the following Kexts:
	- AppleALC
	- itlwm
	- IntelBluetoothFirmware
	- VooodooSMBus (for now)

### 2023-11-19: Fixed Bluetooth
- Updated OpenCore to 0.9.7 Nightly
- Updated `Drivers` and `Kexts`
- Tested successfully with 14.2 Beta (23C5041e)
- **KEXTS**
	- Replaced `BlueToolFixup.kext` by latest one from Acidanthera &rarr; Fixes BT in Sonoma 14.2 beta 3
	- Replaced `CPUFriendDataProvider.kext` by a newer version generated in Sonoma
	- Replaced `AirportItlwm_Monterey` and `AirportItlwm_Ventura` Wi-Fi kexts by slimmed versions  &rarr; Saves 30 MB (Slimmed Sonoma kexts are work in progress)
	- Added `SMCProcessor.kext` and `SMCSuperIO.kext` for CPU and Fan speed Monitoring (you probably don't need these if you're planning to use YogaSMC)
- **CONFIG**
	- Changed `MinKernel` for `AdvancedMap.kext` to `22.0.0` so it loads in Ventura as well.

**NOTES**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
- Don't update the following Kexts:
	- AppleALC
	- itlwm
	- IntelBluetoothFirmware
	- VooodooSMBus (for now)

### 2023-11-15: Disabled YogaSMC
- Updated OpenCore to 0.9.7 Nightly
- Updated `Drivers` and `Kexts`
- Tested successfully with 14.2 Beta (23C5041e)
- **CONFIG**
	- Disabled **YogaSMC** &rarr; I've noticed weird behavior in regards to CPU Power Management and Fan Control. The Performance profiles also don't seem to work correctly. The system just works better with CPUFriend only. Now, only the Volume upd/down and Brightness shortcut keys are working. That's okay for me, since this is all I need, really.

**NOTES**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
- Don't update the following Kexts:
	- AppleALC
	- itlwm
	- IntelBluetoothFirmware
	- VooodooSMBus (for now)

### 2023-10-29: ACPI, Cursor and Framebuffer Tweaks
- Updated OpenCore to 0.9.6 Nightly
- Updated `Drivers` and `Kexts`
- Tested successfully with macOS 14.2 Beta (23C5030f)
- **ACPI**
	- Added modified `DMAR` table without reserved Memory regions (disabled)
	- Added `If (_OSI ("Darwin"))` switch to *SSDT-THINK*, *SSDT-ECRW*, *SSDT-EXT1*, *SSDT-EXT3* and *SSDT-EXT4* &rarr; So changes only apply to macOS
- **CONFIG**
	- **ACPI** 
		- **ACPI/Add**
			- Added modified `DMAR` table without reserved Memory regions (disabled)
		- **ACPI/Delete**
			- Added rule to drop OEM `DMAR` table (disabled)
	- **DeviceProperties**
		- Switched to different Framebuffer Patch (spoofed as UHD 630 instead of Iris 655) due to recent issue reports regarding macOS Ventura
	- **UEFI/AppleInput**
		- Enabled `Custom Delays` &rarr; Increases cursor speed and key repeat rate

**NOTES**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
- Don't update the following Kexts:
	- AppleALC
	- itlwm
	- IntelBluetoothFirmware
	- VooodooSMBus (for now)

### 2023-10-01: Maintenance Update
- Updated OpenCore to 0.9.6 Nightly
- Updated `Drivers` and `Kexts`
- Tested successfully with macOS Monterey to Sonoma 14.1

**NOTES**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
- Don't update the following Kexts:
	- AppleALC
	- itlwm
	- IntelBluetoothFirmware
	- VooodooSMBus (for now)

### 2023-09-05: 3D Globe in Maps, Custom AppleALC Layout
- Updated OpenCore to 0.9.5 Nightly
- Updated `Drivers` and `Kexts`
- Tested successfully with macOS Monterey to Sonoma b7
- **Kexts**
	- Added `AdvancedMap` &rarr; enables 3D Globe in Maps in macOS 12 and newer
	- Added `AirportItlwm_Monterey`, `AirportItlwm_Ventura` and `AirportItlwm_Sonoma` Kexts with corresponding Min/Max Kernel Settings
	- Added Layout 98 to AppleALC &rarr; Enables Line-out of ThinkPad Ultra Docking Station. Work in progress. As of now, only the Line-out works when this layout is enabled
	- Slimmed IntelBluetoothFirmware kext
- **Config**
	- Disabled `VoodooPS2Mouse.kext` &rarr; not required 
	- Disabled `itlw.kext` in favor of AirportItlwm
- **Resources**
	- Updated Aciadanthera Golden Gate iconset  

**NOTES**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
- Don't update the following Kexts:
	- AppleALC
	- itlwm
	- IntelBluetoothFirmware
	- VooodooSMBus (for now)

### 2023-07-08: SSDT and kext refinements
- Updated OpenCore to 0.9.4 Nightly
- Updated `Drivers` and `Kexts`
- Tested successfully with macOS Monterey to Sonoma
- **ACPI**
	- Replaced `SSDT-PLUG` by the one from Acidanthera and removed all unnecessary references. Only required for macOS 11 and older.
	- Removed `SSDT-BAT` &rarr; No longer required. `ECEnabler.kext` takes care of handling the battery status 
	- `SSDT-USBX`: Corrected current values for USBX device according to [specs](https://github.com/5T33Z0/OC-Little-Translated/tree/main/01_Adding_missing_Devices_and_enabling_Features/Embedded_Controller_(SSDT-EC)#adding-the-correct-current-values-to-usbx-device). Disable Always-On USB in BIOS so USB devices don’t draw power in sleep!
- **CONFIG**
	- Kernel/Add:
		- Added `AirportItlwm_Ventura.kext` (loads for Kernel 22.0.0 to 22.9.9)
		- Added `AirportItlwm_Sonoma.kext` (loads for Kernel 23.0.0 to 23.9.9)
		- Changed `Arch` to `x86_64` for all kexts
	- Changed `HibernateMode` from `None` to `Auto`  
	- Changed `ExposeSensitiveData` from `4` to `6` &rarr; Allows OpenCore version to be displayed in Apps like Hackintool, About this Hack, Terminal, etc.
- **KEXTS**
	- Added AirportItlwm_Ventura.kext &rarr; Disable `Itlwm.kext` before using it
	- Added AirportItlwm_Sonoma.kext &rarr; Disable `Itlwm.kext` before using it

**NOTES**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
- Don't update VooodooSMBus for now.

### 2023-06-17: SMBIOS, Hibernation and ext. Display
- Updated OpenCore to 0.9.4 Nightly
- Updated `Drivers` and `Kexts`
- Tested successfully with macOS Monterey to Sonoma
- **CONFIG**
	- **Booter/Quirks**:
		- Enabled `EnableWriteUnprotector`
		- Disabled `RebuildAppleMemoryMap` 
		- &rarr; both changes are necessary so the ReservedMemory fix for Hibernation works. Otherwise the system will shut off when the fix is enabled
	- **DeviceProperties**
		- Modified Framebuffer Patch so handshake between HDMI and DVI monitor takes less time (not an issue when using HDMI to HDMI)
	- **PlatformInfo/Generic**
		- Swithced SMBIOS to MacBookPro15,2 &rarr; works better wich YogaSMC's DYTC funtion
	- **UEFI/ReservedMemory**
		- Enabled Patch for fixing black screen after wake from Hibernation

**NOTES**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
- Don't update VoodooPSController, VooodooSMBus and VoodooRMI for now.
- If you want attach an external display, do it *after* booting. Otherwise handshake takes much longer. still working on that.

### 2023-06-14: Trackpd goodness
- Updated OpenCore to 0.9.4 Nightly
- Updated `Drivers` and `Kexts`
- Tested successfully with macOS Monterey to Sonoma
- **CONFIG**
	- Further refined Framebuffer patch to improve behavior once an external DVI display is connected (still not perfect)
- **KEXT**
	- Got some custom kext to fix Mouse Pointer in BootPicker, Tap to touch and Trackpad scrolling

**NOTES**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
- Don't update VoodooPSController, VooodooSMBus and VoodooRMI for now.

### 2023-06-11: Framebuffer-Fuxxery
- Updated OpenCore to 0.9.3 Nightly
- Updated `Drivers` and `Kexts`
- Tested successfully with macOS Monterey to Sonoma
- **CONFIG**
	- **DeviceProperties**
		- `PciRoot(0x0)/Pci(0x2,0x0)` – Changed Framebuffer Patch for Whiskeylake:
			- Changed `AAPL,ig-platform-id` from `9B3E0000` to `0900A53E` 
			- Changed `device-id` from `9B3E0000` to `A53E0000` as suggeted by [**IntelHD FAQs**](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md#intel-uhd-graphics-610-655-coffee-lake-and-comet-lake-processors) &rarr; Improves handling of external displays.
			- Added backlight register fix to address backlight turning off during boot occasionally
		- `PciRoot(0x0)/Pci(0x1F,0x3)` – Added Audio Device with Layout-ID and Model
	- Updated Comments
	- Updated `MinKernel`/`MaxKernel` settings
- **KEXTS**: Custom compiled kexts:
	- **AppleALC** &rarr; For Realtek ALC257 with Layout `97` only &rarr; 86 kB in size instead of 2,2 MB
	- **itlwm** &rarr; Compatible with macOS Sonoma; only 1,5 MB in sized (instead of 16 MB)

**NOTES**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)

### 2023-06-09: Sonoma here I come!
- Updated OpenCore to 0.9.3 Nightly
- Updated `Drivers` and `Kexts`
- Tested successfully with macOS Monterey to Sonoma
- **CONFIG**
	- Changed `PickerAttributes` from `147` to `131` to fix Mouse Pointer glitches in BootPicker when using the Trackpad
	- Disabled `ProvideCustomSlides` &rarr; Not needed. "All Slides are usable" (according to OC Log)
	- Disabled `SafeModeSlide` &rarr; Not needed according to OC Log.
- **KEXTS**
	- Removed `AirportItlwm` kext &rarr; Currently incompatible with macOS Ventura 13.5b2 and Sonoma. `itlwm` kext works much better overall and doesn't require different versions of the kext based on the used macOS version. Only Downside is that it's not avaiable during instalation. You have to use Ethernet in this case
	- Replaced `BlueToolFixup` with a 0 day version &rarr; Fixes Bluetooth in macOS Sonoma

**NOTES**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)

### 2023-06-09: Let's go!
- Initial Release

**Notes**:

- For deployment, follow the instructions [on my repo](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)

!</details>
