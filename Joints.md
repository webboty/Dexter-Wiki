Dexter's [hardware](Hardware) has 5 joints in the base model, and the v2 [Tool Interface](End-Effectors) adds 2 more for a total of 7. Each [FPGA](Gateware) measured joint has an [encoder](Encoders).

|#	|FPGA	|Connector	|Human / Description            	|Slots	|Eye Rotation* |
| ----  | ----- | ------------- | ------------------------------------- | ----- | ------------ |
|1	|1	|Base		|Roll. Shoulder rotate (vertical)      	| 200	|Counter	
|2	|3**	|Pivot		|Pitch. Shoulder lift	               	| 180	|Counter	
|3	|2	|End		|Pitch. Elbow. Pwr set R65/66       	| 157	|Counter	
|4	|4	|Angle		|Pitch.  DiffA1 Differential with 5 	| 115	|Counter	
|5	|5	|Rotate		|Yaw.  DIffA2 4 and 5 make the wrist	| 100	|Clockwise	
|6	|6	|External	|(not used, could be extrude)		|	|
|6	| n/a	|J20 bottom pin	|Rotate [Dynamixel](End-Effector-Servos)| n/a	| n/a
|7	| n/a	|J20 bottom pin	|Gripper [Dynamixel](End-Effector-Servos)| n/a	| n/a

\* _The direction of the rotation of the eye for Joint 5 is the opposite of the others._<BR>
** _Development of the [FPGA](Gateware) code started in the middle and so the numbering of the joints is not consistent. Specifically, when reading (or writing) the AdcCenters.txt file from the share folder, the 2nd and 4th, and 3rd and 5th, lines must be swapped_

Because joints may (do) have different gearing and drive systems, the conversion between one step of the motor and 1 degree of motion for the joint requires a factor. These also account for microstepping and the conversion from degrees to arc seconds. There is also an compensation factor required for joints 4 and 5 when joint 3 is moved (<tt>ANGLE_END_RATIO</tt>). All of this information is stored in the **AxisCal.txt** file on the share. Anytime an angle value is sent or recieved by the Firmware, it is multiplied by the conversion factor for that joint (<tt>JointsCal</tt> array). The following Javascript (for use in DDE) calculates the correct values given the gear ratios, stepper motor steps per revolution, and stepper driver microstepping setting.
````
var diff_pulley_small_num_teeth = 16
var diff_pulley_large_num_teeth = 90
var micro_step = 16
var motor_steps = 400
var gear_ratios = [
    52 / 1,
    52 / 1,
    52 / 1,
    diff_pulley_large_num_teeth / diff_pulley_small_num_teeth,
    diff_pulley_large_num_teeth / diff_pulley_small_num_teeth
]

//Output:
var AxisCal_string = ""
for(let i = 0; i < 5; i++){
	AxisCal_string += -(gear_ratios[i]*micro_step*motor_steps) / (3600*360) + "\n"
}
AxisCal_string += -Math.round(gear_ratios[3] / gear_ratios[2] * Math.pow(2, 24))
// gear_ratios is a zero index array so [3] is joint 4 and [2] is joint 3.
````
The axis 5 line in the file, second line from the end, may need it's sign swapped. If you do a move_to([x,y,z], [-1,0,-1]) the end effector should be pointing in the negative x direction. That is, if the [x,y,z] values are all positive, and you are standing behind the robot (robots point of view), then the end effector should be pointing to your left with a direction of [-1,0,-1]

For the standard Dexter v1 or Dexter HD, the 

[Status data ](status-data) for each of the joints is returned via the 'g' [command oplet](Command-oplet-instruction) and automatically after most commands. 