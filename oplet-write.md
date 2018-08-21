Writes values to the [FPGA / Gateware](Gateware). The second parameter is written to the address of the FPGA specified by the first parameter. e.g. in [DDE](DDE) `make_ins("w", 42, 64)` writes the value 64 into the address 42 in the FPGA. If the address is ACCELERATION_MAXSPEED ( 5 ) then the maxSpeed and coupledAcceleration variables in DexRun will be updated. 

_Note: This represents the "virtual" address list as used by the 'w' command. The actual FPGA addresses have been changed since (TODO: get date) to use a keyhole window. DexRun.c on the TDInt branch still interprets 'w' commands with these addresses, but it translates them into the keyhole system._

Addr | Function          | Description
---- | ----------------- | ----------------------------
 0 | BASE_POSITION        | Motor position index
 1 | END_POSITON          | ...where the joint has been
 2 | PIVOT_POSITON        | ...commanded to go to
 3 | ANGLE_POSITON        |
 4 | ROT_POSITON          |
 5 | ACCELERATION_MAXSPEED| Acceleration [20:31] and max speed [0:19] (shared register)
 6 | BASE_SIN_CENTER      | DC bias parameters
 7 | BASE_COS_CENTER      | .. ADCCenters
 8 | END_SIN_CENTER       |
 9 | END_COS_CENTER       |
10 | PIVOT_SIN_CENTER    |
11 | PIVOT_COS_CENTER    |
12 | ANGLE_SIN_CENTER    |
13 | ANGLE_COS_CENTER    |
14 | ROT_SIN_CENTER      |
15 | ROT_COS_CENTER      |
16 | PID_DELTATNOT       | delta t not. Inverse of Delta t. 100,000KHz
17 | PID_DELTAT          | delta t. float * 0.0001 seconds
18 | PID_D               | PID Differential Term
19 | PID_I               | PID Integral Term
20 | PID_P               | PID Proportional Term
21 | PID_ADDRESS         | Sets the joint number for the above PID_ settings to be written into. 
22 | BOUNDRY_BASE        | Maximum position past which the force position override will be reduced for each joint.
23 | BOUNDRY_END         | Injects a negative force opposing the external force to limit the amount the joint will move to avoid external forces.
24 | BOUNDRY_PIVOT       | The low boundary is [0:15] and the high boundary is [16:31]
25 | BOUNDRY_ANGLE       | Changed along with TDInt branch via a keyhole to make low and high boundary +-18 bits (19 bits total) each. Use ["S" command](set-parameter-oplet) to set boundaries, or the "B" command, instead of 'w' command.
26 | BOUNDRY_ROT         | "
27 | SPEED_FACTORA       | Multiplier (coefficient / master gain) for the force calculator. Allows quick adjustments.
28 | SPEED_FACTORB       | B not used. 
29 | FRICTION_BASE       | Coefficient to scale the position error. Can be negative. 
30 | FRICTION_END        | Feels like friction. Used in most modes, very important in follow mode.
31 | FRICTION_PIVOT      |
32 | FRICTION_ANGLE      |
33 | FRICTION_ROT        |
34 | MOVE_TRHESHOLD      | Maximum force before joint position starts changing. See follow mode.
35 | F_FACTOR            | 
36 | MAX_ERROR           |
37 | FORCE_BIAS_BASE     |
38 | FORCE_BIAS_END      |
39 | FORCE_BIAS_PIVOT    |
40 | FORCE_BIAS_ANGLE    |
41 | FORCE_BIAS_ROT      |
42 | COMMAND_REG         | control state register. Bits:<BR>0 CMD_CAPCAL_BASE 1 <BR>1 CMD_CAPCAL_END 2 <BR>2 CMD_CAPCAL_PIVOT 4 <BR>3 CMD_MOVEEN 8 <BR>4 CMD_MOVEGO 16 <BR>5 CMD_ENABLE_LOOP 32 <BR>6 CMD_CLEAR_LOOP 64 <BR>7 CMD_CALIBRATE_RUN 128 <BR>8 CMD_RESET_POSITION 256 <BR>9 CMD_RESET_FORCE 512 <BR>10 CMD_CAPCAL_ANGLE 1024 <BR>11 CMD_CAPCAL_ROT 2048 <BR>12 CMD_ANGLE_ENABLE 4096 <BR>13 CMD_ROT_ENABLE 8196 <BR>see <a href="http://JamesNewton.github.io/Dexter.html">cmd calc</a>
43 | DMA_CONTROL         | Bits:<BR>0 DMA_WRITE_ENQUEUE 1<BR>1 DMA_WRITE_INITIATE 2<BR>2 DMA_READ_DEQUEUE 4<BR>3 DMA_READ_BLOCK 8<BR>4 DMA_RESET_ALL 16
44 |  DMA_WRITE_DATA     |
45 |  DMA_WRITE_PARAMS     |
46 |  DMA_WRITE_ADDRESS     |
47 |  DMA_READ_PARAMS     |
48 |  DMA_READ_ADDRESS     |
49 |  REC_PLAY_CMD 49 |Bits:<BR>0 CMD_RESET_RECORD 1<BR>1 CMD_RECORD 2<BR>2 CMD_RESET_PLAY 4<BR>3 CMD_PLAYBACK 8<BR>4 CMD_RESET_PLAY_POSITION     |
50 |  REC_PLAY_TIMEBASE     |
51 |  DIFF_FORCE_TIMEBASE     |
52 |  DIFF_FORCE_BETA     |
53 |  DIFF_FORCE_MOVE_THRESHOLD     |
54 |  DIFF_FORCE_MAX_SPEED     |
55 |  DIFF_FORCE_SPEED_FACTOR_ANGLE     |
56 |  DIFF_FORCE_SPEED_FACTOR_ROT     |
57 |  DIFF_FORCE_ANGLE_COMPENSATE     |
58 |  FINE_ADJUST_BASE     |
59 |  FINE_ADJUST_END     |
60 |  FINE_ADJUST_PIVOT     |
61 |  FINE_ADJUST_ANGLE     |
62 |  FINE_ADJUST_ROT     |
63 |  RECORD_LENGTH     |
64 |  END_EFFECTOR_IO     |
65 |  SERVO_SETPOINT_A     |
66 |  SERVO_SETPOINT_B     |
67 |  BASE_FORCE_DECAY     |
68 |  END_FORCE_DECAY     |
69 |  PIVOT_FORCE_DECAY     |
70 |  ANGLE_FORCE_DECAY     |
71 |  ROTATE_FORCE_DECAY     |
72 |  PID_SCHEDULE_INDEX     |
73 |  GRIPPER_MOTOR_CONTROL     |
74 |  GRIPPER_MOTOR_OFF_WIDTH     |
75 |  GRIPPER_MOTOR_ON_WIDTH     |
76 |  START_SPEED     |
77 |  ANGLE_END_RATIO     |
78 | ???
79| PID?                 |Timebase Divisor
80| Physics J123?        |Timebase Divisor
81| Physics J45?         |Timebase Divisor

