_WARNING: Use *only* SanDisk 16GB Ultra MicroSDHC UHS-I Memory Card with Adapter - 98MB/s, C10, U1, Full HD, A1, Micro SD Card - SDSQUAR-016G-GN6MA, and no other. Failure to use this specific card may fry the MicroZed._

https://www.amazon.com/dp/B073K14CVB/ref=cm_sw_r_other_apa_i_LgyvEbC27VXPP


All of Dexters [Firmware](Firmware) and [Gateware](Gateware) as well as the Ubuntu OS and configuration files are stored on the micro SD card inserted into the microzed board. Although all the files on that card can be edited once Dexter is running, installing a new image is a quick way to make sure everything is setup correctly and is needed when building a new Dexter.

There are two partitions on the card. 
- A FAT32 partition with the [FPGA](Gateware) .bit file. It can be used to transfer other files into the running system, but it is not mounted by default.
- An EXT4 Linux partition which contains the OS, and all the other files for the running system. 

The boot setup and selection of the partition for booting is controlled by u boot<BR>
http://processors.wiki.ti.com/index.php/Linux_Core_U-Boot_User%27s_Guide

The location of the SD card is VERY difficult to access. It is on the opposite side of the [MicroZed board](MicroZed) from the USB Micro B at the bottom of the PCB, just left of center. If you have a fan installed for the stepper drivers, you will need to remove it. The contacts of the SD Card should be facing you, and pointing upward. See [Issue 52](https://github.com/HaddingtonDynamics/Dexter/issues/52) for progress / workarounds.

It can help to use small pliers if they have a rough inner surface. Be very careful not to crush the card. Turn power off to avoid shorts. <BR>
![Re-inserting the SDCard with small pliers, held very gently](https://user-images.githubusercontent.com/419392/77209850-a736be00-6abc-11ea-9a6d-8ea40118f6dd.png)

## Writing a new SD Card Image
Start by downloading the image file (please contact us for the link, it's too big for github to host) and a program for writing the image ([USB Image Tool](http://www.alexpage.de/usb-image-tool/download/) is small at 0.4MB, open source, and known to work in Windows. [Etcher](https://etcher.io/) has versions for Mac, Linux, and Windows, but is ~60MB). You will need a micro SD card adapter if your PC doesn't have a slot built in, and an 8GB or larger micro SD card.

Open the image file in the program and write it to the SD card. Be very careful to write to the correct card. It is best to start with the card NOT inserted, and then insert it and refresh the list so you can see which card appears. In USB Image Tool, on the "Options" tab, you can turn on "Truncate oversized images in device mode..." then make sure "Device mode" is selected (not "Volume Mode") and click the card, click "Reset", and approve clearing all the partitions and data off the card. Click "Restore" and select the img file. Eject the card from the PC. With Dexter powered off, remove the old card (if present) and insert the freshly written card, then power on. 

If you get an "error 32" saying the device is already in use, or "error 6" the issue is likely that the program needs administrative rights. Right click the icon and select "Run as administrator"

On Linux systems, you can also do this from the command line. To be sure you have the right device, start with the SD card out of the PC, and at the command prompt, enter `journalctl -f` or `tail -f /var/log/syslog` if it isn't a systemd based version of Linux. Then insert the SD Card and watch the screen to see which device shows up. Ctrl+C to stop watching. You can also look at the output of the `mount` command to see a list of your main drives which should NOT be written. Once you are sure what device the SD card showed up as, enter the command: `dd bs=1M if=Dexter16_20190308.img of=/dev/xxx` replacing the xxx with the device name for the SD card, and replacing Dexter16_20190308.img with the current image you downloaded. _(Thanks to Scott for this info)_

## Delete partitions from SD Card
You may need to delete any existing partitions on the SD Card or allow the tool to do so. With USB Image Tool, you can use the "Reset" button. If the tool you are using can't remove the existing partition, you may need to delete it yourself before writing the image. 

### Windows
Under windows, you can use a partition editing tool or the command line `diskpart` utility. The GUI "Disk Management" tool will NOT allow you to delete partitions from most SD Cards. It does, however, provide a nice display to show you which drive letters are on which drives. To delete partitions, you probably need diskpart. **WARNING: You can EASILY delete all data from your computers hard drive with these tools! Diskpart especially will allow you to erase a hard drive in 4 easy steps:**
````
C:\Windows\system32>diskpart

Microsoft DiskPart version 6.1.7601
Copyright (C) 1999-2008 Microsoft Corporation.
On computer: YOUR COMPUTER NAME

DISKPART> list disk

  Disk ###  Status         Size     Free     Dyn  Gpt
  --------  -------------  -------  -------  ---  ---
  Disk 0    Online          465 GB      0 B
  Disk 1    Online          465 GB      0 B
  Disk 2    Online         7600 MB      0 B

DISKPART> select disk 2

Disk 2 is now the selected disk.

DISKPART> clean

DiskPart succeeded in cleaning the disk.
**!!! Every drive letter on Disk 2 is now GONE !!!**
DISKPART> exit

Leaving DiskPart...
````
Note the complete lack of conformation, concern, checking, or any form of empathy for the destruction of all data on drive 2! It's just GONE. If Disk 2 were actually a hard drive, the computer would be dead. 

### Ubuntu
To delete partitions:
- Open a terminal and type sudo su
- Type `fdisk -l` and note your USB drive letter. **Do not get this wrong**
- Type `fdisk /dev/sdx` (replacing x with your drive letter)
- Type `d` to proceed to delete a partition
- Type `1` to select the 1st partition and press enter
- Type `d` to proceed to delete another partition (fdisk should automatically select the second partition)
**WARNING: You can easily delete your own hard drive letters. But you are a Linux user, so...**

## SD Card Size
The images should be just under <strike>8GB</strike> 16GB in size and should fit on a standard 16GB card, if it is fully empty without any remaining partitions. In some cases however, the card may not actually have a full 16GB of data available. In this case, USB Image Tool will fail, Etcher will very quickly claim to have copied it but only the FAT partition (not the Ubuntu OS on the EXT4 partition) is transferred and (of course) it won't boot. Since we don't really need the empty space at the end of the image, in USB Image Tool, on the "Options" tab, you can turn on "Truncate oversized images in device mode..." and then be sure to set "Device Mode" at the top left.

You can always use a larger (e.g. 16GB card) but a more advanced utility may be able to shrink the partition sizes to "shoehorn" it into the smaller space. "Paragon Backup" is able to do this, but 1) You have to have an SD card that is working (e.g. a 16GB with the 8GB image) so that you can make a backup in their format. 2) Insert the SD Card and manually delete the partition (it will NOT do that automatically even though it says it will) 3) Restore to the SD Card and let it reduce the size when it offers. 

