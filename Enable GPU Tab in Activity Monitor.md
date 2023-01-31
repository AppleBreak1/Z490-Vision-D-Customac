# Enabling GPU Tab in Activity Monitor

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

# Show both iGPU and dGPU in GPU tab and GPU History Window

There are two ways

- Use desktop type framebuffer such as 0x3E9B0007  (Expect longer boot times and if dGPU is the primary display controller, system might become unstable with more than one display connected)
- Use empty framebuffer such as 0x9BC50003 with either enable-metal property or igfxmetal=1 boot flag.  

# Fix iGPU shown as" Intel KBL Unknown" in GPU History Window

- Inject device-id 0x3E9B

# iGPU utilization in GPU Tab and GPU History Window.

When you opt to use the desktop framebuffer it will just work. However, with the empty framebuffer + "enable-metal" property, system has to go through the sleep/wake cycle once in order to see the actual iGPU usage in Activity Monitor.

<img width="1321" alt="qqq" src="https://user-images.githubusercontent.com/97265013/215239222-c2882fd5-3ecb-40cc-a994-8958a4bf4398.png">
