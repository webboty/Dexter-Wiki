The end point of the standard Dexter robot [hardware](Hardware) is a replaceable "tool interface" It has a shape which makes it easy for other tools to be clicked on and off. There are "pogo pins" at the end for electrical connection. 

The signals available to the end effector include power, ground, and whatever signals the main board has been configured to produce. Early versions supplied USB connection, some were configured to produce PWM type RC servo drive signals, but the standard going forward will be a new tool interface which incorporates 2 [Dynamixel XL-320 servos](End-Effector-Servos) and a [Tinyscreen+](End-Effector-Screen) (ARM based, small OLED screen, 4 buttons, lots of IO). The Tinyscreen+ will listen on the servo bus and may return data there or via the dedicated return data line. 

Signals from Dexter to the Tool Interface:
- Ground
- Logic power +5 Volts
- Supply power, regulated to Servo power voltage at the interface
- Servo data bus, bidirectional
- Return serial data

Multiple signals are available from the Tinyduino for many different purposes.

Known end effectors:
- Marker / Pen holder:<br>
http://hdrobotic.com/dexter-community/#!/grippers:marker-holder

See also:
- Current Tool Interface CAD files (note: this will be replaced): [OnShape](https://cad.onshape.com/documents/2af8ed0e61a34ebf69284c68/w/72caf65e51bde98e456925d2/e/b03fb46577fe162df32757e9), [STL](https://www.thingiverse.com/download:3318346) There is room in the body for wiring and a [Tinyduino ](https://tinycircuits.com/collections/kits/products/tinyduino-basic-kit) interface device which can be attached with [slightly longer bolts](https://www.mcmaster.com/#91251a059/=17p3i1d). 
- http://hdrobotic.com/dexter-community/#!/grippers Our forum section for grippers. 
- http://www.appropedia.org/User:Zsarnold#Enterprise See Semester 2 for a great demo of Dexter removing parts from a 3D printer. 
- https://www.eng.yale.edu/grablab/openhand/ These are good designs for open source grippers. They do NOT have the exact tool interface for Dexter, but could be adapted, or the tool interface part can be replace. 
