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

# Building Instructions

## Install dependencies
- sudo apt-get install build-essential uuid-dev iasl git python3-distutils gcc-arm-linux-gnueabi gcc-aarch64-linux-gn
    ### Install Powershell
    - sudo apt-get update
    - sudo apt-get install -y wget apt-transport-https software-properties-common
    - wget -q https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
    - sudo dpkg -i packages-microsoft-prod.deb
    - sudo apt-get update
    - sudo apt-get install -y powershell

## Clone Repositories
- git clone https://github.com/fail0verflow/shofel2.git
- git clone https://github.com/WolfLink115/Coreboot.git --recursive
- git clone https://github.com/tianocore/edk2.git --recursive
    ### Clone NintendoSwitchPkg inside of the EDK2 source
    - https://github.com/WolfLink115/NintendoSwitchPkg.git
    ### Clone Brotli Inside of edk2 BaseTools and MdeModulePkg if it didn't when cloning
    - `rm -rf edk2/BaseTools/Source/C/BrotliCompress/brotli && git clone https://github.com/google/brotli.git edk2/BaseTools/Source/C/BrotliCompress`
    - `rm -rf edk2/MdeModulePkg/Library/BrotliCustomDecompressLib/brotli && git clone https://github.com/google/brotli.git`

## Build ShofEL2 exploit, EDK2, and Coreboot
- cd shofel2/exploit && make
- cd ../edk2
- source edksetup.sh
- make -C BaseTools/
- cp NintendoSwitchPkg/Tools/run-build.sh . && ./run-build.sh
- cd ../Coreboot && make nintendo_switch_defconfig && make

## After all that we should now have a coreboot.rom file in the build folder. Now we can try to boot edk2.
- cd ../shofel2/exploit
- sudo ./shofel2.py cbfs.bin ../../Coreboot/build/coreboot.rom

### If all went well, your Nintendo Switch should now be booting EDK2. Thank you for reading!

### If you run into a USB error, and you are using USB2.0, try using a USB3.0 port. If your pc doesn't have a USB3.0 port, there is a .patch file in the shofel2 source that you apply to your kernel. That will allow USB2.0 to work with shofel2. If you don't want to do that, or you just want to make things easier on yourself, you can use tegrarcmsmash.exe on a Windows install.

# Special thanks to

- Imbushuo for making the EDK2 Package and editing Coreboot sources to work with display, as well as helping me with errors I had faced.

- Fail0verflow for making the shofel2 exploit and Switch Coreboot sources.

- The entirety of the Switch modding team for making this possible.

