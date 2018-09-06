# Introduction
This script automates the process of building EFI application using the EDK II
development environment and sending them to the virtual disk of an OVMF based
virtual machine.

## Main Features
* Build and copy EFI applications
* Manage individual application profiles
* Dynamic configuration of paths and parameters
* Build and replace the OVMF firmware image

## Assumptions
* The script assumes you use [EDK II](https://github.com/tianocore/edk2) and **libvrt (qemu)** running [OVMF](https://github.com/tianocore/tianocore.github.io/wiki/OVMF) firmware
* You need to have root access permissions in order to mount the virtual disk
* The virtual disk must be a FAT32 raw image file named `OVMF_DISK.img`

## Future development plans
* Control more parameters of the EDK II build
* Support other virtual disks formats and paths
* Add an automatically generated UEFI startup script

# Virtual machine prerequisites
## Configuring libvirt
After installing **qemu**, **libvirt**, **[OVMF](https://github.com/tianocore/tianocore.github.io/wiki/How-to-build-OVMF)**, and **virt-manager**, add the path to
your OVMF firmware image and runtime variables template to your libvirt config
so `virt-install` or `virt-manager` can find those later on. Add the following
line to `/etc/libvirt/qemu.conf`:

```
nvram = [ "<PATH_TO_OVMF>/OVMF_CODE.fd:<PATH_TO_OVMF>/OVMF_VARS.fd" ]
```
## Creating a FAT32 raw image file
1. Create a file filled with zeros:  
`$ dd if=/dev/zero of=OVMF_DISK.img bs=1M count=100`
2. Use `fdisk OVMF_DISK.img` to create primary partition of type **W95 FAT32 (LBA)**.
3. Create the FAT32 file system in the image:  
`$ mkfs.vfat OVMF_DISK.img`

## Creating a OVMF VM using `virt-manager`
1. Press on **File** -> **New Virtual Machine** and select **Import existing disk image**.
2. Browse for the FAT32 raw image file you created.
3. On the final step of the wizard, select **Customize configuration before install**.
4. Change the **Firmware** to the **UEFI x86_64** option.
5. [Optional] Change **Chipset** to **Q35**.
6. [Optional] Go to the **CPUs** screen, uncheck **Copy host CPU configutaion** and  
on the **Model** field write **host-passthrough**.
7. [Optional] Go to **Disk 1** screen, change **Disk Bus** to **SATA** and **Cache mode** to **writethrough**.
8. Click on **Begin Installation**.


# Script Usage

## Installation
The script should be placed in a working directory where it will be able to save its profile and configurations files.

Before running the script, EDK II BaseTools should be [compiled](https://github.com/tianocore/tianocore.github.io/wiki/Common-instructions).
The build configurations file `edk2/Conf/target.txt` should be edited with the
following values: `TOOL_CHAIN_TAG = GCC5` and `TARGET_ARCH = X64`. Currently,
the script does not support other build configurations without editing it.

Run command `./efiappbnc --help` for more information.

##  Configurations
The script needs to be configured with some paths and parameters in order to
work. The configurations are saved to a `.config` file in the working directory.
If the file does not exist, the script will ask to set configurations. The script
will check for missing configurations when needed.

Run command `./efiappbnc configure` to edit existing configurations.

## Usage example
<pre>
<font size = "3"><span style="color:Lime;">leyaniv@leyaniv-Airtop2</span> <span style="color:Blue;">/work/TianoCore/efiappbnc $</span> ./efiappbnc
<span style="color:Lime;"><b>	===Edit configurations===</b></span>
1) OVMF virtual image file path: Not set
2) Number of threads to build:   Not set
3) EDK2 repository path:         Not set
<span style="color:DarkOrange;">Select a number to edit (Press enter to save and continue):</span> 1
<span style="color:DarkOrange;">Edit "OVMF virtual image file path":</span> /work/TianoCore/OVMF
1) OVMF virtual image file path: /work/TianoCore/OVMF
2) Number of threads to build:   Not set
3) EDK2 repository path:         Not set
<span style="color:DarkOrange;">Select a number to edit (Press enter to save and continue):</span> 2
<span style="color:DarkOrange;">Edit "Number of threads to build":</span> 14
1) OVMF virtual image file path: /work/TianoCore/OVMF
2) Number of threads to build:   14
3) EDK2 repository path:         Not set
<span style="color:DarkOrange;">Select a number to edit (Press enter to save and continue):</span> 3
<span style="color:DarkOrange;">Edit "EDK2 repository path":</span> /work/TianoCore/edk2
1) OVMF virtual image file path: /work/TianoCore/OVMF
2) Number of threads to build:   14
3) EDK2 repository path:         /work/TianoCore/edk2
<span style="color:DarkOrange;">Select a number to edit (Press enter to save and continue):</span>
<span style="color:Lime;">>></span> <b>Saving configurations</b>
<span style="color:Lime;">>></span> <b>Looking for available app profiles</b>
<span style="color:Lime;">>></span> <b>No profiles found, create a new one</b>
<span style="color:DarkOrange;">Input application name:</span> CLHello
<span style="color:Lime;"><b>	===EDK2 build===</b></span>
<span style="color:Lime;">>></span> <b>Building shell environment</b>
Loading previous configuration from /work/TianoCore/edk2/Conf/BuildEnv.sh
WORKSPACE: /work/TianoCore/edk2
EDK_TOOLS_PATH: /work/TianoCore/edk2/BaseTools
CONF_PATH: /work/TianoCore/edk2/Conf
<span style="color:DarkOrange;">Input platform name:</span> Compulab
<span style="color:DarkOrange;">Input package name:</span> CompulabPkg
<span style="color:DarkOrange;">Input package DSC file name:</span> CompulabPkg.dsc
<span style="color:Lime;">>></span> <b>Building package</b>
Build environment: Linux-4.13.0-45-generic-x86_64-with-LinuxMint-18.3-sylvia
Build start time: 17:50:09, Sep.06 2018

