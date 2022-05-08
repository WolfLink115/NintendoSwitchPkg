# EDK2 Implementation for Nintendo Switch (Tegra210)

## Status
Capable to boot something from SD card. If you have a Linux kernel with EFI stub support, supply the device tree for any Tegra210 device (not limited to Nintendo Switch) should boot.

ACPI also boots Windows and Linux, but limited devices are provided (only CPU at this moment).

## Device Support
- CPU services: GIC and Arch Timer.
- Clocks (reset, PLL, etc.)
- Power Management (PMC, PMIC & regulators, etc.)
- GPIO and Pin Multiplexor.
- MicroSD (should support SDSC, HC. XC probed and have partition table shown, but not intensively tested). eMMC support will be added soon.
- Screen and FrameBuffer (need special [Coreboot](https://github.com/imbushuo/Coreboot))
- Side-band buttons, not yet registered as EFI Input Device.
- UART (Right Joy Con, 115200, 8n1)

## Windows Kernel Debugger Enablement

    bcdedit /store E:\EFI\Microsoft\Boot\BCD /set {default} debug on
    bcdedit /store E:\EFI\Microsoft\Boot\BCD /dbgsettings serial debugport:1 baudrate:115200
    
Plug in connector on the right-side Joy Con and connect to PC. Use WinDbg serial connection and it should work.

## Planned / In-Progress

- EHCI USB host
- eMMC
- Sideband buttons as input device
- Joy-Con (maybe not. Need high speed serial)

## Building Instructions
- https://github.com/WolfLink115/NintendoSwitchPkg/blob/master/buildinstructions.txt
# Special thanks to

- Imbushuo for making the EDK2 Package and editing Coreboot sources to work with display, as well as helping me with errors I had faced.

- Fail0verflow for making the shofel2 exploit and Switch Coreboot sources.

- The entirety of the Switch modding team for making this possible.

