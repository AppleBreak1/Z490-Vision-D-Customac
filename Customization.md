# Customizing

* [**Enable GPU Tab in Activity Monitor**](#Enable-GPU-Tab-in-Activity-Monitor)
* [**Show Both iGPU and dGPU in GPU Tab and GPU History Window**](#Show-Both-iGPU-and-dGPU-in-GPU-tab-and-GPU-History-Window)
* [**Fix iGPU Shown as "Intel KBL Unknown" in GPU History Window**](#Fix-iGPU-Shown-as-Intel-KBL-Unknown-in-GPU-History-Window)
* [**iGPU Utilization Tracking in GPU Tab and GPU History Window**](#iGPU-Utilization-in-GPU-Tab-and-GPU-History-Window)
* [**System Profiler**](#System-Profiler)
  - SATA and NVMe SSD Controller shown as Generic Controller
  - Intel I219 Ethernet Model name change
* [Spoofing AMD GPU Names Shown in Apps Like FCPX or Activity Monitor](#Spoofing-AMD-GPU-Names-Shown-in-Apps-Like-FCPX-or-Activity-Monitor)
___

# Enable GPU Tab in Activity Monitor

Run below command in terminal

**To Enable**</br>

```shell
defaults write com.apple.ActivityMonitor ShowGPUTab -bool true
```

**To Disable**</br>

```shell
defaults write com.apple.ActivityMonitor ShowGPUTab -bool false
```

Credit: [notjosh](https://github.com/notjosh) for the command.

# Show Both iGPU and dGPU in GPU tab and GPU History Window

There are two ways

- Use desktop type framebuffer with connectors such as 0x3E9B0007  (Expect longer boot times and if dGPU is the primary display controller, system might become unstable with more than one display connected)
- Use empty framebuffer such as 0x9BC50003 with either enable-metal property or igfxmetal=1 boot flag.  

# Fix iGPU Shown as Intel KBL Unknown in GPU History Window

- Inject device-id 0x3E9B

# iGPU Utilization Tracking in GPU Tab and GPU History Window

When you opt to use the desktop framebuffer with connectors, iGPU utilization tracking will work. However, with the empty framebuffer + "enable-metal" property, loading Apple's GuC firmware by injecting igfxfw is necessary and the system has to go through the sleep/wake cycle once in order to see the graphical representation of iGPU usage in Activity Monitor.

<img width="1321" alt="qqq" src="https://user-images.githubusercontent.com/97265013/215239222-c2882fd5-3ecb-40cc-a994-8958a4bf4398.png">

# System Profiler

  - Change SATA and NVMe Controllers Shown as Generic Controller in System Profiler

    - Find device path of SATA AHCI controller and NVME SSD controller then inject below properties.
  
      - SATA AHCI Controller device path -> name |String| pci8086,a182
      - NVME SSD Controller device path -> subsystem-id -> |Data| 04A80000 or 06A80000

        Note: If spoofing subsystem-id is not enough, you may need to spoof vendor-id and device-id

                 Vendor ID: 0x144d <D4140000>
                 Device ID: 0xA804 <04A80000> or 0xA806 <06A80000>
      
        <img width="1821" alt="Screenshot 2023-02-22 at 7 40 52 PM" src="https://user-images.githubusercontent.com/97265013/220816615-621f5de6-28a7-4b74-83fe-0b642c83ff74.png">


___
  - Change Intel I219 Ethernet name in System Profiler
  
    - When IntelMausi.kext is used for I219 ethernet, IntelMausi binary in /Contents/MacOS folder needs to be modfied with hex editor to change how it's shown in System Profiler. 
      
         <img width="1676" alt="Screenshot 2023-02-22 at 1 28 50 PM" src="https://user-images.githubusercontent.com/97265013/220765537-61d2f18f-7b04-4ad8-955f-f2f6e21ff292.png">
     
    - Alternatively, you might as well build the kext from its source after changing the model name.
      
# Spoofing AMD GPU Names Shown in Apps Like FCPX or Activity Monitor

  Under Device Path of dGPU
  - Inject ATY,DeviceName 
  - Inject ATY,FamilyName

    <img width="688" alt="Screenshot 2024-11-20 at 6 18 05 PM" src="https://github.com/user-attachments/assets/8048bfa4-4f1e-4fb5-b527-15f768679871">

    
