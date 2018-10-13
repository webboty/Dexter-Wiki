All of Dexters [Firmware](Firmware) and [Gateware](Gateware) as well as the Ubuntu OS and configuration files are stored on the micro SD card inserted into the microzed board. Although all the files on that card can be edited once Dexter is running, installing a new image is a quick way to make sure everything is setup correctly and is needed when building a new Dexter.

The location of the SD card is VERY difficult to access. It is on the opposite side of the [MicroZed board](MicroZed) from the USB Micro B at the bottom of the PCB, just left of center. If you have a fan installed for the stepper drivers, you will need to remove it. The contacts of the SD Card should be facing you, and pointing upward. 

## Writing a new SD Card Image
Start by downloading the image file and a program for writing the image ([USB Image Tool](http://www.alexpage.de/usb-image-tool/download/) is small at 0.4MB, open source, and known to work in Windows. [Etcher](https://etcher.io/) has versions for Mac, Linux, and Windows, but is ~60MB). You will need a micro SD card adapter if your PC doesn't have a slot built in, and an 8GB or larger micro SD card.

Open the image file in the program and write it to the SD card. Be very careful to write to the correct card. It is best to start with the card NOT inserted, and then insert it and refresh the list so you can see which card appears. In USB Image Tool, make sure "Device mode" is selected (not "Volume Mode") and click the card, click "Reset", and approve clearing all the partitions and data off the card. Click "Restore" and select the img file. Eject the card from the PC. With Dexter powered off, remove the old card (if present) and insert the freshly written card, then power on. 

If you get an "error 32" saying the device is already in use, the issue is likely that the program needs administrative rights. Right click the icon and select "Run as administrator"

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
The images should be just under 8GB in size and should fit on a standard 8GB card, if it is fully empty without any remaining partitions. In some cases however, the card may not actually have a full 8GB of data available. In this case, USB Image Tool will fail, Etcher will very quickly claim to have copied it but only the FAT partition (not the Ubuntu OS on the EXT4 partition) is transferred and (of course) it won't boot.

You can always use a larger (e.g. 16GB card) but a more advanced utility may be able to shrink the partition sizes to "shoehorn" it into the smaller space. "Paragon Backup" is able to do this, but 1) You have to have an SD card that is working (e.g. a 16GB with the 8GB image) so that you can make a backup in their format. 2) Insert the SD Card and manually delete the partition (it will NOT do that automatically even though it says it will) 3) Restore to the SD Card and let it reduce the size when it offers. 
