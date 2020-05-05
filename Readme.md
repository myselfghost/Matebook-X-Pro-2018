# Huawei Matebook X Pro (2018 Edition)
[![release](https://img.shields.io/badge/download-release-blue.svg)](https://github.com/profzei/Matebook-X-Pro-2018/releases) [![wiki](https://img.shields.io/badge/support-wiki-green.svg)](https://github.com/profzei/Matebook-X-Pro-2018/wiki)

#### macOS on Huawei Matebook X Pro 2018
![Alt text](https://ivanov-audio.com/wp-content/uploads/2014/01/Hackintosh-Featured-Image.png)

#### This repo is currently compatible with macOS Mojave 10.14.6 (18G87)
This is intended to create a "fully" functional (as far as possible) hackintosh for the Huawei Matebook X Pro. There are some well-written guides ([here](https://dortania.github.io/getting-started/)), so it's not a particular issue getting macOS installed onto my MBXP; but the reason why I didn't stop my efforts into resolving each bug, there was because of "minor issues" I ran into. The installation is not perfect yet since it's a continuos work-in-progress, but I'm glad to say that I learned a lot in the meantime.


## Configuration

| Specifications      | Details                                          |
| :--- | :---|
| Computer model      | Huawei Matebook X Pro 2018 Space Gray            |
| Processor           | Intel Core i7-8550U Processor @ 1.8 GHz          |
| Memory              | 8 GB LPDDR3 2133 MHz                             |
| Hard Disk           | LiteON SSD PCIe NVMe 512 GB [CA3-8D512]          |
| Integrated Graphics | NVIDIA GeForce MX150 / Intel(R) UHD Graphics 620 |
| Screen              | JDI 3k Display @ 3000 x 2000 (13.9 inch)         |
| Sound Card          | Realtek ALC255 ???                               |
| Wireless Card       | Intel Dual Band Wireless-AC 8265/8275            |
| Bluetooth Card      | Intel Bluetooth 8265/8275                        |

This repository is for personal purposes: it is heavily based on the hard work done by [gnodipac886](https://github.com/gnodipac886/MatebookXPro-hackintosh), but with some significant personal improvements in `CLOVER/ACPI/patched` and `CLOVER/kexts/Other` sections.


Changelog: see [here](Changelog.md)

## Current Status
- Discrete graphics card (NVIDIA GeForce MX150) is not working, since macOS doesn't support Optimus technology
	- Have used `SSDT-DDGPU.aml` to disable it in order to save power.
- Fingerprint sensor is not working
	- Have used `SSDT-UIAC.aml` to disable it in order to save power.
- Intel Wi-Fi (Intel Wireless 8265) is not (yet) working: there is hard work and, therefore, some progress in Intel Wi-Fi support. Hope to see a fully functional `kext` asap.
	- I bought a USB Wi-Fi dongle: [tp-link](https://www.tp-link.com/it/support/download/tl-wn823n/?utm_medium=select-local#Driver) TL-WN823Nv3 with macOS updated driver support [Wireless-USB-Adapter Driver](https://github.com/chris1111/Wireless-USB-Adapter-Clover/releases) 

## Device Firmware
- Bios version: `1.28`

## Bootloader Firmware
- Default bootloader: Clover `r5103` [Dids release](https://github.com/Dids/clover-builder/releases)

## SMBIOS
- Default SMBIOS settings of this repo is `MacBookPro14,1`

## Power management: CPUFriend
CPU power management can be achieved by using `CPUFriend.kext` while `CPUFriendDataProvider.kext` defines how it should be done. `CPUFriendDataProvider.kext` is generated for a specific CPU and power setting. The one supplied in this repository was made for `i7-8550U` and is optimized for balanced performance.
- The kexts and SSDT for `i7-8550U` are [here](/CPUFriend/1.2.0).
- `CPUFriendDataProvider.kext` is generated for SMBIOS `MacBookPro15,2` because of Kaby Lake R architecture.
- `CPUFriend.kext` and `CPUFriendDataProvider.kext` need to be put in `CLOVER/kexts/Other`
- Furthermore, you also need to put `SSDT-XCPM.aml` in `CLOVER/ACPI/patched` for working as normal after awake.

## Power management: Hibernation
Hibernation (suspend to disk or S4 sleep) is not supported on a Hackintosh: so it's recommended to disable it.
```
sudo pmset -a hibernatemode 0
sudo rm -rf /private/var/vm/sleepimage
sudo touch /private/var/vm/sleepimage
sudo chflags uchg /private/var/vm/sleepimage
```
Also, it's important to disable the other hibernation related functions.
```
sudo pmset -a standby 0
sudo pmset -a autopoweroff 0
```
Disabling additional features prevents random wakeups while the lid is closed.
```
sudo pmset -a powernap 0
sudo pmset -a proximitywake 0   [optional]
sudo pmset -b tcpkeepalive 0    [optional]
```
After every update, ALL these settings should be reapplied manually.

You can verify yuor power settings by typing in terminal `sudo pmset -g live` . If you ever want to reset these settings: `sudo pmset -a restoredefaults`


## USB port mapping

Proper `SSDT-UIAC.aml` and `SSDT-USBX.aml` are used for USB Host Controller (XHCI-Device-ID: `<2f 9d 00 00>`): these files are configured to map only the necessary ports (tested with IOReg) with the correct connector type and prevent it from shutdown issues.

| Port      | Address               | Physical Location                                         | Internal/External |
| :--- | :--- | :--- | :--- |
| HS01/SS01 | `00000001`/`0000000D` | Left Port type-C (Power Source) - next to 3.5mm jack port | E                 |
| HS02/SS02 | `00000002`/`0000000E` | Right Port type-A                                         | E                 |
| HS03      | `00000003`            | Left Port type-C Thunderbolt                              | E                 |
| HS05      | `00000005`            | Bluetooth USB Port                                        | I                 |
| HS07      | `00000007`            | Integrated HD Camera module                               | I                 |

## Optional

#### Monitor temperatures and power consumption with [HWMonitor](https://github.com/kzlekk/HWSensors/releases) 

This app is relatively old and no longer supported, but it gets the job done.

#### Make dock animation faster and without delay
Run these lines in terminal:
```
defaults write com.apple.dock autohide-delay -float 0
defaults write com.apple.dock autohide-time-modifier -float 0.5
killall Dock
```

#### Graphics section
Refer to: [here](/Graphics/)

## Update tracker

| Item | Version |
| :--- | :--- |
| MacOS | 10.14.6 |
| [Clover](https://github.com/CloverHackyColor/CloverBootloader/releases) | 5115 |
| [Lilu](https://github.com/acidanthera/Lilu/releases) | 1.4.3 |
| [WhateverGreen](https://github.com/acidanthera/whatevergreen/releases) | 1.3.8 |
| [AppleALC](https://github.com/acidanthera/AppleALC/releases) | 1.4.8 |
| [CodecCommander](https://github.com/Sniki/EAPD-Codec-Commander/releases) | ~~2.7.2~~ 2.7.1 |
| [CPUFriend](https://github.com/acidanthera/CPUFriend/releases) | 1.2.0 |
| [USBInjectAll](https://github.com/Sniki/OS-X-USB-Inject-All/releases) | 0.7.3 |
| [VoodooI2C](https://github.com/alexandred/VoodooI2C/releases) | 2.4 |
| [VoodooI2CHID](https://github.com/alexandred/VoodooI2C/releases) | 2.4 |
| [VoodooPS2Controller](https://github.com/acidanthera/VoodooPS2/releases) | 2.1.3 |
| [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases) | 1.1.2 |
| NullEthernet | 1.0.6 |
| [NoTouchID](https://github.com/al3xtjames/NoTouchID/releases) | 1.0.3 |
| [HibernationFixup](https://github.com/acidanthera/HibernationFixup/releases) | 1.3.2 |
| [NVMeFix](https://github.com/acidanthera/NVMeFix/releases) | 1.0.2 |
| [IntelBluetoothFirmware](https://github.com/zxystd/IntelBluetoothFirmware/releases) | 1.0.3 |

## Development: Catalina 10.15.4 support

- Update `Clover` to r5107+ to support macOS 10.15.4
- Remove [config] `MSR 0xE2` kernel patch because `Clover` can automatically patch
```
	<dict>
		<key>Comment</key>
		<string>MSR 0xE2 _xcpm_idle instant reboot(c) Pike R. Alpha</string>
		<key>Disabled</key>
		<false/>
		<key>Find</key>
		<data>ILniAAAADzA=</data>
		<key>Replace</key>
		<data>ILniAAAAkJA=</data>
	</dict>
```
- Add [config] `setpowerstate_panic=0` kernel patch for macOS 10.15 according to [Acindathera/AppleALC#513](https://github.com/acidanthera/bugtracker/issues/513#issuecomment-542838126)
```
	<dict>
		<key>Comment</key>
		<string>Disable setPowerState panic with setpowerstate_panic=0 boot-args on 10.15 (credit vit9696)</string>
		<key>Disabled</key>
		<false/>
		<key>MatchOS</key>
		<string>10.15</string>
		<key>Find</key>
		<data>Y29tLmFwcGxlAA==</data>
		<key>Replace</key>
		<data>bm90LmFwcGxlAA==</data>
	</dict>
```
- Add [config] `complete-modeset-framebuffers` property to Intel UHD Graphics 620 device section to improve HDMI
```
	<key>PciRoot(0x0)/Pci(0x2,0x0)</key>
	<dict>
		...
		<key>complete-modeset-framebuffers</key>
		<data>AAAAAAAAAAE=</data>
		...
	</dict>
```
- Add [config] `force-online` and `force-online-framebuffers` properties to Intel UHD Graphics 620 device section to fix HDMI on macOS 10.15.4
```
	<key>PciRoot(0x0)/Pci(0x2,0x0)</key>
	<dict>
		...
		<key>force-online</key>
		<data>AQAAAA==</data>
		<key>force-online-framebuffers</key>
		<data>AAAAAAAAAAE=</data>
		...
	</dict>
```

### Fix AppleID issue on macOS Catalina

- If you encounter the problem with AppleID which cannot login and logout, this problem happened on both Hackintosh and Macintosh.
- Fix this issue with the following commands:
```
sudo -v
killall -9 accountsd com.apple.iCloudHelper
defaults delete MobileMeAccounts
rm -rf ~/Library/Accounts
killall -9 accountsd com.apple.iCloudHelper
sudo reboot
```














































