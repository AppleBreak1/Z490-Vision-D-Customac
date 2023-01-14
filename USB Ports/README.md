# Important Notes

This method modifies OEM USB Table SSDT-8.aml(where the USB Ports are declared). Modification includes limiting 11 ports using GUPC method and enabling 15 ports using TUPC method in Mac OS only. To make it work, first remove the previously configured USB port mapping method, then drop OEM SSDT-8 table in ACPI section of the config.plist and add patched SSDT-8.aml which is saved as SSDT-USBPorts.aml after the modificiation. This USB port mapping won't work unless SSDT-8 table is dropped.

Below example is based on F5 BIOS

<img width="1613" alt="Screenshot 2023-01-11 at 11 32 53 AM" src="https://user-images.githubusercontent.com/97265013/211901264-0c900a68-e956-4a99-b524-7dbcc30ae79b.png">


Config.plist

- ACPI -> Delete -> Drop OEM USB Table (SSDT-8.aml)
- ACPI -> Add - > SSDT-USBPorts.aml




The advantage of using this USB port mapping method.

   - Kextless
   - Will work on any SMBIOS
   - Does not affect other types of operating systems.

# Patches

Diable USB port

<img width="1030" alt="Screenshot 2022-08-25 at 12 31 08 PM" src="https://user-images.githubusercontent.com/97265013/186759663-d4de3add-d1fc-4d7f-9b71-31c41cec7ce6.png">

Enable USB port

<img width="953" alt="Screenshot 2022-08-25 at 12 45 21 PM" src="https://user-images.githubusercontent.com/97265013/186757334-912eced2-8fd2-45a6-956f-c90a9a476dab.png">

For USB port connector types please refer to [Dortania's USB Mapping Guide](https://dortania.github.io/OpenCore-Post-Install/usb/intel-mapping/intel.html)
   
# Mapped Ports

Ports: HS03, HS04, HS05, HS06, HS07, HS08, HS09, HS10, HS11, HS12, SS03, SS04, SS05, SS06, SS07 



IO Layout Credit ([CaseySJ](https://www.tonymacx86.com/threads/gigabyte-z490-vision-d-thunderbolt-3-i5-10400-amd-rx-580.298642/))

![Vision D IO Panel V2](https://user-images.githubusercontent.com/97265013/211897543-9e6875f9-10e9-4e0f-83e5-6ef05a98545b.png)
![Vision D Main Board V3](https://user-images.githubusercontent.com/97265013/211897549-33223c71-48c7-4d5b-80b5-d98d08c55766.png)


