# cm-io-board
# Flashing the Compute Module eMMC/Mirco SD

The Compute Module has an on-board eMMC/Mirco SD device connected to the primary SD card interface. This guide explains how to write data to the eMMC/Mirco SD storage using a CM_HAT.

Please also read the section on [Module booting and flashing the eMMC](cm-designguide.md#modulebootingandflashing) in the [Compute Module hardware design guide](cm-designguide.md).

## Steps to flash the eMMC/Mirco SD on a Compute Module

To flash the Compute Module eMMC/Mirco SD, you either need a Linux system (a Raspberry Pi is recommended, or Ubuntu on a PC) or a Windows system (Windows 7 is recommended). For BCM2837 (CM3/CM3L), a bug which affected the Mac has been fixed, so this will also work.

**Note** There is a bug in the BCM2835 bootloader which returns a slightly incorrect USB packet to the host. Most USB hosts seem to ignore this benign bug and work fine; we do however see some USB ports that don't work due to this bug. We don't quite understand why some ports fail, as it doesn't seem to be correlated with whether they are USB2 or USB3 (we have seen both types working), but it's likely to be specific to the host controller and driver. This bug has been fixed in BCM2837

**For Windows users**

Under Windows, an installer is available to install the required drivers and boot tool automatically. 

### Windows installer

For those who just want to enable the Compute Module eMMC/Mirco SD as a mass storage device under Windows, the standalone installer is the recommended option. This installer has been tested on Windows 10 32-bit and 64-bit, and Windows XP 32 bit.

Please ensure you are not writing to any USB devices whilst the installer is running.

1. Download and run the [Windows installer](CM-Boot-Installer.exe) to install the drivers and boot tool.
1. Plug your host PC USB into the CMIO USB SLAVE port.
1. Apply power to the CMIO board; Windows should now find the hardware and install the driver.
1. Once the driver installation is complete, run the `RPiBoot.exe` tool that was previously installed.
1. After a few seconds, the Compute Module eMMC will pop up under Windows as a disk (USB mass storage device).

## Writing to the eMMC/Mirco SD - Linux

After `rpiboot` completes, you will see a new device appear; this is commonly `/dev/sda` on a Pi but it could be another location such as `/dev/sdb`, so check in `/dev/` before running `rpiboot` so you can see what changes.

You now need to write a raw OS image (such as [Raspbian](http://downloads.raspberrypi.org/raspbian_latest)) to the device. Note the following command may take some time to complete, depending on the size of the image:

```bash
sudo dd if=raw_os_image_of_your_choice.img of=/dev/sda bs=4MiB
```

Once the image has been written, unplug and re-plug the USB; you should see two partitions appear (for Raspian) in `/dev`. In total, you should see something similar to this:

```bash
/dev/sda    <- Device
/dev/sda1   <- First partition (FAT)
/dev/sda2   <- Second partition (Linux filesystem)
```

The `/dev/sda1` and `/dev/sda2` partitions can now be mounted normally.

Make sure P3 (USB BOOT DISABLE) is set to the disabled position and/or nothing is plugged into the USB slave port. Power cycling the IO board should now result in the Compute Module booting from eMMC/Mirco SD. 
