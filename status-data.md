# Dexter status data

The status of the Dexter robot arm is returned to [DDE](DDE) by the [Firmware](Firmware) after every command. All the _DELTA and _AT values are scaled by the calibration values in the AxisCal.txt file. [DDE](DDE) calculates additional status data and adds it to the status array<sup>[1](https://github.com/cfry/dde/blob/e53ec91c4c6e0d3755a4e370af5d62ac8dc84e22/doc/ref_man.html#L2730)</sup> available to jobs. This includes slope values for each joint.

Units are always integer arc seconds.
For each of the following "[Joint](Joints)..." sections:
<BR>\*\_AT - Current incremental position goal toward the commanded final position goal. 
<BR>\*\_DELTA - Instantaneous error in position. Difference between \_AT and actual position. 
<BR>\*\_PID_DELTA - Accumulated corrective error signal. Related to force due to inertia, gravity, etc.
<BR>\*\_FORCE_DELTA - In follow mode, this is the offset from commanded position necessary to keep force at zero. 
<BR>\*\_SIN - The current A2D value from the sin sensor on that joints encoder. 
<BR>\*\_COS - The current A2D value from the cos sensor on that joints encoder. 
<BR>PLAYBACK\_ - Not used? The step and direction ticks sent to the motor in the time between status updates. August 2018 changed to Measured angle of the joint 
<BR>SENT\_  - Little used? Commanded position goal. 
<BR>SLOPE\_ - Unused. Was rate of change of \_AT. Measurement of joint speed. April 2018 changed to servo information (see below)

In April 2018, the FPGA and [Firmware](https://github.com/HaddingtonDynamics/Dexter/commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) (see line 2455) code was changed so that the [Dynamixel servo](End-Effector-Servos) data is returned in the SLOPE items as follows:
<BR>Joint 6 position replaces SLOPE_BASE_POSITION,
<BR>Joint 6 force replaces SLOPE_PIVOT_POSITION,
<BR>Joint 7 position replaces SLOPE_END_POSITION,
<BR>Joint 7 force replaces SLOPE_ANGLE_POSITION.

In August 2018, the FPGA and [Firmware](https://github.com/HaddingtonDynamics/Dexter/commit/1ca9251b47468d9841713ec89b62e91050125188) was changed to return the actual measured angle of each joint encoder as follows:
<BR> BASE_MEASURED_ANGLE replaces PLAYBACK_BASE_POSITION 
<BR> PIVOT_MEASURED_ANGLE replaces PLAYBACK_PIVOT_POSITION 
<BR> END_MEASURED_ANGLE replaces PLAYBACK_END_POSITION 
<BR> ANGLE_MEASURED_ANGLE replaces PLAYBACK_ANGLE_POSITION 
<BR> ROT_MEASURED_ANGLE replaces PLAYBACK_ROT_POSITION 

## Returned data
Address | Description
--- | ---
 00 | Job number
 01 | Instruction number
 02 | Start time
 03 | End time
 04 | Oplet (e.g. 'g')
 05 | ?
 06 | DMA_READ_DATA
 07 | DMA_READ_DATA
 08 | RECORD_BLOCK_SIZE
 09 | END_EFFECTOR_IO_IN
 &nbsp; | **Joint 1: BASE**
 10 | BASE_POSITION_AT
 11 | BASE_POSITION_DELTA 
 12 | BASE_POSITION_PID_DELTA
 13 | BASE_POSITION_FORCE_DELTA
 14 | BASE_SIN
 15 | BASE_COS
 16 | PLAYBACK_BASE_POSITION (Now BASE_MEASURED_ANGLE)
 17 | SENT_BASE_POSITION 
 18 | SLOPE_BASE_POSITION (Now Joint 6 angle)
 &nbsp; | **Joint 2: PIVOT**
 19 | PIVOT_POSITION_AT
 20 | PIVOT_POSITION_DELTA
 21 | PIVOT_POSITION_PID_DELTA
 22 | PIVOT_POSITION_FORCE_DELTA
 23 | PIVOT_SIN
 24 | PIVOT_COS
 25 | PLAYBACK_PIVOT_POSITION (Now PIVOT_MEASURED_ANGLE)
 26 | SENT_PIVOT_POSITION
 27 | SLOPE_PIVOT_POSITION (Now Joint 6 force)
 &nbsp; | **Joint 3: END**
 28 | END_POSITION_AT
 29 | END_POSITION_DELTA
 30 | END_POSITION_PID_DELTA
 31 | END_POSITION_FORCE_DELTA
 32 | END_SIN
 33 | END_COS
 34 | PLAYBACK_END_POSITION (Now END_MEASURED_ANGLE)
 35 | SENT_END_POSITION
 36 | SLOPE_END_POSITION
 &nbsp; | **Joint 4: ANGLE**
 37 | ANGLE_POSITION_AT
 38 | ANGLE_POSITION_DELTA
 39 | ANGLE_POSITION_PID_DELTA
 40 | ANGLE_POSITION_FORCE_DELTA
 41 | ANGLE_SIN
 42 | ANGLE_COS
 43 | PLAYBACK_ANGLE_POSITION (Now ANGLE_MEASURED_ANGLE)
 44 | SENT_ANGLE_POSITION
 45 | SLOPE_ANGLE_POSITION
 &nbsp; | **Joint 5: ROT**
 46 | ROT_POSITION_AT
 47 | ROT_POSITION_DELTA
 48 | ROT_POSITION_PID_DELTA
 49 | ROT_POSITION_FORCE_DELTA
 50 | ROT_SIN
 51 | ROT_COS
 52 | PLAYBACK_ROT_POSITION (Now ROT_MEASURED_ANGLE)
 53 | SENT_ROT_POSITION
 54 | SLOPE_ROT_POSITION

