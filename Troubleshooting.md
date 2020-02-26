## At Power On
When powering on Dexter, you should see a series of lights. The processor board has 3 bright LEDs.
- green (labeled "D5") lights when the processor board is getting power.
- blue (labeled "D2") lights when the FPGA is loaded and ready for operation.
- red (labeled "D3") lights during disk access (to the SD card.)

### Power source
If you don't see any light, the power adapter may be disconnected, defective, etc...
- Check that the power adapter has a light on it. If it doesn't, check the wall outlet, power strip, etc... If it does have a light, check the cord to the robot, and the power connection there. If you don't find any obvious issues, the power brick may be defective, or the MicroZed board may be burned out. Testing the voltage output with a multimeter might be an option.

### Jumpers
If you see the green light, but don't see the blue or red light, or if you are unable to connect even if you do see the blue light, check that the jumpers are on correcting on the [MicroZed](MicroZed) board. These are in the lower left corner, and control how the board boots. To boot from the SD card, the jumpers must be set as follows: JP1: 1-2, JP2: 2-3, JP3: 2-3<br>
![](https://user-images.githubusercontent.com/419392/59471887-65703d00-8df1-11e9-8719-f88cfd6d3ffb.png)

### SD Card
The [SD Card](SD-Card-Image) can be difficult to get into the socket correctly and can easily be partially inserted or just fall out. It is on the opposite side of the MicroZed board from the USB Micro B at the bottom of the PCB, just left of center. If you have a fan installed for the stepper drivers, you will need to remove it. You can't really see the socket to get the correct orientation, so make sure the contacts of the SD card are facing you, and on the top.

When you push it up, it may jam or want to twist to the side. Keep moving it around a bit until you can feel it working against a spring, and moving up and down smoothly then press it in until it sort of clicks, not that you will hear the click, but it will suddenly not press back against you as you lower your finger, and you can press it up again and it will release, then again and it will stay. 

### Console connection
If the robot doesn't seem to be booting, or you can't connect via DDE or ping, a great way to troubleshoot is by using the built in console port. This will allow you to watch the MicroZed board boot (which it will start to do even without an SD Card) and so you can see exactly where it stops or what error messages it displays. For detailed directions see [Dexter USB Connection](Dexter-USB-Connection)

If you don't see any messages, even after pressing the reset (SW2) button on the board, then the SD card may be defective, installed incorrectly, or may not have the correct image.

### Boot test
If the SD card has a bad image the system might try to boot, but not complete. 

One way to eliminate the possibility of the SD card image being the issue is to just format the SD card using the FAT32 File System. [In Windows 10, use the command line tool to access the FAT32 format.](https://superuser.com/a/1179872/138765). In Linux or Mac, just right click the SD card in the file manager and select format, then FAT32. You can test the SD card in your PC by writing files to it, and reading them back. The details of the drive should show that the File System on it is formatted as FAT32.

Then, to boot the MicroZed card with a formatted SD card, change the boot jumpers to use QSPI which is the internal boot rom. Those settings are JP1:1-2, JP2:1-2, JP3:2-3.  Note, the SD card must be inserted, and must be formatted FAT32, but it doesn't have to have anything on it. If you have the main power connection for Dexter plugged in, you will need to disconnect and reconnect it; just pressing the reset switch doesn't change the boot mode.

If you get a blue light after about 10 seconds, and get a `zynq>` prompt on the console connection (see above), then the FGPA is probably good and the problem was a bad image on the SD card. Be sure to change back to  JP1:1-2, JP22-3, JP3:2-3 to boot from the SD card after you [re-image](SD-Card-Image#writing-a-new-sd-card-image) it. And if you have the main power connection plugged in, you will need to disconnect and re-connect it; just pressing the reset switch doesn't change the boot mode.

If you still don't get a blue light, and you are sure the SD card is formatted FAT32 and is inserted correctly, then the FPGA board may need to be replaced. You might want to remove the MicroZed board from Dexter, so that only a know good FAT 32 SD card is connected, and then power it via the USB cable from your PC. If you get the blue light and `zynq>` prompt then the issue is the Green HDR Motor board. 

### Freak Out

When going into a closed loop mode where the [Encoders](Encoders) are feeding position information into the [FPGA](FPGA) to correct each joint to an exact location, one or more joints may start to shake or vibrate, and may squeal and lose position badly. Here is a [video of a customers Dexter in that state](https://youtu.be/S1g-IPPbs4I).

Most often, this is caused by a problem with the encoder. E.g. an LED or photosensor has come out of the hole in the encoder block, the encoder block has been pulled out of mesh with the disk, a connector is loose or a wire broken, or the encoder is simply not [calibrated](Encoder-Calibration) correctly. 

To diagnose this, in [DDE](DDE), from the "Calibrate Dexter..." dialog on the "Jobs" menu, do the eye calibration for the affected joint. Here is an example of an encoder which is NOT working correctly:<br>
![Bad encoder eye. Sensor pulled out of encoder block.](https://user-images.githubusercontent.com/419392/59715702-4f83c300-91c9-11e9-85df-87ec6569a9de.png)

And here is another example of one that is correct (after being fixed) showing the "eye" and the correct center dot placed by the user.<br>
![Good encoder, after replacing Sensor in hole of encoder block.](https://user-images.githubusercontent.com/419392/59716213-6d9df300-91ca-11e9-87d6-0b530f39fb61.png)

#### LED or Photo Sensor Loose

Here is an example of a Sensor that was accidentally pulled out of it's hole in the encoder block:<br>
![](https://user-images.githubusercontent.com/419392/59716415-cff6f380-91ca-11e9-94e7-980a95b79e56.png)

Replacing it and hot glueing it in place corrected the example issue shown with the eyes above. 

### Failure to find home

The defining feature of the Dexter HDI is it's ability to find home position automatically after power cycle. Previous versions needed to be manually homed before being turned on. If something is wrong with calibration, or if there is a mechanical problem, this may fail and the robot will not be accessible except via [SSH](Dexter-Networking) or [Console Cable connection](Dexter-USB-Connection). 

To find the cause of the issue, we need to first stop Dexter from trying to find home on bootup. First, connect to Dexters command line via [SSH](Dexter-Networking) or [Console Cable connection](Dexter-USB-Connection). At the command prompt, enter `cd /srv/samba/share` and then, from that directory, run `nano RunDexRun` to edit that file. Use the down arrow or PgDn key to move to the bottom of the file. Find the line that mentions `Find_Index_Pulses`. The entire line is:<BR>
`sleep 5 && sudo node core define_and_start_job /srv/samba/share/dde_apps/Find_Index_Pulses_HDI.dde`<br>
comment out this line by inserting a '#' at the start of the line. Press Ctrl-X to close nano, and answer "Y" when asked to save the file and confirm the same file name by just pressing Enter. 

Now power cycle the robot. Wait for it to "twitch" the end effector and then you can start DDE and connect to the robot. Note that you are in open loop mode, so PID moves will not work. Perform the [calibrated](Encoder-Calibration) procedure and look for problems with the encoder eyes, or with the joints not moving correctly through the entire range of motion. Do NOT save the calibration results! If you find problems, contact us. 
