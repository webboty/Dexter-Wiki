## At Power On
When powering on Dexter, you should see a series of lights. The processor board has 3 bright LEDs.
- green lights when the processor board is getting power.
- blue lights when the FPGA is programmed and ready for operation.
- red lights during disk access (to the SD card.)

### Power source
If you don't see any light, the power adapter may be disconnected, defective, etc...
- Check that the power adapter has a light on it. If it doesn't, check the wall outlet, power strip, etc... If it does have a light, check the cord to the robot, and the power connection there. If you don't find any obvious issues, the power brick may be defective, or the MicroZed board may be burned out. Testing the voltage output with a multimeter might be an option.

### Jumpers
If you see the green light, but don't see the blue or red light, check that the jumpers are on correcting on the [MicroZed](MicroZed) board. These are in the lower left corner, and control how the board boots. To boot from the SD card, the jumpers must be set as follows: JP1: 1-2, JP2: 2-3, JP3: 2-3<br>
![](https://user-images.githubusercontent.com/419392/59471887-65703d00-8df1-11e9-8719-f88cfd6d3ffb.png)

### SD Card
The [SD Card](SD-Card-Image) is difficult to insert and can easily be partially inserted or just fall out. It is on the opposite side of the MicroZed board from the USB Micro B at the bottom of the PCB, just left of center. If you have a fan installed for the stepper drivers, you will need to remove it. The contacts of the SD Card should be facing you, and pointing upward. 

## [Calibration](Encoder-Calibration)

### Freak Out

After calibration, when going into a closed loop mode where the [Encoders](Encoders) are feeding position information into the [FPGA](FPGA) to correct each joint to an exact location, one or more joints may start to shake or vibrate, and may squeal and lose position badly. Here is a [video of a customers Dexter in that state](https://youtu.be/S1g-IPPbs4I).

Most often, this is caused by a problem with the encoder. E.g. an LED or photosensor has come out of the hole in the encoder block, the encoder block has been pulled out of mesh with the disk, a connector is loose or a wire broken, or the encoder is simply not [calibrated](Encoder-Calibration) correctly. 

To diagnose this, in [DDE](DDE), from the "Calibrate Dexter..." dialog on the "Jobs" menu, do the eye calibration for the affected joint. Here is an example of an encoder which is NOT working correctly:<br>
![Bad encoder eye. Sensor pulled out of encoder block.](https://user-images.githubusercontent.com/419392/59715702-4f83c300-91c9-11e9-85df-87ec6569a9de.png)

And here is another example of one that is correct (after being fixed) showing the "eye" and the correct center dot placed by the user.<br>
![Good encoder, after replacing Sensor in hole of encoder block.](https://user-images.githubusercontent.com/419392/59716213-6d9df300-91ca-11e9-87d6-0b530f39fb61.png)

#### LED or Photo Sensor Loose

Here is an example of a Sensor that was accidentally pulled out of it's hole in the encoder block:<br>
![](https://user-images.githubusercontent.com/419392/59716415-cff6f380-91ca-11e9-94e7-980a95b79e56.png)

Replacing it and hot glueing it in place corrected the example issue shown with the eyes above. 