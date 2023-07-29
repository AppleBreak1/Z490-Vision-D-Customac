# Z490-Vision-D-Customac

![713M1ibBFHL _AC_SL1500_](https://user-images.githubusercontent.com/97265013/211924502-7de45a78-4462-4c30-b392-da86ce8112aa.png)

# Hardware Specification

CPU : Intel i9-10900K

GPU: Gigabyte RX 580 Gaming 8GB

Motherboard: Z490 Vision D

BIOS revision: F5

Wifi/BT Card: Fenvi FV-T919


# Working

- Restart/Sleep/Shutdown

- Wake on Lan / Wake for network access

- iMessage / Facetime (WebCam Required)

- AirDrop/Handoff/Continuity (Requires natively supported WiFi/BT card such as FV-T919)

- AirplaytoMac/Sidecar/Universal Control (Requires natively supported WiFi/BT card such as FV-T919)

- UHD-630(IQSV Encoding/Decoding)

- Thunderbolt 3 / USB-C to HDMI / Ethernet (I225/I219)
  
     <details> 
     <summary>Screenshot</summary><img width="895" alt="Screenshot 2023-01-14 at 7 54 56 AM" src="https://user-images.githubusercontent.com/97265013/212481325-cad7d83e-f49f-4c22-a7f2-d36a79311734.png">



     </details>  

     
- Working Mac OS: Sierra ~ macOS Sonoma

Note 1: Config.plist changes for Sierra(10.12.6) ~ Catalina

    - SecureBootModel -> Disabled
    - UEFI -> APFS -> MinDate and MinVersion -> -1

Note 2: For Sierra(10.12.6) and High Sierra(10.13.6)

    - SMBIOS iMac18,3 
    
    - Spoof to i9-9900K CPUID (0x0906ED) for High Sierra 10.13.6
    
    - Spoof to i7-7700K CPUID (0x0906E9) for Sierra 10.12.6 
    
    - iGPU DeviceProperty -> AAPL,ig-platform-id -> <03001259> (For headless mode)  
                                                 -> <00001259> (To drive a display)     
                                            
                          -> rps-control instead of igfxfw       
    
    - Boot-args -> -disablegfxfirmware (For Sierra only. This prevents firmware load attempt)
    
 Note 3: For Mojave
 
    - SMBIOS iMac19,1
    
    - iGPU ig-Platform-ID -> <0300983E> (Headless mode)
                          -> <07009B3E> (To drive a display)
    
    - rps-control instead of igfxfw property
 
# Not Working

- DRM in Safari and TV+ (For workaround, force [AMD DRM](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.Chart.md))
- I225 Ethernet in macOS Mojave and below (Support for I225 Ethernet was first introduced in macOS Catalina)


# BIOS Settings

**Run Load Optimized Defaults**

- VT-d -> Enabled (Requirement for loading AppleVTD)

- CSM Support -> Disabled

- X.M.P -> Profile 1

- Internal Graphics -> Enabled

- Above 4G Decoding -> Enabled

- Thunderbolt

- Security Level -> No security

- Thunderbolt USB support -> Enabled

- GPIO3 Force Pwr -> Enabled

- XHCI Hand-Off -> Enabled

- CFG Lock -> Disabled

- Windows 8/10 WHQL


# OpenCore Config.plist

- ACPI -> Delete -> Drop OEM DMAR Table (Requirement for resolving issues with WiFi/Ethernet when enabling AppleVTD for motherboards that have Reserved Memory Regions in their DMAR table)

- Device Properties

    - Intel I225 model name injection 
    - ALC 1220-VB Audio layout-id injection
    - UHD-630 Property Injection 
      
      - ig-platform-id (For iGPU acceleration)
      - device-id (Fixes KBL Unknown to UHD-630)
      - igfxfw (Load Apple's GuC Firmware)


- Kernel -> Quirks -> CustomSMBIOSGuid -> Yes
- Kernel -> Quirks -> DisableIOMapper -> No (Requirement for loading AppleVTD)
- Kernel -> Quirks -> [DisableIOMapperMapping](https://github.com/acidanthera/OpenCorePkg/pull/440) -> Yes, if having issues with WiFi/Ethernet with AppleVTD enabled in Ventura 13.3+)


- Misc -> Security -> ScanPolicy -> 0 (Set it to 2,687,747 if you wish to hide unnecessary entries)


- NVRAM -> Boot-args -> Nothing. (For AMD Rx 5xxx/6xxx GPU, agdpmod=pikera is required)


- PlatformInfo -> Generic -> SystemProductName -> iMac20,2
- PlatformInfo -> Generic -> UpdateSMBIOSMode -> Custom


# SSDT

- SSDT-PLUG.aml (Injects plugin type 1; No longer required beginning with Monterey 12.3) 

- SSDT-EC-USBX.aml (Injects fake Embedded Controller device - High Power Charging for iPhone, iPad, etc)

- SSDT-DMAR.aml (Modifed to remove Reserved Memory Regions from OEM DMAR Table. This is required to fix issues with WiFi/ethernet when enabling AppleVTD. Don't forget to drop OEM DMAR table in ACPI section of the OpenCore config.plist first)

- SSDT-MCHCSBUS-DTGP.aml (Injects MCHC, SBUS, and DTGP method)

- SSDT-TB3HP.aml (SSDT for Thunderbolt hotplug)

- SSDT-AWAC.aml (Disables AWAC clock and enables RTC to fix system clock)

- SSDT-COSMETIC.aml (Optional; injects fake or missing devices and rename devices)

    - Injected Devices: PNLF, THRM, SRAM, XSPI 
    - RP02.PXSX to GIGE  -> Intel I225 Ethernet
    - RP03.PXSX to ARPT  -> FV-T919 in PCIEx1 slot
    - RP09.PXSX to SSD1  -> 2nd M.2 slot (M2A_CPU) * Disables SATA 1 port when occupied by  M.2 PCIe SSD
    - RP17.PXSX to SSD0  -> 1st M.2 slot (M2M_SB) * Disables SATA 4 and 5 ports when occupied by M.2 PCIe SSD
    - RP21.PXSX to SSD2  -> 3rd M.2 Slot (M2P_SB) * Shares bandwidth with PCIEX4 


- SSDT-LPCB.aml (injects DMAC, FWHD, and ARTC devices under LPCB) Note that these are mostly for cosmetic thus optional.

- SSDT-USBW.aml (Single-key wake from sleep; associated with USBWakeFixup.kext)

# Kexts

- VirtualSMC and plugins

- Lilu.kext

- AppleALC.kext (Compiled to work with "layout-id 11" only)

- WhateverGreen.Kext

- CPUFriend.kext

- CPUFriendDataProvider.kext (For i9-10900K)

- NVMeFix.kext

- USBPorts.kext (Ports: HS03, HS04, HS05, HS06, HS07, HS08, HS09, HS10, HS11, HS12, SS03, SS04, SS05, SS06, SS07)
   
   - Works on iMac20,2, iMac18,3, iMac19,1, iMacPro1,1 and MacPro7,1

- IntelMausi.kext (I219 Ethernet)

- FakePCIID.kext (Required for FakePCIID_Intel_HDMI_Audio.kext and FakePCIID_Intel_I225-V.kext) 

- FakePCIID_Intel_HDMI_Audio.kext (Required for onboard Audio to work in Sierra ~ Mojave)

- FakePCIID_Intel_I225-V.kext (Required for I225 Ethernet in macOS Catalina along with fake device-id injection) 

   Note: In macOS Big Sur or higher, AppleEthernetE1000 driver kit natively attaches to i225 ethernet. Thus, FakePCIID_Intel_I225-V.kext is no longer needed. However, if the ethernet port is occupied without having AppleVTD enabled with modifed DMAR table, the system will experience freeze, crash, and etc. To avoid having these issues, we need to enable AppleVTD with the following.
    
    Enabling AppleVTD
    
        - Enable VT-d in BIOS, 
        - Kernel -> Quirks -> DisableIoMapper -> False
        - Kernel -> Quirks -> DisableIoMapperMapping -> True (Required for Ventura 13.3+; see the macOS Ventura note section)
        - Drop OEM DMAR Table in config.plist
        - Inject modified DMAR Table(Reserved Memory Regions removed) in Config.plist

- USBWakeFixup.kext (Works with SSDT-USBW. Causes Bluetooth issue in Monterey+; thus set minkernel to 19.0 and max kernel to 20.9.9) 
        
   Note: One may wish to continue using USBWakeFixup.kext in macOS Monterey or newer. If so, you may set memory clock speed below 2933 MHz or use [Bluesnooze](https://github.com/odlp/bluesnooze) without configuring memory clock speed below 2933 MHz. 
   
# Drivers

- HfsPlus.efi

- OpenCanopy.efi

- OpenRuntime.efi

- CrScreenshotDxe.efi

- AudioDxe.efi (Causes a good 4~5 second delay at splash screen thus disabled)
  
  To enable boot chime in Opencore Boot Picker, make the following changes.
  
        - UEFI -> Audio -> AudioSupport -> Yes
        - UEFI -> Drivers -> Enable AudioDxe.efi

# ACPI USB Port Mapping
[Instruction](https://github.com/AppleBreak1/Z490-Vision-D-Customac/tree/main/USB%20Ports)


# macOS Update Notes

Common Requirements for Sierra ~ Catalina

- SecureBootModel -> Disabled
- UEFI -> APFS -> MinDate and MinVersion -> -1

macOS Sierra 10.12.6

- Change SMBIOS to iMac18,3 (Required)
- Spoof to Kaby Lake CPUIDs (Required)
- Inject ig-platform-id: <03001259> device-id: <12590000> (Necessary for iGPU acceleration) 
- Inject rps-control property instead of igfxfw (Necessary for iGPU acceleration and improvement) 
- Add boot-arg -disablegfxfirmware (To prevent firmware load attempt. Necessary for iGPU acceleration) 
- FakePCIID_Intel_HDMI_Audio.kext for onboard audio

macOS High Sierra 10.13.6

- Change SMBIOS to iMac18,3 (Required)
- Spoof to Kaby Lake or Coffee Lake CPUIDs (Required)
- Inject AAPL,ig-platform-id: <03001259> device-id: <12590000> (Necessary for iGPU acceleration) 
- Inject rps-control property instead of igfxfw (Necessary for iGPU acceleration and improvement) 
- FakePCIID_Intel_HDMI_Audio.kext for onboard audio

macOS Mojave 

- SMBIOS iMac19,1
- Spoof to Coffee Lake CPUIDs 
- Inject AAPL,ig-platform-id: <0300983E> device-id: <9B3E0000> (Necessary for iGPU acceleration) 
- Inject rps-control property instead of igfxfw (Necessary for iGPU acceleration and improvement) 
- FakePCIID_Intel_HDMI_Audio.kext for onboard audio 
- Added NVMeFix.kext


macOS Catalina

- SMBIOS iMac19,1 or may use iMac20,2 for Catalina 10.15.6+
- May set SecureBootModel to "[j185f](https://dortania.github.io/OpenCore-Post-Install/universal/security/applesecureboot.html#what-is-apple-secure-boot)" if on SMBIOS iMac20,2 (Requires Catalina 10.15.6+) 
- Inject AAPL,ig-platform-id: <0300C59B> device-id: <9B3E0000> (May not be necessary if WhatEverGreen.Kext is used)
- Inject igfxfw property (Necessary for iGPU performance improvement)
- Added USBWakeFixup.kext, SSDT-USBW.aml 
- I225 Ethernet requires FakePCIID_Intel_I225-V.kext + Fake device-id injection 

macOS Big Sur

- SecureBootModel -> [j185f](https://dortania.github.io/OpenCore-Post-Install/universal/security/applesecureboot.html#what-is-apple-secure-boot)
- UEFI -> APFS -> MinDate and MinVersion -> 0
- Injecting AAPL,slot-name for iGPU or dGPU breaks certain encoding functions(H264) in FCPX
- Intel I225 Ethernet is natively supported in Big Sur 11.3+ (AppleVTD is required for this to work and flashing EEPROM with [custom firmware](https://www.insanelymac.com/forum/topic/348493-discussion-intel-i225-v-on-macos-monterey/?do=findComment&comment=2779420) may be necessary for some folks) 

macOS Monterey 

- USBWakeFixup.kext causes bluetooth issue after sleep/wake cycle 

    - Two possible workarounds
    
         - Set memory clock speed below 2933 MHz (This may also resolve "Disk not ejected properly" pop-up)
         - [Bluesnooze](https://github.com/odlp/bluesnooze)
         
- Beginning with Monterey 12.3, SSDT-PLUG.aml is no longer required

macOS Ventura

- As of Ventura 13.1, 9 out of 10 times my bluetooth Apple Magic Keyboard and Trackpad do not respond on startup unless bluetooth or the devices itself has been toggled off and on again with USB mouse. This also seems to be a problem with real Macs as well, as decribed [here](https://discussions.apple.com/thread/254330209?answerId=258137456022#258137456022)  

   [Workaround](https://github.com/AppleBreak1/Z490-Vision-D-Customac/commit/d33acbdda25619aba76856b41a931fc0349ede8e)
   
   Update: This issue has been resolved in Ventura 13.3

- As of Ventura 13.3, a WiFi and the Intel I219 Ethernet no longer work if AppleVTD is enabled. One must apply the [patch](https://github.com/CaseySJ/Ventura-AppleVTD-Patch) discovered by [CaseySJ](https://github.com/CaseySJ) to resolve such behavior.

   Update: This [patch](https://github.com/CaseySJ/Ventura-AppleVTD-Patch) is incorporated into OpenCore 0.9.2 as a [DisableIoMapperMapping](https://github.com/acidanthera/OpenCorePkg/pull/440) kernel quirk.
    
# Geekbench 5 & Cinebench R23

<img width="1656" alt="Screenshot 2023-01-11 at 10 35 15 AM" src="https://user-images.githubusercontent.com/97265013/211889865-666ff78a-5bee-433c-9591-4532980a74a3.png">






# Resources

- [Apple Inc](https://www.apple.com/) for MacOS.
- [Acidanthera Team](https://github.com/acidanthera) for OpenCore Bootloader, its documentation, and KEXTS.
- [Dortania's OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/) for thorough explanation.
- [CaseySJ's Golden Build Guide](https://www.tonymacx86.com/threads/gigabyte-z490-vision-d-thunderbolt-3-i5-10400-amd-rx-580.298642/) for IO layout findings and Thunderbolt 3 hotplug fix SSDT.


Big thanks to Hackintosh community and those who contributed to make hackintosh possible from the beginning to the end!