### Expanding the image to fill your SD Card
If you want more space on Dexter, and you have a larger SD Card (e.g. 16M or more) then you can expand the 2nd (Linux ext4) partition. 

_WARNING: This is dangerous and can easily lose all data. Be ready to reload the image: Make a backup if you have made changes._

First, SSH into Dexter

Check the size of the SD card and current partition with the `lsblk` command. For example:
````
root@localhost:~# lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
mmcblk0     179:0    0   15G  0 disk
|-mmcblk0p1 179:1    0   15M  0 part
`-mmcblk0p2 179:2    0  7.4G  0 part /
mtdblock0    31:0    0    4M  0 disk
````
Assuming you have additional space (mmcblk0 is larger than mmcblk0p1), you can fdisk the drive, delete the existing partition entry from the partition table, and make a new, larger, partition:
````
fdisk /dev/mmcblk0
````
- At the prompt, enter `d` to delete a partition, and select partition 2.
- Next, enter `n` to add a new partition, and just accept the default values for each question by pressing enter. If you like, on the "Last sector" prompt, you can specify the size of the partition by entering a plus sign, then the size with an M or G for Megabytes or Gigibytes. E.g. to make a 12GB partition (assuming an SD card with enough space) enter "+12G"
- Finish by select `w` to write the changes to the disk.

Note: You will see an error "Re-reading the partition table failed.: Device or resource busy" this is normal. 

To actually read in the new partition table, issue the command: 
````
partprobe
````
It should return without any messages. If it reports an error, try to fdisk again.

At this point, the new partition table is in place, but the data on the disk does not match it. To update the disk data, issue the `resize2fs` command on the updated partition:
````
resize2fs /dev/mmcblk0p2
````
At this point, everything is done; `lsblk` should show the new partition size and `df -h` should show that you have additional space on the file system.

## Checklist for Making New Image
- Ensure /etc/network/interfaces is setup for non-DHCP, but fixed IP address.
- `rm /var/lib/zerotier-one/identity.secret ` <br> `rm /var/lib/zerotier-one/identity.public` <br>so that a new ZeroTeir unique identity will be generated the first time it starts.
- Make sure Find_Index_Pulses_HDI.dde and PHUI2RCP.js are enabled at the end of RunDexRun.

After the image is made, try shrinking it on Ubuntu with <br>
https://github.com/Drewsif/PiShrink

## History
2017/00/00 The original image was based on Xilinux 1.3 which is basically Lubuntu 12. 

2019/03/08 a [new version](https://github.com/HaddingtonDynamics/Dexter/issues/25) was released based on Xilinux 2.0 which is basically Lubuntu 16.04. 

To find which version you are running, if you can [SSH into Dexter](https://github.com/HaddingtonDynamics/Dexter/wiki/Dexter-Networking#shell-access-via-ssh), run a `lsb_release -a` command. We strongly recommend upgrading to the latest image if at all possible, and using a new SD card will both allow you to maintain your old one as a backup, but also refresh the SD card as FLASH memory does have a limited lifetime. 