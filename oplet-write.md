Writes values to the [FPGA / Gateware](Gateware). The second parameter is written to the address of the FPGA specified by the first parameter. e.g. in [DDE](DDE) `make_ins("w", 42, 64)` writes the value 64 into the address 42 in the FPGA. If the address is ACCELERATION_MAXSPEED ( 5 ) then the maxSpeed and coupledAcceleration variables in DexRun will be updated. 


Addr | Function
---- | ---------
0 | define BASE_POSITION |
1 | END_POSITON |
2 |  PIVOT_POSITON|
3 |  ANGLE_POSITON|
4 |  ROT_POSITON|
5 | ACCELERATION_MAXSPEED| 
6 | BASE_SIN_CENTER |
7 | BASE_COS_CENTER |
8 | END_SIN_CENTER |
9 | END_COS_CENTER |
10 | PIVOT_SIN_CENTER |
11 | PIVOT_COS_CENTER |
12 | ANGLE_SIN_CENTER |
13 | ANGLE_COS_CENTER |
14 | ROT_SIN_CENTER |
15 | ROT_COS_CENTER |
16 | PID_DELTATNOT |
17 | PID_DELTAT |
18 | PID_D |
19 | PID_I |
20 | PID_P |
21 | PID_ADDRESS |
22 | BOUNDRY_BASE | Force
23 | BOUNDRY_END | Force
24 | BOUNDRY_PIVOT | Force
25 | BOUNDRY_ANGLE | Force
26 | BOUNDRY_ROT | Force
27 | SPEED_FACTORA |
28 | SPEED_FACTORB |
29 | FRICTION_BASE |
30 | FRICTION_END |
31 | FRICTION_PIVOT |
32 | FRICTION_ANGLE |
33 | FRICTION_ROT |
34 | MOVE_TRHESHOLD |
35 | F_FACTOR |
36 | MAX_ERROR |
37 | FORCE_BIAS_BASE |
38 | FORCE_BIAS_END |
39 | FORCE_BIAS_PIVOT |
40 | FORCE_BIAS_ANGLE |
41 | FORCE_BIAS_ROT |
42 | COMMAND_REG |Bits:<BR>1:CapCalibrateBase<BR>2:CapCalibrateEnd<BR>3:CapCalibratePivot<BR>4: GoMove<BR>5: SelectMoveChange<BR>6: EnableLoop<BR>7: AClrLoop <BR>8: CAL_RUN <BR>9: ResetMotorPosition <BR>10: ResetForce <BR> 13-14: DiffAxisEnable<BR>see <a href="http://JamesNewton.github.io/Dexter.html">cmd calc</a>
79| PID? |Timebase Divisor
80| Physics J123? |Timebase Divisor
81| Physics J45? |Timebase Divisor




#define ACCELERATION_MAXSPEED 5

// DC bias paramaters


// control state register
#define COMMAND_REG 42
#define CMD_CAPCAL_BASE 1
#define CMD_CAPCAL_END 2
#define CMD_CAPCAL_PIVOT 4
#define CMD_MOVEEN 8
#define CMD_MOVEGO 16
#define CMD_ENABLE_LOOP 32
#define CMD_CLEAR_LOOP 64
#define CMD_CALIBRATE_RUN 128
#define CMD_RESET_POSITION 256
#define CMD_RESET_FORCE 512
#define CMD_CAPCAL_ANGLE 1024
#define CMD_CAPCAL_ROT 2048
#define CMD_ANGLE_ENABLE 4096
#define CMD_ROT_ENABLE 8196







//DMA 
#define DMA_READ_ADDRESS 48
#define DMA_READ_PARAMS 47
#define DMA_WRITE_ADDRESS 46
#define DMA_WRITE_PARAMS 45
#define DMA_WRITE_DATA 44
#define DMA_CONTROL 43

// DMA control bits breakdown

#define DMA_WRITE_ENQUEUE 1
#define DMA_WRITE_INITIATE 2
#define DMA_READ_DEQUEUE 4
#define DMA_READ_BLOCK 8
#define DMA_RESET_ALL 16

#define REC_PLAY_CMD 49
#define CMD_RESET_RECORD 1
#define CMD_RECORD 2
#define CMD_RESET_PLAY 4
#define CMD_PLAYBACK 8
#define CMD_RESET_PLAY_POSITION 16



#define REC_PLAY_TIMEBASE 50
#define DIFF_FORCE_TIMEBASE 51
#define DIFF_FORCE_BETA 52
#define DIFF_FORCE_MOVE_THRESHOLD 53
#define DIFF_FORCE_MAX_SPEED 54
#define DIFF_FORCE_SPEED_FACTOR_ANGLE 55
#define DIFF_FORCE_SPEED_FACTOR_ROT 56
#define DIFF_FORCE_ANGLE_COMPENSATE 57

#define FINE_ADJUST_BASE 58
#define FINE_ADJUST_END 59
#define FINE_ADJUST_PIVOT 60
#define FINE_ADJUST_ANGLE 61
#define FINE_ADJUST_ROT 62

#define RECORD_LENGTH 63



#define END_EFFECTOR_IO 64
#define SERVO_SETPOINT_A 65
#define SERVO_SETPOINT_B 66

#define BASE_FORCE_DECAY 67
#define END_FORCE_DECAY 68
#define PIVOT_FORCE_DECAY 69
#define ANGLE_FORCE_DECAY 70
#define ROTATE_FORCE_DECAY 71

#define PID_SCHEDULE_INDEX 72

#define GRIPPER_MOTOR_CONTROL 73
#define GRIPPER_MOTOR_ON_WIDTH 75
#define GRIPPER_MOTOR_OFF_WIDTH 74
#define START_SPEED 76
#define ANGLE_END_RATIO 77


