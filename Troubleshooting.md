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