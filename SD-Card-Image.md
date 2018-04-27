All of Dexters [Firmware](Firmware) and [Gateware](Gateware) as well as the Ubuntu OS and configuration files are stored on the micro SD card inserted into the microzed board. Although all the files on that card can be edited once Dexter is running, installing a new image is a quick way to make sure everything is setup correctly and is needed when building a new Dexter.

## Writing a new SD Card Image
Start by downloading the image file and a program for writing the image ([USB Image Tool](http://www.alexpage.de/usb-image-tool/download/) is small at 0.4MB, open source, and known to work in Windows. [Etcher](https://etcher.io/) has versions for Mac, Linux, and Windows, but is ~60MB). You will need a micro SD card adapter if your PC doesn't have a slot built in, and an 8GB or larger micro SD card.

Open the image file in the program and write it to the SD card. In USB Image Tool, click the card, click "Restore" and select the file. Be very careful to write to the correct card. It is best to start with the card NOT inserted, and then insert it and refresh the list so you can see which card appears. Eject the card from the PC. With Dexter powered off, remove the old card (if present) and insert the freshly written card, then power on. 

If you get an "error 32" saying the device is already in use, the issue is likely that the program needs administrative rights. Right click the icon and select "Run as administrator"

