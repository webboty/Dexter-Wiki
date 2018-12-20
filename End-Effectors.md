Table of contents:
- <a href="#tool-interfaces"> Tool Interfaces </a>
- <a href="#end-effectors"> End Effectors </a>
- <a href="#see-also"> See also </a>

# Tool Interfaces
## Version 1

The end point of the standard Dexter robot [hardware](Hardware) is a replaceable "tool interface" It has a shape which makes it easy for other tools to be clicked on and off. There are [spring loaded connectors, sometimes called "pogo pins"](https://www.mouser.com/ProductDetail/855-P70-2300045R) at the end for electrical connection, and space for a Tinyduino if additional processing of the signals is needed.

The signals available to the end effector include power, ground, and whatever signals the [main board](MicroZed) has been configured to produce. Early versions were wired to USB connection. Later version bring out the AUX1 and AUX2 GPIO pins from the FPGA via J20 and J21. These pins can be configured to produce different signals.
- There is a digital IO pin on the white connectors in the upper right on the back of the motor board<BR>
`Dexter.move_all_joints(0, 0, 0, 0, 0)`<BR>
`make_ins("S", "GripperMotor", 1), //Digital output pin. 0 = off 1 = 5v` TODO: Verify this?
- Another pin on that same connector is the PWM output pin.<BR>
`Dexter.move_all_joints(0, 0, 0, 0, 0)`<BR>
`make_ins("S", "EERoll", 512), //PWN pin. Integer range of 0-512`  TODO: Verify this?

## Version 2
The standard going forward will be a new tool interface which incorporates 2 [Dynamixel XL-320 servos](End-Effector-Servos) and a [Tinyscreen+](End-Effector-Screen) (ARM based, small OLED screen, 4 buttons, lots of IO). One FPGA IO pin will be configured to send and receive data via the [Dynamixel protocol 2.0](http://support.robotis.com/en/product/actuator/dynamixel_pro/communication.htm). This requires an update to the [FPGA](Gateware) image. <BR>
`Dexter.move_all_joints(0, 0, 0, 0, 0)`<BR>
`make_ins("S", "ServoSet2X", 2, char1 + char2<<8, char3+char4<<8)`
<BR>The Tinyscreen+ will listen on the servo bus and may return data there or via the dedicated return data line.

Signals from the Dexter [Motor Control PCB](Motor-Control-PCB) to the Tool Interface:
- Ground (P25 pin 1)
- Logic power +5 Volts (P25 pin 2)
- Supply power (18V DC from AC adapter J25?), regulated to Servo power voltage (6 - 8.75V) at the interface by the servo power supply board  See: [Parts](https://octopart.com/bom-tool/4UgoKwTw), [source](https://www.mouser.com/ProjectManager/ProjectDetail.aspx?AccessID=da6dc9e512), [Schematic and PCB design](https://workspace.circuitmaker.com/Projects/Details/James-Newton-2/Dexter-Tool-Interface)
- Servo data bus, bidirectional. AUX1 via J20 (bottom pin, on bottom connector left side of motor board)
- Return serial data. AUX2 via J21?
- The Dexter HD has 1 additional wire which can be used for the servo power (6 - 8.75V).

_Note: J20 and J21 accept TE Connectivity part 2-179694-2_
[^](http://www.te.com/usa-en/product-2-179694-2.html)
[^](https://www.mouser.com/ProductDetail/571-2-179694-2)
[^](https://www.digikey.com/product-detail/en/te-connectivity-amp-connectors/2-179694-2/A113162-ND/2135564)
. _A 2 position, 2mm, IDC, AMP CT connector. While this is an IDC part, the [crimp head](http://www.te.com/usa-en/product-58372-1.html) cost is insain even without the [crimp handle](http://www.te.com/usa-en/product-58074-1.html) so manually pressing it in with round nose tweezers or just [soldering the wire on](https://youtu.be/Vy5zcLWQZoc?t=26m3s) maybe the practical choice._

Multiple signals are available from the [Tinyscreen+](End-Effector-Screen) for many different purposes. A PCB to breakout those signals and make them accessible to end effectors via "spring loaded contacts" (pogos) is being designed. [Schematic and PCB](https://workspace.circuitmaker.com/Projects/Details/Caleb-Ho/Dexter-Tool-Interface-v2-Tinyscreen-Dynamixel-Copy)

# End effectors:
## for the Original Tool Interface
- http://hdrobotic.com/dexter-community/#!/grippers:marker-holder Simple Marker / Pen holder used to write or draw via [DDE's](DDE) DXF.init_drawing and txt.string_to_line functions. Hint: This functionality should allow Dexter to 3D print in the future.
- http://www.appropedia.org/User:Zsarnold#Enterprise See "Semester 2" A Mantis gripper was adapted to Dexter for a great demo of removing parts from a 3D printer. [Mantis Gripper Design](https://www.thingiverse.com/thing:1480408) and [Dexter adapter files](https://www.thingiverse.com/thing:2877079). Demo setup: [Video](https://www.youtube.com/watch?v=g7vwe5gVP3k), [Images](https://imgur.com/a/42GDrtg), and [DDE code](https://pastebin.com/EKfpEdBR). 

## New Servo Tool Interface
- n/a

# See also:
- Current Tool Interface CAD files (note: this will be replaced): [OnShape](https://cad.onshape.com/documents/2af8ed0e61a34ebf69284c68/w/72caf65e51bde98e456925d2/e/b03fb46577fe162df32757e9), [STL](https://www.thingiverse.com/download:3318346) There is room in the body for wiring and a [Tinyduino ](https://tinycircuits.com/collections/kits/products/tinyduino-basic-kit) interface device which can be attached with [slightly longer bolts](https://www.mcmaster.com/#91251a059/=17p3i1d). 
- http://hdrobotic.com/dexter-community/#!/grippers Our forum section for grippers. 
- https://www.eng.yale.edu/grablab/openhand/ These are good designs for open source grippers. They do NOT have the exact tool interface for Dexter, but could be adapted, or the tool interface part can be replace. 
