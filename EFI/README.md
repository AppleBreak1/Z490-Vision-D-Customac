OC 0.9.2 - Enabled [DisableIoMapperMapping](https://github.com/acidanthera/OpenCorePkg/pull/440) kernel quirk to fix WiFI/Ethernet issue on Ventura 13.3+ when AppleVTD is enabled

OC 0.9.3 - macOS Sonoma 14.0 beta ready

OC 0.9.5 - SSDT adjustments

&ensp;&ensp;&emsp;&emsp;&emsp;&nbsp;- Name change from SSDT-MCHCSBUS-DTPG.aml ->  SSDT-SBUS-MCHC.aml (DTGP removed and added to SSDT-TH3HP.aml)

&ensp;&ensp;&emsp;&emsp;&emsp;&nbsp;- Added HPTE=0 in SSDT-AWAC.aml to disable HPET

OC 0.9.8 - Configured specifically for Sonoma 14.4. Includes combination of Fenvi Broadcom (Wi-Fi/BT), Intel (WiFi/BT), and (Intel WiFi + Fenvi Broadcom Bluetooth).  