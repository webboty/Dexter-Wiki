Note: Changing end effectors, assuming they have different lengths, changes Link Lengths, and that will make the [Kinematics](Kinematics) change. If you are using onboard Kin, via the ['M'](Command-oplet-instruction#M) and ['T'](Command-oplet-instruction#T) oplets, the [SetParameter LinkLengths](set-parameter-oplet#LinkLengths) needs to be updated with a single value to change length 5. 

In DDE, when using the "move_to" or other Kinematics commands, search the help panel for "link lengths" to ensure it is setup correctly.

[How to measure Length Links](https://github.com/cfry/dde/blob/master/doc/coor_images/Tooltip_Location.PNG?raw=true)

Table of contents:
- <a href="#tool-interfaces"> Tool Interfaces</a>: <a href="#version-1">Version 1: RC servos</a> or <a href="#version-2">Version 2: Dynamixel servos</a> 
- <a href="#end-effectors"> End Effectors </a>
- <a href="#see-also"> See also </a>

# Tool Interfaces
## Version 1

The end point of the standard Dexter robot [hardware](Hardware) is a replaceable "tool interface" It has a shape which makes it easy for other tools to be clicked on and off. There are [spring loaded connectors, sometimes called "pogo pins"](https://www.mouser.com/ProductDetail/855-P70-2300045R) at the end for electrical connection, and space for a Tinyduino if additional processing of the signals is needed.

The signals available to the end effector include power, ground, and whatever signals the [main board](MicroZed) has been configured to produce. Early versions were wired to USB connection. Later version bring out the AUX1 and AUX2 GPIO pins from the FPGA via J20 and J21 which can be configured to produce different signals. 

### J19 pin 7
IO pin 7 of connector J19 on bottom of PCB. This is controlled via the GripperMotor SetParameter. e.g.

`Dexter.move_all_joints(0, 0, 0, 0, 0)`<BR>
`make_ins("S", "GripperMotor", 1), //Digital output pin. 0 = off 1 = 5v`  TODO: Verify this?

The FPGA support high resolution PWM on this pin, via W Oplet address 73 to 75. Bit 0 of W oplet address 75 enables / disables this pin. e.g. `W 73 1` enables it. Address 74 controls the off time, and address 75 controls the on time. 

### J20 / J21 (Blue and Green wires)
AUX1 and AUX2 GPIO pins from the FPGA via J20 and J21. These are the white connectors in the upper right on the back of the motor board. On Dexter HD, the J20 pins are brought out to the end effector as the "green" and "blue" wires. Green is the top pin on J20, and blue is the bottom. 

In the original firmware, these were connected to the EERoll and EESpan SetParameter commands. 
`Dexter.move_all_joints(0, 0, 0, 0, 0)`<BR>
`make_ins("S", "EERoll", 512), //PWN pin. Integer range of 0-512`  

In [commit 42df0e01285ef8b67764ed53f3cc697df44d4d93](https://github.com/HaddingtonDynamics/Dexter/commit/42df0e01285ef8b67764ed53f3cc697df44d4d93#diff-691272021fae98368efb598f8e089c16R1562), Roll and Span were changed to instead expect Dynamixel servos. However, the wires can still be set manually via the [W oplet](https://github.com/HaddingtonDynamics/Dexter/wiki/oplet-write), at address 64-66. 

#### w oplet address 64:

Bit | Wire | Function
--- | --- | ---
6, 7 | Blue | `6,7 Mode`<BR>`0,0 GPIO` (see bits 0, 1) <BR>`0,1 PWM` High speed (not for servos) <BR>`1,0 RC PWM` For standard servos <BR>`1,1 Dynamixel` 
4, 5 | Green | `4,5 Mode`<BR>`0,0 GPIO` (see bits 2, 3) <BR>`0,1 PWM` High speed (not for servos) <BR>`1,0 RC PWM` For standard servos
3 | Green | Output level: When Tristate is 0 and mode GPIO, 0=low, 1=high
2 | Green | Tristate 0=output (high or low), 1=input
1 | Blue | Output level: When Tristate is 0 and mode GPIO, 0=low, 1=high
0 | Blue | Tristate: 0=output (high or low), 1=input

E.g. `w 64 80` sets both wires for RC servo PWM output. 80 = 1010000 binary. 

In PWM modes, the associated tristate bit must be zero, and the duty cycle is set via `w 65 _dutycycle_` or `w 66 _dutycycle_`. Instead of EESpan use `w 65 _dutycycle_` and for EERoll, `w 66 _dutycycle_` The _dutycycle_ value should be 591*degrees+1142000

## Version 2
![Version 2 Tool Interface Exploded View.](https://github.com/HaddingtonDynamics/Dexter/raw/master/Hardware/GripperAssembly.PNG)

The standard going forward will be a [new tool interface](https://www.thingiverse.com/thing:3166448) which incorporates 2 [Dynamixel XL-320 servos](End-Effector-Servos) and a [Tinyscreen+](End-Effector-Screen) (ARM based, small OLED screen, 4 buttons, lots of IO). One FPGA IO pin will be configured to send and receive data via the [Dynamixel protocol 2.0](http://support.robotis.com/en/product/actuator/dynamixel_pro/communication.htm). This requires an update to the [FPGA](Gateware) image. <BR>

To move the Dynamixel servos, the standard ["a" move all joints command ](Command-oplet-instruction#a) has been extended to include two more (optional) joint angles. E.g.:<BR>
`Dexter.move_all_joints(J1, J2, J3, J4, J5, J6, J7) //assumes variables J1-7 are set to degrees`<BR>
`make_ins("a", 0, 0, 0, 0, 0, 512, 100)`

Low level commands to directly communicate on the Dynamixel Protocol are also available e.g.<BR>
`make_ins("S", "ServoSet", servo_addr, register_addr, val_8bit)`<BR>
`make_ins("S", "ServoSet2X", servo_addr, register_addr, val_16bit)`

The Tinyscreen+ will listen on the servo bus and may return data there or via the dedicated return data line. e.g.<BR>
`make_ins("S", "ServoSet2X", 2, char1 + char2<<8, char3+char4<<8)`

Signals from the Dexter [Motor Control PCB](Motor-Control-PCB) to the Tool Interface:
- Black: Ground (J25 pin 1, top pin)
- Red: Logic power +5 Volts (J25 pin 2, the bottom pin). Note: Although out of spec, this actually appears to work very well as a power supply for the servos, making the next signal unnecessary.
- Supply power (18V to 36V DC from AC adapter J25?), Could be regulated to Servo power voltage (6 - 8.75V) at the interface by the servo power supply board  See: [Parts](https://octopart.com/bom-tool/4UgoKwTw), [source](https://www.mouser.com/ProjectManager/ProjectDetail.aspx?AccessID=da6dc9e512), [Schematic and PCB design](https://workspace.circuitmaker.com/Projects/Details/James-Newton-2/Dexter-Tool-Interface-Servo-Power-Supply). Note: The +5 supply line appears to work ok instead. 
- Blue: Servo data bus, bidirectional. AUX1 via J20 (bottom pin, on bottom connector left side of motor board)
- Green: Aux / Return serial data. AUX2 via J21?
- The Dexter HD has 1 additional wire which could be used for the servo power (6 - 8.75V) if we want to route that separately from the main supply power.

_Note: J20 and J21 accept TE Connectivity part 2-179694-2_
[^](http://www.te.com/usa-en/product-2-179694-2.html)
[^](https://www.mouser.com/ProductDetail/571-2-179694-2)
[^](https://www.digikey.com/product-detail/en/te-connectivity-amp-connectors/2-179694-2/A113162-ND/2135564)
. _A 2 position, 2mm, IDC, AMP CT connector. While this is an IDC part, the [crimp head](http://www.te.com/usa-en/product-58372-1.html) cost is insain even without the [crimp handle](http://www.te.com/usa-en/product-58074-1.html) so manually pressing it in with round nose tweezers or just [soldering the wire on](https://youtu.be/Vy5zcLWQZoc?t=26m3s) maybe the practical choice._

Multiple signals are available from the [Tinyscreen+](End-Effector-Screen) for many different purposes. A PCB to breakout those signals and make them accessible to end effectors via "spring loaded contacts" (pogos) is being designed. [Schematic and PCB](https://workspace.circuitmaker.com/Projects/Details/Caleb-Ho/Dexter-Tool-Interface-v2-Tinyscreen-Dynamixel-Copy)

Mechanically, the Tool Interface has a diamond shaped slide and a "power takeoff" (joint 7) to which end effectors are mounted. The slide can be lubricated with pencil graphite to ease insertion and removal of end effectors. The power takeoff should be rotated as shown below before inserting a new end effector. If you wish to leave the robot powered on while changing tools, position Joint 7 at about 170 degrees. (The raw value is 585) It should look like this:

![Correct orientation for insertion / removal of tools from the Version 2 Tool Changer.](https://user-images.githubusercontent.com/419392/69197573-a67a2500-0ae6-11ea-937c-cc6d421945ed.png)

If the end effector isn't all the way on, usually the power takeoff will pull it on the rest of the way with the first activation. 

# End effectors:
## for the Original Tool Interface
- http://hdrobotic.com/dexter-community/#!/grippers:marker-holder Simple Marker / Pen holder used to write or draw via [DDE's](DDE) DXF.init_drawing and txt.string_to_line functions. Hint: This functionality should allow Dexter to 3D print in the future.
- [Laser](http://hdrobotic.com/store/hd-gripper-6th-and-7th-axis-force-feedback-and-trainable-brsfj)
- [Laser with Microscope](http://hdrobotic.com/store/hd-gripper-6th-and-7th-axis-force-feedback-and-trainable-brsfj-9bh6n)
- http://www.appropedia.org/User:Zsarnold#Enterprise See "Semester 2" A Mantis gripper was adapted to Dexter for a great demo of removing parts from a 3D printer. [Mantis Gripper Design](https://www.thingiverse.com/thing:1480408) and [Dexter adapter files](https://www.thingiverse.com/thing:2877079). Demo setup: [Video](https://www.youtube.com/watch?v=g7vwe5gVP3k), [Images](https://imgur.com/a/42GDrtg), and [DDE code](https://pastebin.com/EKfpEdBR). 

## New Servo Tool Interface
- [Curved gripper](https://www.thingiverse.com/thing:3166448) 
- [Straight fingered gripper](https://www.thingiverse.com/thing:3166448)
<table><tr><td width=207 valign="top">
To insert the removable finger into the gripper, and install it into Tool Interface, first turn the knob so that the dial points to the line as shown here:<br>
<img src="https://user-images.githubusercontent.com/419392/57670426-1baeef80-75c4-11e9-8f35-2c820016c5ce.JPG"  width=207 height=155>
</td><td width=146 valign="top">
Then push the finger in until the dial points toward the Tool Interface, and slide it in.<br>
<img src="https://user-images.githubusercontent.com/419392/57670424-18b3ff00-75c4-11e9-9521-967dbbd6b59b.JPG" width=146 height=155>
</td></tr></table>

- [Cutting Shears](http://hdrobotic.com/store/hd-gripper-6th-and-7th-axis-force-feedback-and-trainable-brsfj-9bh6n-c7bmw)


# See also:
- Version 2 Tool Interface CAD files (note: needs to be expanded) [Starter in OpenJSCAD](https://openjscad.org/#https://raw.githubusercontent.com/JamesNewton/AdvancedRoboticsWithJavascript/master/cad/EndEffectorStart.jscad)
- Version 1 Tool Interface CAD files (note: this will be replaced): [OnShape](https://cad.onshape.com/documents/2af8ed0e61a34ebf69284c68/w/72caf65e51bde98e456925d2/e/b03fb46577fe162df32757e9), [STL](https://www.thingiverse.com/download:3318346) There is room in the body for wiring and a [Tinyduino ](https://tinycircuits.com/collections/kits/products/tinyduino-basic-kit) interface device which can be attached with [slightly longer bolts](https://www.mcmaster.com/#91251a059/=17p3i1d). 
- http://hdrobotic.com/dexter-community/#!/grippers Our forum section for grippers. 
- https://www.eng.yale.edu/grablab/openhand/ These are good designs for open source grippers. They do NOT have the exact tool interface for Dexter, but could be adapted, or the tool interface part can be replace. 
