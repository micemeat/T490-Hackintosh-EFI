# ThinkPad T490 Hackintosh EFI

OpenCore 1.0.8 EFI for running macOS on the Lenovo ThinkPad T490 (20Q8).

## System Specs

| Component | Details |
|-----------|---------|
| Model | Lenovo ThinkPad T490 |
| Type | 20Q8-S6KR00 |
| CPU | Intel Core (Whiskey Lake) |
| iGPU | Intel UHD Graphics 620 |
| SMBIOS | MacBookPro15,2 |

## SMBIOS (Already Configured)

⚠️ **These values are pre-configured — verify before using!**

| Field | Value |
|-------|-------|
| SystemSerialNumber | `C02WLTZBJHCC` |
| MLB | `C02816902CDJH4RCB` |
| SystemUUID | `A5E3599A-89E9-4188-9D4A-C08B73C8A3AF` |
| ROM | `002B6780AEA2` |

### ⚠️ Validate Your Serial

Before using, check: https://checkcoverage.apple.com

Enter serial: `C02WLTZBJHCC`

Expected result: **"Purchase Date not Validated"**

If it shows a real device → **generate a new serial** using [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)

## BIOS Settings

- **Security → Secure Boot**: OFF
- **Security → Intel SGX**: Disabled
- **Startup → UEFI/Legacy Boot**: UEFI Only
- **Config → Display → Shared Display Priority**: HDMI
- **Config → CPU → Hyperthreading**: ON

## Installation Notes

1. Copy EFI folder to USB installer or NVMe ESP partition
2. Reset NVRAM on first boot (OpenCore picker → Reset NVRAM)
3. Install macOS
4. For macOS Tahoe (26.x): Apply root patches with [OCLP-Mod](https://github.com/laobamac/OCLP-Mod/releases)

## Credits

- **Source EFI**: [5T33Z0/Thinkpad-T490-Hackintosh-OpenCore](https://github.com/5T33Z0/Thinkpad-T490-Hackintosh-OpenCore)
- **OpenCore**: [Acidanthera](https://github.com/acidanthera/OpenCorePkg)

## ⚠️ Disclaimer

This is for educational purposes only. Hackintosh may violate Apple's Terms of Service. Use at your own risk.
