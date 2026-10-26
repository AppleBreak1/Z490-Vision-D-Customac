# EFI Configuration

- EFI-FvBcm Wifi+BT
  - Configuration for FV-T919. 
- EFI-Intel Wifi+BT
  - Configuration for using built-in Intel AX201 Wi-Fi and Bluetooth.


#  Important Notes 

- Note that these EFIs are specifically configured for macOS Tahoe for Wifi and Bluetooth functionality.
  
- Broadcom Wi-Fi does not require system root patches for functional Wi-Fi with AppleVTD enabled on macOS Tahoe, thanks to [Laobamac](https://github.com/laobamac) for [SimpleBCM](https://github.com/laobamac/SimpleBCM) kext and [OCLP](https://github.com/dortania/Opencore-Legacy-Patcher) devs for [AirPortBrcmNIC-Tahoe.kext](https://github.com/dortania/OpenCore-Legacy-Patcher/tree/main/payloads/Kexts/Wifi) for AppleVTD patch.
   
    - IOSkywalkFamily.kext still needs to be downgraded for Broadcom Wi-Fi, and because of this, Intel I225 ethernet that relies on IOSkywalkFamily will not work with DEXT driver. Also, AppleIGC will not work on macOS Tahoe with AppleVTD enabled. You'll need the AppleIntelI210Ethernet.kext from macOS Monterey for this which is not included in the EFI.

- Intel Wi-Fi does not require system root patches for functional Wi-Fi with AppleVTD enabled on macOS Tahoe, thanks to [Laobamac](https://github.com/laobamac) for [AirportItlwm](https://github.com/laobamac/itlwm) kext.

- Since the kexts are injected via bootloader, Wi-Fi continues to function in macOS Tahoe Recovery environment.
  
- To significantly reduce the size of each respective kexts for Intel Wi-Fi and Bluetooth, the AirportItlwm and IntelBluetoothFirmware are compiled only with “iwlwifi-QuZ-a0-hr-b0-68” and “ibt-19-0-4” firmwares which work with AX201NGW module that is shipped with this motherboard.

- System root patch for onboard audio on macOS Tahoe is not required thanks to [Laobamac](https://github.com/laobamac) for [SimpleHDA](https://github.com/laobamac/SimpleHDA) kext.

- Thanks to [lshbluesky](https://github.com/lshbluesky) for updated [IntelBluetoothFirmware](https://github.com/lshbluesky/IntelBluetoothFirmware) for macOS Tahoe.

- Implements ACPI USB mapping that does not require dropping original USB Table. Explained [here](https://github.com/AppleBreak1/Z490-Vision-D-Customac/tree/Tahoe/Tahoe#acpi-usb-port-mapping)

- Install/Update for Sonoma 14.4+ requires setting SecureBootModel to Disabled to avoid bootloop. SecureBootModel can be enabled afterwards.

  
