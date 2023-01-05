# Lenovo Yoga-710-14IKB Hackintosh
[![macOS](https://img.shields.io/badge/Ventura-13.1%2022C65-FFFF00.svg)](https://www.apple.com/macos) 
[![macOS](https://img.shields.io/badge/Monterey-12.6%2021G115-blue.svg)](https://www.apple.com/macos) 
[![OpenCore](https://img.shields.io/badge/OpenCore-0.8.8-success.svg)](https://github.com/acidanthera/OpenCorePkg)
[![License](https://img.shields.io/badge/License-MIT-blueviolet.svg)](https://github.com/wintermist/Lenovo-Yoga-710-14IKB-Hackintosh/blob/main/LICENSE)

# Disclaimer
This repository is meant to serve as rough documentation of the steps I followed to turn my Yoga-710-14IKB into a working Hackintosh. The documentation and the files listed here should be used as a reference only. If you choose to use the documentation and/or the files for your personal use, you are allowed to do so on your responsibility. Under no circumstances will I be held responsible for any data loss or bricked device or other disasters that might strike your PC. Also, create your own SMBIOS. I've removed my SMBIOS details from the __config.plist__.

### Update
_Monterey-EFI will no longer be updated._ The Ventura EFI should keep on working on Monterey systems, but there is no way for me to test.

![screenshot.png](https://raw.githubusercontent.com/wintermist/Lenovo-Yoga-710-14IKB-Hackintosh/main/Resources/screenshot.png)

# Configuration
| Specification    | Details                                                                                                           |
|------------------|-------------------------------------------------------------------------------------------------------------------|
| Laptop Model     | Lenovo Yoga 710 (14") - 14IKB  (without dGPU)                                                                     |
| Display          | 14.00 inch 16:9, 1920 x 1080 pixel 157 PPI, Multitouch, AU Optronics B140HAN03.0, a-Si WLED TFT, IPS, glossy: yes |
| Processor        | Intel i5-7200U (4) @ 2.50GHz (IKBL)                                                                               |
| GPU              | Intel HD Graphics 620 (Integrated)                                                                                |
| Motherboard      | Intel Kaby Lake-U Premium PCH                                                                                     |
| Memory           | 1x8GB - G.SKILL Ripjaws DDR4-2400MHz-CL16-16-16 (F4-2400C16S-8GRS)                                                |
| Storage          | Crucial MX500 M.2 SATA SSD (CT500MX500SSD4)                                                                       | 
| Card Reader | Realtek RTS5229 PCI Express Card Reader |
| Sound Codec      | ALC236 (layout-id: 15)                                                                                            |
| WLAN & Bluetooth | BCM94352Z - Lenovo China 4352+20702 NGFF  `[0489:e07a]`                                                           |
| BIOS/UEFI        | 2XCN38WW (V2.12)                                                                                                  |

**NOTE:** My version of Yoga 710 is iGPU only. There is another variant that comes with When I first got this laptop, I upgraded the RAM from 4GB to 8GB. The included 240GB LITE-ON SSD died within two years, so I replaced it with a Crucial MX500 M.2 SSD. When I first installed High Sierra on this laptop, I swapped the Intel 8260NGW card with a Lenovo branded BCM94352Z. Replacement of the WLAN+Bluetooth combo card is probably unnecessary because of successful efforts to support Intel WiFi cards under macOS. All the details of these replaced components are in the above list.

# Important
- I own an iGPU-only variant of Yoga 710. There was another variant that came with discrete Nvidia 940MX GPU which is unsupported by macOS. That GPU must be disabled using one of [_these_](https://dortania.github.io/OpenCore-Install-Guide/extras/spoof.html) ways for proper functioning of hackintosh.
- You have to replace _BlueToolFixup.kext_ with _BrcmBluetoothInjector.kext_ if you want to boot Big Sur.
- XhciPortLimit is broken in Big Sur 11.3 and above, so you can't boot Big Sur (11.3+), Monterey (and Ventura) without properly configured USBMap.kext. You must properly map your USB ports using an older macOS (Catalina, for example) before you attempt booting Big Sur 11.3+.

# Working
- Graphics (with QE/CI)
- Proper CPU Power Management
- Sleep/Wake
- Audio and Brightness **Fn** keys
- Brightness
- Keyboard
- Trackpad (full gestures support.)
- Battery Status
- Audio
- Siri
- WiFi
- Bluetooth
- HDMI output (along with audio)
- Touchscreen (with a caveat)
- Webcam
- iServices (iMessage, FaceTime, AppStore etc.)
- Card Reader

# Not Working
- Physical Trackpad buttons
- DRM (iGPU only systems are [unsupported](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.Chart.md).)

#  Untested
- Anything that requires owning a real Apple device i.e., Handoff, AirDrop etc. 

# BIOS/UEFI Settings
An important element of having power management in Hackintosh is disabling [CFG-Lock](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html#checking-if-your-firmware-supports-cfg-lock-unlocking) in BIOS. The problem is most laptop BIOS keeps this setting hidden from users. Yoga 710 is no different, so you can't find this option in the BIOS. But, fortunately, there is a way to unlock the __Advanced__ setting in our BIOS without invasive procedures. We will use a bunch of key combinations to get access to the Advanced settings.

* Press Power Button → Press Fn+F2 to enter BIOS → Now press the Power Button again to turn off the laptop. → Now press the following keys in the order in which they appear below.
    * `F1` → `1` → `Q` → `A` → `Z`
    * `F2` → `2` → `W` → `S` → `X`
    * `F3` → `3` → `E` → `D` → `C`
    * `F4` → `4` → `R` → `F` → `V`
    * `F5` → `5` → `T` → `G` → `B`
    * `F6` → `6` → `Y` → `H` → `N`
* Now press Power Button → Press Fn+F2 to enter BIOS, __Advanced__ tab should be visible.

Now you can configure your BIOS freely. [Guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/kaby-lake.html#intel-bios-settings)

To disable CFG-Lock:
* `Advanced` → `Power & Performance` → `CPU - Power Management Control` → `CPU Lock Configuration` → `CFG Lock` → `Disabled`

# Required Drivers/Kexts

## [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg/releases/)
- [Adding the Base OpenCore Files](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/opencore-efi.html)

## [VirtualSMC](https://github.com/acidanthera/virtualsmc/releases)
- VirtualSMC.kext
- SMCBatteryManager.kext (Required for working battery status.)
- SMCProcessor.kext
- SMCSuperIO.kext

## [Lilu](https://github.com/acidanthera/Lilu/releases/)
- Lilu.kext

## [WhateverGreen](https://github.com/acidanthera/WhateverGreen/releases/)
- WhateverGreen.kext

## [VoodooPS2](https://github.com/acidanthera/VoodooPS2)
- VoodooPS2Controller.kext

## [AirportBrcmFixup](https://github.com/acidanthera/AirportBrcmFixup/releases/)
- AirportBrcmFixup.kext (Delete AirPortBrcm4360_Injector.kext or disable using config.plist)

## [BrcmPatchRam](https://github.com/acidanthera/BrcmPatchRAM/releases/)
- BlueToolFixup.kext
- BrcmFirmwareData.kext
- BrcmPatchRAM3.kext

## [AppleALC](https://github.com/acidanthera/AppleALC/releases/)
- AppleALC.kext

## [BrightnessKeys](https://github.com/acidanthera/BrightnessKeys/releases/)
- BrightnessKeys.kext

## [VoodooInput](https://github.com/acidanthera/VoodooInput/releases)
- VoodooInput.kext

## [VoodooRMI](https://github.com/VoodooSMBus/VoodooRMI/releases)
- VoodooRMI.kext
- VoodooSMBus.kext

## [VoodooI2C](https://github.com/VoodooI2C/VoodooI2C/releases)
- VoodooI2C.kext
- VoodooI2CHID.kext

## [USBMap](https://github.com/corpnewt/USBMap)
- USBMap.kext (Needs to be generated manually.)

## [RealtekCardReader](https://github.com/0xFireWolf/RealtekCardReader/releases)
- RealtekCardReader.kext

## [RealtekCardReaderFriend](https://github.com/0xFireWolf/RealtekCardReaderFriend/releases)
- RealtekCardReaderFriend.kext

## [ECEnabler](https://github.com/1Revenger1/ECEnabler/releases)
- ECEnabler.kext

## [CPUFriend](https://github.com/acidanthera/CPUFriend/releases) (Optional)
- CPUFriend.kext (must be paired with CPUFriendDataProvider.kext)

__Note:__ Don't use CPUFriend.kext during installation.


# Preparation
I can't stress enough how important it is to take note of the hardware and device/vendor ids before you even attempt to proceed further. [Guide](https://dortania.github.io/OpenCore-Install-Guide/find-hardware.html#finding-hardware-using-windows) 

Use [SSDTTime](https://github.com/corpnewt/SSDTTime) to dump your DSDT and SSDTs in Linux (or Windows). Make sure to take a copy of those in an external drive. While you will not use the whole DSDT with OpenCore, you may have to refer to it for manually patching other SSDTs. You will also need a 16GB+ USB flash drive to make the bootable USB installer. I will assume that you already have access to a mac or a Hackintosh machine.

If you plan on installing Ventura or Monterey or Big Sur 11.3+, make sure that you install a prior version of macOS and create your [USBMap.kext](https://github.com/corpnewt/USBMap). You may try the USBMap.kext available in this repository and place it inside __EFI__ > __OC__ > __Kexts__ folder. Alternatively, if you are still on windows, you may give _[this](https://github.com/USBToolBox/tool)_ a go.

# Installation
For installation, refer to _[Dortania's OpenCore install guide](https://dortania.github.io/OpenCore-Install-Guide/)_. Ideally, you should read the whole guide thoroughly. Once you are done, lots of things make sense.

In OpenCore, some kexts are required to be loaded before some other kext(s) to function properly. So make sure that you understand the kext loading order. You can also refer to the __config.plist__ available in this repository to see the order in which they must be loaded.

# Useful Tips
1. Pay attention to VoodooRMI kexts loading order. There are redundant kexts that must be disabled. [ProperTree](https://github.com/corpnewt/ProperTree) does this automatically when you take OC Snapshot, but you should verify it manually.
2. To increase VRAM and get HDMI to work properly, you must patch the framebuffer using [Hackintool](https://github.com/headkaze/Hackintool). [Guide](https://www.tonymacx86.com/threads/guide-general-framebuffer-patching-guide-hdmi-black-screen-problem.269149/#Preparation)
3. I2C devices must be GPIO pinned for VoodooI2C to work properly. [Guide](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad-methods/manual.html#checking-gpi0)
4. The touchscreen works only after waking up from sleep (it has been this way since High Sierra). To get it working, you need Windows patches (which is often discouraged) using __SSDT-XOSI.aml__, and you also need to add XOSI renames in your config.plist. Since I do not use the touchscreen, I have not included the files in this repository. [Guide](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad-methods/manual.html#osi-to-xosi)
5. Bluetooth situation does not look too good in macOS Monterey. You have to use BlueToolFixup.kext. Currently, it shows up as _THIRD_PARTY_DONGLE_. You can read up about current limitations [here](https://github.com/acidanthera/bugtracker/issues/1821).
6. Using prebuilt SSDTs is fine, but try to create SSDTs manually. It teaches you a lot in the process. SSDTTime generated SSDTs that I ended up using are–
    - SSDT-EC.aml
    - SSDT-HPET.aml
    - SSDT-PLUG.aml
7. __Using CPUFriend.kext (OPTIONAL)__ This should be the last thing in your Hackintosh building process, and it is optional. [Guide](https://www.olarila.com/topic/5693-guide-ssdt-with-pikes-pm-script-and-use-with-cpufriend/) (No need to download the Data.zip at Olarila. That zip contains required configurations for Coffee Lake users. And you should opt for the kext version i.e., CPUFriendDataProvider.kext)

# Credits
- [Apple](https://www.apple.com/) for macOS.
- [Acidanthera team](https://github.com/acidanthera) for OpenCorePkg, VirtualSMC, Lilu, AppleALC, BrcmPatchRAM, VoodooPS2, WhateverGreen, AirportBrcmFixup, VoodooInput, CPUFriend, BrightnessKeys.
- [VoodooSMBus](https://github.com/VoodooSMBus) for VoodooRMI.
- [0xFireWolf](https://github.com/0xFireWolf) for RealtekCardReader and RealtekCardReaderFriend.
- [dortania](https://github.com/dortania) for awesome OpenCore Install Guide.
- [CorpNewt](https://github.com/corpnewt) for USBMap.kext, GenSMBIOS, SSDTTime, MountEFI and ProperTree.
- [1Revenger1](https://github.com/1Revenger1) for ECEnabler.kext and other contributions.
- [alexandred](https://github.com/alexandred) and other VoodooI2C team for VoodooI2C.
- [daliansky](https://github.com/daliansky) for the original guide on unlocking the Advanced menu in the BIOS.
- [Headkaze](https://github.com/headkaze) for Hackintool.
- [Piker-Alpha](https://github.com/Piker-Alpha) for ssdtPRGen.sh.
- [RehabMan](https://bitbucket.org/RehabMan/) for awesome guides and tools.

