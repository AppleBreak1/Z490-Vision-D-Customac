# Important Notes

This method modifies the OEM table where the USB Ports are declared(SSDT-8.aml for F5 BIOS). Modification includes limiting 11 ports using its GUPC method and enabling 15 ports using its TUPC method in Mac OS only.

Before proceeding with this method, you'll first need to have discovered which ports to enable and to disable for [limiting the USB port counts to 15 per controller](https://dortania.github.io/OpenCore-Post-Install/usb/#macos-and-the-15-port-limit). You may use scripts like [USBMap](https://github.com/corpnewt/USBMap) or discover the ports [manually](https://dortania.github.io/OpenCore-Post-Install/usb/manual/manual.html#usb-mapping-the-manual-way). 

Note that different BIOS versions may have different names for the table where the USB ports are declared.

# Requirements

- Modify the OEM table for USB (SSDT-8.aml for F5 BIOS) to satisfy the 15 port limit
- Inject the modified USB table (Preferably renamed after the modification to make it more easily identifiable)
- Drop the OEM table for USB (SSDT-8.aml for F5 BIOS) in ACPI section of the config.plist
- Remove the previously configured USB port mapping method
  
___

Below example is based on F5 BIOS 

<img width="1613" alt="Screenshot 2023-01-11 at 11 32 53 AM" src="https://user-images.githubusercontent.com/97265013/211901264-0c900a68-e956-4a99-b524-7dbcc30ae79b.png">


Config.plist

- ACPI -> Delete -> Drop the OEM table for USB (SSDT-8.aml for F5 BIOS)
- ACPI -> Add - > SSDT-USBPorts.aml (Renamed from SSDT-8.aml after the modification)


The advantage of using ACPI USB port mapping method.

   - Kextless
   - USB ports are defined at the ACPI level and is unlikely to break because of future macOS updates
   - Will work on any SMBIOS
   - Does not affect other types of operating systems

         Note that if the changes are made to the OEM table due to BIOS updates, steps to
         modify and drop the OEM table for USB may need to be redone accordingly. 


# Modification
Defined GUPC and TUPC Method
<img width="1243" alt="3" src="https://github.com/AppleBreak1/Z390-Designare-Customac/assets/97265013/93f4d5be-c4b6-459d-b6d9-6c7ba9ff35c4">
<br>
<br>

Looking at the above two methods used for _UPC object in OEM table for USB, we can see the following.


   - GUPC and TUPC both take one argument (Arg0)
     
   - GUPC(Arg0=PCKG[Zero]) defines connectable status of the port (By default, GUPC disables(Zero) USB port and defines connector type as 0xFF(Internal)
     
   - TUPC(Arg0=PCKG[One]) defines USB connector type (By default, TUPC enables(One) USB port and sets connector type as Zero(USB1/USB2)

___
For its intended function of each USB port on macOS, limiting the number of USB ports to 15 per USB controller and correctly defining the USB connector type for each enabled USB port are necessary. 

As we can see, both of the defined GUPC and TUPC method above only take one argument(GUPC=Enable/Disable USB port; TUPC=USB connector type). The methods can be altered to take two arguments; however, without redefining the methods, we can use both GUPC and TUPC to map the USB ports as below.

   - Use GUPC to disable USB port (If GUPC is called to enable USB port, we cannot control the connector type being defined for USB port individually. Instead, connector type of the enabled USB port with GUPC method will always be defined as 0xFF(Internal) as this is the default for the defined GUPC method and there is no second argument to control connector type. Thus, use GUPC for the purpose of disabling USB port)

      ~~~
      Return (GUPC (Zero)) -> Disable USB port
      ~~~
                 
   - Use TUPC to enable USB port and define USB connector type (By default, calling the given TUPC method enables USB port as it is how it's defined and we can selectively and individually set USB connector type for each of its enabled USB port by changing the value of it's argument. However, it cannot be called to disable USB port unless we rewrite the TUPC method)

      ~~~
      Return (TUPC (0x03)) -> Define USB connector type as 0x03 (Type-A USB2/USB3)
      ~~~
      
   - Add If (_OSI (" Darwin ")) statement to allow above method to be used in Mac OS only and leave everything else intact for other OSes.

<br>

**Example**

- Disable USB port: GUPC
  
<img width="1151" alt="1" src="https://github.com/user-attachments/assets/08ea688d-2eed-4274-bcb3-69801f8e749d">

___
- Enable USB port: TUPC
  
<img width="1160" alt="2" src="https://github.com/user-attachments/assets/6f576c25-3853-4246-a336-2a864eb5017e">

The _PLD (Physical Location Description) object is not very important for USB port mapping. For the USB ports to be enabled, may leave it as is and for the USB ports to be disabled, modify the first variable (Arg0) of GPLD or TPLD to Zero. You can ignore the second variable (Arg1) as well.

     Return (GPLD (Zero, 0x03))     -> Port not visible (Zero)     

  - Do note that modifying _PLD and variables for GPLD and TPLD may not be necessary at all. May as well leave them untouched.
___
USB connector types for each port

- HS01/SS01 = 0x09

- HS03/SS03/HS04/SS04/HS05/SS05/HS06/SS06/HS07/SS07 = 0x03

- HS08/HS13 = 0x0A

- HS09/HS10 = 0x00

- HS11 = 0xFF (Define as Internal(0xFF) if Bluetooth is connected. Otherwise, Zero(USB2))
  
- HS12 = 0xFF 
  
- HS14= 0xFF  (Onboard Intel CNVI module, disable if not used)


For more details of USB port connector types, please refer to [Dortania's USB Mapping Guide](https://dortania.github.io/OpenCore-Post-Install/usb/intel-mapping/intel.html)
   
# Mapped USB Ports

Enabled Ports(15): HS03, HS04, HS05, HS06, HS07, HS08, HS09, HS10, HS11, HS12, SS03, SS04, SS05, SS06, SS07 

Disabled Ports(11): HS01, HS02, HS13, HS14, SS01, SS02, SS08, SS09, SS10, USR1, USR2

![Vision D IO Panel V2](https://user-images.githubusercontent.com/97265013/211897543-9e6875f9-10e9-4e0f-83e5-6ef05a98545b.png)
![Vision D Main Board V3](https://user-images.githubusercontent.com/97265013/211897549-33223c71-48c7-4d5b-80b5-d98d08c55766.png)

IO Layout Credit ([CaseySJ](https://www.tonymacx86.com/threads/gigabyte-z490-vision-d-thunderbolt-3-i5-10400-amd-rx-580.298642/))

# Activating more than 15 USB ports

Activating more than 15 USB ports require XhciPortLimit quirk to be enabled.  Normally when XhciPortLimit quirk is used with OEM USB table, all the USB ports returning (GUPC (One)) will be activated with connector type 0xFF as this is the default for GUPC method. However, with the modified USB table, we can manually activate only the needed USB ports beyond 15 USB ports using TUPC method and correctly define USB connector type. The USB ports to be disabled will need to return (GUPC (Zero))

Notes:

- OpenCore 0.9.3+ is required for XhciPortLimit quirk to be fully functional in Big Sur 11.3+
- While using modified USB table addresses most of the concerns listed [here]( https://dortania.github.io/OpenCore-Post-Install/usb/), the potential data corruption issue from using XhciPortlimit still remains which I have not yet experienced.

# Resources

https://learn.microsoft.com/en-us/windows-hardware/drivers/install/using-acpi-to-configure-usb-ports-on-a-computer

https://www.macos86.it/topic/9-mappatura-porte-usb/

https://dortania.github.io/OpenCore-Post-Install/usb/
