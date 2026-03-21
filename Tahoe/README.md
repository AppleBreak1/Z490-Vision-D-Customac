# NOTES

- Updated OpenCore to 1.0.6

- Updated kexts to support Tahoe

- Implemented ACPI USB Port mapping that does not require dropping Original USB Table

  - Mapped: HS03,4,5,6,7,8,9,10,11,13 SS03,04,05,06,07

- This EFI does not enable Wi-Fi settings. Will update this in the future once the OCLP 3.0 with Broadcom Wi-Fi patch is released by OCLP team

  - Currently, Intel Wi-Fi works with Itlwm.kext + Heliport on Tahoe. However, AppleVTD needs to be disabled.

- Intel I219/I225 are functional as of Tahoe 26.2

  - [IntelMausiEthernet.kext](https://github.com/Mieze/IntelMausiEthernet) for I219 (Works with or without AppleVTD)
  - Native AppleEthernetE1000 for I225 (Requires AppleVTD to be enabled)
  - [AppleIGC.kext](https://github.com/SongXiaoXi/AppleIGC) for I225 (Requires AppleVTD to be disabled on macOS Tahoe)

- ~~For systems with AMD dGPUs, current version of WhateverGreen causes hang during macOS Tahoe upgrade/installation. In order to bypass this, one needs to disable WhateverGreen during the Tahoe installation and enable it back once the installation is complete. Or, use laobamac_yyds' forked version of [WhateverGreen](https://www.insanelymac.com/forum/topic/361713-pre-release-macos-tahoe-261/page/9/#findComment-2843040) until fix is implemented by Acidenthera team~~ This issue is fixed with Lilu 1.7.2.

- AppleHDA.kext has been removed from macOS Tahoe. To get audio back, there are couple of options

   - Bring back AppleHDA.kext via root patching
   - Use VoodooHDA.kext

# ACPI USB Port Mapping

This method does not require dropping original USB Table.

1. Discover USB Ports

   - Boot with SSDT-RHUB.aml created by [SSDTTime](https://github.com/corpnewt/SSDTTime) and XhciPortLimit quirk enabled. This will enable every USB ports in the system on macOS.
   - Use IORegistryExplorer to discover all the needed USB Ports plugging USB2 and USB3 devices to each USB ports while making note of the address for each USB ports to be mapped.
   - Once the discovering part is finished, disable SSDT-RHUB.aml and XhciPortLmit quirk and move onto the next step.

2. Create ACPI USB Table.

   <img width="1043" height="826" alt="USB Map" src="https://github.com/user-attachments/assets/dac91d1f-5fa5-4411-bcb3-069dcc102cdd" />

   USB Port Address
   ~~~
   HS01   One     SS01  0x11
   HS02  0x02     SS02  0x12
   HS03  0x03     SS03  0x13
   HS04  0x04     SS04  0x14
   HS05  0x05     SS05  0x15
   HS06  0x06     SS06  0x16
   HS07  0x07     SS07  0x17
   HS08  0x08     SS08  0x18
   HS09  0x09     SS09  0x19
   HS10  0x0A     SS10  0x1A
   HS11  0x0B
   HS12  0x0C 
   HS13  0x0D
   HS14  0x0E
   USR1  0x0F
   USR2  0x10
   ~~~
   
3. Update Config.plist accordingly




