WORKSPACE        = /work/TianoCore/edk2
ECP_SOURCE       = /work/TianoCore/edk2/EdkCompatibilityPkg
EDK_SOURCE       = /work/TianoCore/edk2/EdkCompatibilityPkg
EFI_SOURCE       = /work/TianoCore/edk2/EdkCompatibilityPkg
EDK_TOOLS_PATH   = /work/TianoCore/edk2/BaseTools
CONF_PATH        = /work/TianoCore/edk2/Conf

Processing meta-data . done!
Building ... /work/TianoCore/edk2/ShellPkg/Library/UefiShellLib/UefiShellLib.inf [X64]
Building ... /work/TianoCore/edk2/ShellPkg/Library/UefiShellCEntryLib/UefiShellCEntryLib.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/UefiFileHandleLib/UefiFileHandleLib.inf [X64]
Building ... /work/TianoCore/edk2/MdeModulePkg/Library/UefiHiiLib/UefiHiiLib.inf [X64]
Building ... /work/TianoCore/edk2/MdeModulePkg/Library/UefiSortLib/UefiSortLib.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/UefiApplicationEntryPoint/UefiApplicationEntryPoint.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/UefiLib/UefiLib.inf [X64]
Building ... /work/TianoCore/edk2/MdeModulePkg/Library/UefiHiiServicesLib/UefiHiiServicesLib.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/UefiDevicePathLib/UefiDevicePathLib.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/UefiRuntimeServicesTableLib/UefiRuntimeServicesTableLib.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/UefiMemoryAllocationLib/UefiMemoryAllocationLib.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/UefiBootServicesTableLib/UefiBootServicesTableLib.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/BaseLib/BaseLib.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/BasePrintLib/BasePrintLib.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/UefiDebugLibConOut/UefiDebugLibConOut.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/BasePcdLibNull/BasePcdLibNull.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/BaseMemoryLib/BaseMemoryLib.inf [X64]
Building ... /work/TianoCore/edk2/MdePkg/Library/BaseDebugPrintErrorLevelLib/BaseDebugPrintErrorLevelLib.inf [X64]
Building ... /work/TianoCore/edk2/CompulabPkg/CLHello/CLHello.inf [X64]

- Done -
Build end time: 17:50:10, Sep.06 2018
Build total time: 00:00:01

<span style="color:Lime;"><b>	===Copy application to OVMF drive===</b></span>
Input application file name: CLHello.efi
<span style="color:Lime;">>></span> <b>Mounting OVMF drive image</b>
<span style="color:Lime;">>></span> <b>Copying application to mounted image</b>
<span style="color:Lime;">>></span> <b>Unmounting OVMF drive image</b>
<span style="color:Lime;">>></span> <b>Resseting OVMF qemu session</b>
Domain OVMF-RAW was reset

