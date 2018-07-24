# [Dexter Hardware](https://github.com/HaddingtonDynamics/Dexter/blob/master/Hardware)

Hardware includes:
* 3D printed parts
* NEMA 17 Stepper Motors
* Harmonic or Wave drives
* Steel shafts and bearings
* Carbon fiber strakes and tubes
* [Motor driver / interface PCB](Motor-Control-PCB)
* Xilinx based microzed processor board [Schematic Rev F](http://microzed.org/sites/default/files/documentations/MicroZed_Rev_F_Schematic_141212.pdf)
* [micro SD card](SD-Card-Image)
* Wiring harness
* [End effector tool interface](End-Effectors)

Many different end effectors will be developed for Dexter, and they will be documented in the [End effector list](End-Effectors).

Dexter has 5 joints in the base model, and the Tool Interface adds 2 more for a total of 7.

|#	|FPGA	|Connector	|Human / Description            	|Slots	|Eye Rotation* |
| ----  | ----- | ------------- | ------------------------------------- | ----- | ------------ |
|1	|1	|Base		|Shoulder rotate                	| 200	|Counter	
|2	|3?**	|Pivot		|Shoulder lift	                	| 180	|Counter	
|3	|2?	|End		|Elbow set by R65/66            	| 157	|Counter	
|4	|4	|Angle		|Wrist: Differential combination 	| 115	|Counter	
|5	|5	|Rotate		|DiffA1 is rotate, DIffA2 is lift	| 100	|Clockwise	
|6	|6	|External	|(not used)		        	|	|
|6	| n/a	|(Dynamixel)	|Wrist rotate                   	| n/a	| n/a
|7	| n/a	|(Dynamixel)	|Gripper                        	| n/a	| n/a

\* _The direction of the rotation of the eye for Joint 5 is the opposite of the others._<BR>
** _Development of the FPGA code started in the middle and so the numbering of the joints is not consistent. _