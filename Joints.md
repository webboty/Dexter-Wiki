Dexter's [hardware](Hardware) has 5 joints in the base model, and the v2 [Tool Interface](End-Effectors) adds 2 more for a total of 7.

|#	|FPGA	|Connector	|Human / Description            	|Slots	|Eye Rotation* |
| ----  | ----- | ------------- | ------------------------------------- | ----- | ------------ |
|1	|1	|Base		|Shoulder rotate                	| 200	|Counter	
|2	|3?**	|Pivot		|Shoulder lift	                	| 180	|Counter	
|3	|2?	|End		|Elbow. Pwr set by R65/66            	| 157	|Counter	
|4	|4	|Angle		|Wrist. Differential combination: 	| 115	|Counter	
|5	|5	|Rotate		|DiffA1 is rotate, DIffA2 is lift	| 100	|Clockwise	
|6	|6	|External	|(not used)		        	|	|
|6	| n/a	|J20 bottom pin	|Rotate [Dynamixel](End-Effector-Servos)| n/a	| n/a
|7	| n/a	|J20 bottom pin	|Gripper [Dynamixel](End-Effector-Servos)| n/a	| n/a

\* _The direction of the rotation of the eye for Joint 5 is the opposite of the others._<BR>
** _Development of the [FPGA](Gateware) code started in the middle and so the numbering of the joints is not consistent. Specifically, when reading (or writing) the AdcCenters.txt file from the share folder, the 2nd and 4th, and 3rd and 5th, lines must be swapped_

[Status data ](status-data) for each of the joints is returned via the 'g' [command oplet](Command-oplet-instruction) and automatically after most commands. 