<span style="color:DarkOrange;">Do you want to save a app profile? (Press y/n):</span> y
<span style="color:Lime;">>></span> <b>Saving a profile for CLHello app</b>
<span style="color:Lime;">>></span> <b>Done!</b>
</font>
</pre>

# OVMF Tips & tricks
* Type `exit` on the EFI shell to get to the setup menu.
* To change the screen resolution, enter the setup menu, go to **Device Manager**,  
then **OVMF Platform Configuration**. Change the preferred resolution and exit.

## Console output modes
The console output text mode (number of rows and columns) can be changed using
the `mode` shell command. You might find the original selection of available
modes to be very limited on your OVMF firmware. To add additional modes to your
firmware, you need to edit the array `mTerminalConsoleModeData` with your desired
selection of modes.

The array is located in `MdeModulePkg/Universal/Console/TerminalDxe/Terminal.c`

For example (based on [this patch](http://resources.ovirt.org/pub/ovirt-3.6/src/ovmf/0019-MdeModulePkg-TerminalDxe-add-other-text-resolutions-.patch)):
```c
TERMINAL_CONSOLE_MODE_DATA mTerminalConsoleModeData[] = {
  {   80,  25 }, // from graphics resolution  640 x  480
  {   80,  50 }, // from graphics resolution  640 x  960
  {  100,  25 }, // from graphics resolution  800 x  480
  {  100,  31 }, // from graphics resolution  800 x  600
  {  104,  32 }, // from graphics resolution  832 x  624
  {  120,  33 }, // from graphics resolution  960 x  640
  {  128,  31 }, // from graphics resolution 1024 x  600
  {  128,  40 }, // from graphics resolution 1024 x  768
  {  144,  45 }, // from graphics resolution 1152 x  864
  {  144,  45 }, // from graphics resolution 1152 x  870
  {  160,  37 }, // from graphics resolution 1280 x  720
  {  160,  40 }, // from graphics resolution 1280 x  760
  {  160,  40 }, // from graphics resolution 1280 x  768
  {  160,  42 }, // from graphics resolution 1280 x  800
  {  160,  50 }, // from graphics resolution 1280 x  960
  {  160,  53 }, // from graphics resolution 1280 x 1024
  {  170,  40 }, // from graphics resolution 1360 x  768
  {  170,  40 }, // from graphics resolution 1366 x  768
  {  175,  55 }, // from graphics resolution 1400 x 1050
  {  180,  47 }, // from graphics resolution 1440 x  900
  {  200,  47 }, // from graphics resolution 1600 x  900
  {  200,  63 }, // from graphics resolution 1600 x 1200
  {  210,  55 }, // from graphics resolution 1680 x 1050
  {  240,  56 }, // from graphics resolution 1920 x 1080
  {  240,  63 }, // from graphics resolution 1920 x 1200
  {  240,  75 }, // from graphics resolution 1920 x 1440
  {  250, 105 }, // from graphics resolution 2000 x 2000
  {  256,  80 }, // from graphics resolution 2048 x 1536
  {  256, 107 }, // from graphics resolution 2048 x 2048
  {  320,  75 }, // from graphics resolution 2560 x 1440
  {  320,  84 }, // from graphics resolution 2560 x 1600
  {  320, 107 }, // from graphics resolution 2560 x 2048
  {  350, 110 }, // from graphics resolution 2800 x 2100
  {  400, 126 }, // from graphics resolution 3200 x 2400
  {  480, 113 }, // from graphics resolution 3840 x 2160
  {  512, 113 }, // from graphics resolution 4096 x 2160
  {  960, 227 }, // from graphics resolution 7680 x 4320
  { 1024, 227 }, // from graphics resolution 8192 x 4320
  //
  // New modes can be added here.
  //
};
```
## UEFI shell startup script
After copying the application binary to the virtual disk, the script will reset
the qemu session. A startup script can be useful to avoid typing the same
commands after each reset. The startup script is a file named `startup.nsh` that
is located at the root of the virtual disk. The following script is an example

```bash
echo -off
mode 128 40	# Change console output mode (suits 1024 x 768 resolution)
fs0:		# Go to the virtual drive
ls		# List files
echo -on
```
