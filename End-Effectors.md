The end point of the standard Dexter robot [hardware](Hardware) is a replaceable "tool interface" It has a shape which makes it easy for other tools to be clicked on and off. There are "pogo pins" at the end for electrical connection. The tool interface has room for wiring and a [Tinyduino ](https://tinycircuits.com/collections/kits/products/tinyduino-basic-kit) interface device which is attached with [slightly longer bolts](https://www.mcmaster.com/#91251a059/=17p3i1d). 

The signals available to the end effector include power, ground, and whatever signals the main board has been configured to produce. Early versions supplied USB connection, some were configured to produce PWM type RC servo drive signals, but the standard going forward will be a Dynamixel XL-320 servo bus and a return serial data line. The Tinyduino will listen on the servo bus and may return data there or via the dedicated return data line. 

Signals from Dexter to the Tool Interface:
- Groun
- Logic power +5 Volts
- Servo power
- Servo bus
- Return serial data

Multiple signals are available from the Tinyduino for many different purposes.

Known end effectors:
- Marker / Pen holder:<br>
http://hdrobotic.com/dexter-community/#!/grippers:marker-holder

See also:
- Tool Interface CAD files: [OnShape](https://cad.onshape.com/documents/2af8ed0e61a34ebf69284c68/w/72caf65e51bde98e456925d2/e/b03fb46577fe162df32757e9), [STL](https://www.thingiverse.com/download:3318346)
- http://hdrobotic.com/dexter-community/#!/grippers Our forum section for grippers. 
- https://www.eng.yale.edu/grablab/openhand/ These are good designs for open source grippers. They do NOT have the exact tool interface for Dexter, but could be adapted, or the tool interface part can be replace. 
