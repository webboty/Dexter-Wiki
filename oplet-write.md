The Write (`w`) [oplet](Command-oplet-instruction) writes low level values to the [FPGA / Gateware](Gateware) and in some cases also sets variables in the [firmware](Firmware). This is the lower case 'w'; the upper case 'W' is [Write to Robot](write_to_robot). The second parameter (the value) is written to the address of the FPGA specified by the first parameter.  e.g. `w 42 64` writes the value 64 into the address 42 in the FPGA. Some addresses are also "tracked" by the C firmware (DexRun) for example, `w 5 6516872` writes a new value to FPGA register #5, ACCELERATION_MAXSPEED but the maxSpeed and coupledAcceleration variables in DexRun will also be updated. For higher level settings in the [Firmware](Firmware), see the ['S' oplet](set-parameter-oplet)

_Note: This represents the "virtual" address list as used by the 'w' command. The actual FPGA addresses have been changed since [2018.05.29](https://github.com/HaddingtonDynamics/Dexter/commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) to use a "keyhole window" to reduce the number of mapped addresses used by the FPGA. In the keyhole system, one address in a block will select what the next address actually changes. DexRun.c still interprets 'w' commands with the original addresses when the function has not been converted to a keyhole system via the array "<a href="https://github.com/HaddingtonDynamics/Dexter/search?q=OldMemMapInderection&unscoped_q=OldMemMapInderection">OldMemMapInderection</a>". Items which have been struck out below are not available via 'w' and must be set via other commands which are linked here._

Addr | Name               | Description
---- | ------------------ | ----------------------------
 0 | BASE_POSITION_AT     | <strike>J1 Motor position index</strike> 
 1 | END_POSITION_AT      | <strike>J2 ...where the joint has been</strike>
 2 | PIVOT_POSITION_AT    | <strike>J3 ...commanded to go to</strike>
 3 | ANGLE_POSITION_AT    | <strike>J4</strike> Use the "a" [oplet](Command-oplet-instruction#a)
 4 | ROT_POSITION_AT      | <strike>J5</strike>
 5 | ACCELERATION_MAXSPEED| Acceleration [20:31] and max speed [0:19] (shared register). Use the [oplet S AngularAcceleration](set-parameter-oplet#AngularAcceleration) and Use [oplet S AngularSpeed](set-parameter-oplet#AngularSpeed)
 6 | BASE_SIN_CENTER      | <strike>J1 DC bias parameters</strike> 
 7 | BASE_COS_CENTER      | <strike>J1</strike> Use the "W" or [write-to-robot](write-to-robot) [oplet](Command-oplet-instruction) 
 8 | END_SIN_CENTER       | <strike>J2</strike> to write the [AdcCenters.txt](Encoders#adc-centers) file.
 9 | END_COS_CENTER       | <strike>J2</strike>
10 | PIVOT_SIN_CENTER    | <strike>J3</strike>
11 | PIVOT_COS_CENTER    | <strike>J3</strike>
12 | ANGLE_SIN_CENTER    | <strike>J4</strike>
13 | ANGLE_COS_CENTER    | <strike>J4</strike>
14 | ROT_SIN_CENTER      | <strike>J5</strike>
15 | ROT_COS_CENTER      | <strike>J5</strike>
16 | PID_DELTATNOT       | <strike>delta t not. Inverse of Delta t. 100,000KHz</strike>
17 | PID_DELTAT          | <strike>delta t. float * 0.0001 seconds</strike>
18 | PID_D               | <strike>PID Differential Term</strike>
19 | PID_I               | <strike>PID Integral Term</strike>
20 | PID_P               | PID Proportional Term <A HREF="https://github.com/HaddingtonDynamics/Dexter/issues/33#issuecomment-427921288">in Floating point</A>
21 | PID_ADDRESS         | Sets the joint number for the above PID_ settings to be written into. Multiple sets are available for PID_SCHEDULE_INDEX
22 | BOUNDRY_BASE        | <strike>Maximum position past which the force position override will be reduced for each joint.</strike> 
23 | BOUNDRY_END         | <strike>Injects a negative force opposing the external force to limit the amount the joint will move to avoid external forces.</strike>
24 | BOUNDRY_PIVOT       | <strike>The low boundary is [0:15] and the high boundary is [16:31]</strike>
25 | BOUNDRY_ANGLE       | <strike>Changed along with TDInt branch via a keyhole to make low and high boundary +-18 bits (19 bits total) each.</strike> 
26 | BOUNDRY_ROT         | <strike>"</strike> Use [B oplet](Command-oplet-instruction#B) to set all joint boundaries or [SetParameter oplet: S J#Boundry\[High\|Low\]](set-parameter-oplet#JointBoundaries)
27 | SPEED_FACTORA       | Multiplier (coefficient / master gain) for the force calculator. Allows quick adjustments.
28 | SPEED_FACTORB       | B not used. 
29 | FRICTION_BASE       | <strike>Coefficient to scale the position error. Can be negative. </strike> 
30 | FRICTION_END        | <strike>Feels like friction. Used in most modes, very important in follow mode.</strike>
31 | FRICTION_PIVOT      | <strike>"</strike> Use [SetParameter oplet: S J#Friction](set-parameter-oplet#JointFrictions) 
32 | FRICTION_ANGLE      | <strike>"</strike>
33 | FRICTION_ROT        | <strike>"</strike>
34 | MOVE_TRHESHOLD      | Maximum force before joint 1,2,3 position starts changing. See follow mode.
35 | F_FACTOR            | Like SPEED_FACTORA, but for PID controller. Affects all joints at once. 
36 | MAX_ERROR           | Speed limit for PID controller. Clips the velocity of the step signals going to the motor. 
37 | FORCE_BIAS_BASE     | <strike>Adds to the force (position error) on each joint. Useful for removing the force of gravity.</strike> 
38 | FORCE_BIAS_END      | <strike>Also used to provide haptic force feedback in teleoperation or VR systems.</strike>
39 | FORCE_BIAS_PIVOT    | <strike>"</strike> Use [SetParameter oplet: S J#Force](set-parameter-oplet#JointForces) 
40 | FORCE_BIAS_ANGLE    | <strike>"</strike>
41 | FORCE_BIAS_ROT      | <strike>"</strike>
42 | COMMAND_REG         | control state register. Bits:<BR>0 CMD_CAPCAL_BASE 1 <BR>1 CMD_CAPCAL_END 2 <BR>2 CMD_CAPCAL_PIVOT 4 <BR>3 CMD_MOVEEN 8 <BR>4 CMD_MOVEGO 16 <BR>5 CMD_ENABLE_LOOP 32 <BR>6 CMD_CLEAR_LOOP 64 <BR>7 CMD_CALIBRATE_RUN 128 <BR>8 CMD_RESET_POSITION 256 <BR>9 CMD_RESET_FORCE 512 <BR>10 CMD_CAPCAL_ANGLE 1024 <BR>11 CMD_CAPCAL_ROT 2048 <BR>12 CMD_ANGLE_ENABLE 4096 <BR>13 CMD_ROT_ENABLE 8196 <BR>see <a href="http://JamesNewton.github.io/Dexter.html">cmd calc</a>
43 | DMA_CONTROL         | Used to read and write tables and other large data objects. Bits:<BR>0 DMA_WRITE_ENQUEUE 1<BR>1 DMA_WRITE_INITIATE 2<BR>2 DMA_READ_DEQUEUE 4<BR>3 DMA_READ_BLOCK 8<BR>4 DMA_RESET_ALL 16
44 |  DMA_WRITE_DATA     |
45 |  DMA_WRITE_PARAMS     |
46 |  DMA_WRITE_ADDRESS            |
47 |  DMA_READ_PARAMS              |
48 |  DMA_READ_ADDRESS             |
49 |  REC_PLAY_CMD                 | Bits:<BR>0 CMD_RESET_RECORD 1<BR>1 CMD_RECORD 2<BR>2 CMD_RESET_PLAY 4<BR>3 CMD_PLAYBACK 8<BR>4 CMD_RESET_PLAY_POSITION
50 |  REC_PLAY_TIMEBASE            |
51 |  <strike>DIFF_FORCE_TIMEBASE</strike> (Now MAXSPEED_XYZ)          | <strike>Timebase for PID and Force calculators. Depreciated</strike>
52 |  DIFF_FORCE_BETA              | Scaling factor for boundaries for the differential 
53 |  DIFF_FORCE_MOVE_THRESHOLD    | Maximum force before joint 4, 5 position starts changing. See follow mode.
54 |  DIFF_FORCE_MAX_SPEED         | Maximum speed the force calculator can generate. Like MAX_ERROR (but for force, not PID)
55 |  DIFF_FORCE_SPEED_FACTOR_ANGLE| Multiplier (coefficient / master gain) for the force calculator on J4. Like SPEED_FACTORA
56 |  DIFF_FORCE_SPEED_FACTOR_ROT  | Multiplier (coefficient / master gain) for the force calculator on J5. Like SPEED_FACTORA
57 |  <strike>DIFF_FORCE_ANGLE_COMPENSATE</strike> (Now EXTRUDER_CONTROL)  | <strike>Unused. </strike>
58 |  FINE_ADJUST_BASE             | <strike>Position offset to commanded joint angle into the PID. </strike>
59 |  FINE_ADJUST_END              | <strike>Has the same effect as offsetting the error signal. </strike>
60 |  FINE_ADJUST_PIVOT            | <strike>Allows moves to be made by the PID only vs the coupled acceleration calculator + PID</strike>
61 |  FINE_ADJUST_ANGLE            | <strike>"</strike> See PID_MOVE. Use the "P" [oplet](Command-oplet-instruction#P)
62 |  FINE_ADJUST_ROT              | <strike>"</strike>
63 |  RECORD_LENGTH                | <strike>Length of the recording to be played back</strike>
64 |  END_EFFECTOR_IO              | <strike>Controls the configuration of the bits that go to the end effector.  See [End-Effectors](End-Effectors)</strike>
65 |  SERVO_SETPOINT_A             | <strike>PWM value in PWM mode on end effector pins.</strike>
66 |  SERVO_SETPOINT_B             | <strike>"</strike> See <a href="https://github.com/HaddingtonDynamics/Dexter/issues/73">Issue 73</a>
67 |  BASE_FORCE_DECAY             | How hard the joint tries to return to the commanded position when the position.
68 |  END_FORCE_DECAY              | has been changed to avoid exceeding the maximum force. 
69 |  PIVOT_FORCE_DECAY            | Subtracts out the offset introduced by the force controller.
70 |  ANGLE_FORCE_DECAY            | See Protect mode
71 |  ROTATE_FORCE_DECAY           | "
72 |  PID_SCHEDULE_INDEX           | <strike>Gain scheduling. Changes the offset to the PID parameters. Multiplies PID_ADDRESS. To quickly change settings.</strike>
73 |  GRIPPER_MOTOR_CONTROL        | <strike>PWM output from 7 pin SIL connector on the side (near bottom) of the motor board. Just the first bit is used to enable / disable.</strike> 
74 |  GRIPPER_MOTOR_OFF_WIDTH      | <strike>"</strike> Use [oplet S GripperMotor](set-parameter-oplet#GripperMotor)
75 |  GRIPPER_MOTOR_ON_WIDTH       | <strike>"</strike>
76 |  START_SPEED                  | <strike>Starting speed into this move.</strike> Use [oplet S AngularSpeedStartAndEnd](set-parameter-oplet#AngularSpeedStartAndEnd)
77 |  ANGLE_END_RATIO              | <strike>Difference in the transmission ratios for the differential joint.</strike>
78 | RESET_PID_AND_FLUSH_QUEUE     | bit 0 resets PID_DELTA. Bit 1 is E_STOP? Set to all zero for normal operation.
79 | XYZ_FORCE_TIMEBASE            | Timebase Divisor
80 | DIFFERENTIAL_FORCE_TIMEBASE   | Timebase Divisor
81 | PID_TIMEBASE                  | Timebase Divisor

