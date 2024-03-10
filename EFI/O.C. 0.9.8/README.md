# EFI Configuration

- EFI-FvBcm Wifi+BT - Configuration for FV-T919. OCLP 1.4.0+ Modern Wireless patch is still required for Broadcom Wi-Fi to function.

- EFI-Intel Wifi + FvBcm BT - Configuration for using built-in Intel AX201 Wi-Fi and FV-T919 Bluetooth.

- EFI-Intel Wifi+BT - Configuration for using built-in Intel AX201 Wi-Fi and Bluetooth.


#  Note 

- EFI builds here are configured for Sonoma 14.4

- Install/Update for Sonoma 14.4 may require setting SecureBootModel to Disabled(Needs to be done before the OS installation process) to avoid bootloop.

- For Intel Wi-Fi and Bluetooth, To significantly reduce the size of each respective kexts, the AirportItlwm and IntelBluetoothFirmware are compiled only with “iwlwifi-QuZ-a0-hr-b0-68” and “ibt-19-0-4” firmware which should work with AX201NGW module that comes with this motherboard.



  **Please note that the AirportItlwm.kext here is only compatible with Sonoma 14.4 and it will not work with Sonoma 14.3.1 and older. 