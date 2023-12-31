# Dexter status data

The status of the Dexter robot arm is returned to [DDE](DDE) by the [Firmware](Firmware) after every command. All the _DELTA and _AT values are scaled by the calibration values in the AxisCal.txt file. [DDE](DDE) calculates additional status data and adds it to the status array<sup>[1](https://github.com/cfry/dde/blob/e53ec91c4c6e0d3755a4e370af5d62ac8dc84e22/doc/ref_man.html#L2730)</sup> available to jobs. This includes slope values for each joint.

Each data item is a signed 32 bit binary integer.

Units for angles are always integer arc seconds.

## Joint Data Meanings
For each of the following "[Joint](Joints)..." sections (e.g. replace * in the following with J1...J5) in the status data:

Name | Description
--- | ---
\*\_AT | Current incremental position goal toward the commanded final position goal. 
\*\_DELTA | Instantaneous error in position. Difference between \_AT and actual position. Always gets corrected out to zero given drive strength to do so.
\*\_PID_DELTA | . Related to force due to inertia, gravity, etc.
\*\_FORCE_DELTA | In follow mode, this is the angular offset from commanded position necessary to keep force at zero. 
\*\_SIN | The current A2D value from the sin sensor on that joints encoder. 
\*\_COS | The current A2D value from the cos sensor on that joints encoder. 
PLAYBACK\_ | Not used? The step and direction ticks sent to the motor in the time between status updates. August 2018 changed to the current measured angle of the joint.
SENT\_  | Little used? Commanded position goal. 
SLOPE\_ | Unused. Was rate of change of \_AT. Measurement of joint speed. April 2018 changed to servo information on some joints (see below)

## Changes
In April 2018, the FPGA and [Firmware](https://github.com/HaddingtonDynamics/Dexter/commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) (see line 2455) code was changed so that the [Dynamixel servo](End-Effector-Servos) data is returned by the getNormalizedInput function for the SLOPE items as follows:
<BR>Joint 6 position replaces SLOPE_BASE_POSITION,
<BR>Joint 6 force replaces SLOPE_PIVOT_POSITION,
<BR>Joint 7 position replaces SLOPE_END_POSITION,
<BR>Joint 7 force replaces SLOPE_ANGLE_POSITION.

Also, the SLOPE_ROT_POSITION was replaced with the error status of the two servos with J6 in the LSB and J7 in the next byte up (*256).

In August 2018, the FPGA and [Firmware](https://github.com/HaddingtonDynamics/Dexter/commit/1ca9251b47468d9841713ec89b62e91050125188) was changed to return the actual measured angle of each joint encoder as follows:
<BR> BASE_MEASURED_ANGLE replaces PLAYBACK_BASE_POSITION 
<BR> PIVOT_MEASURED_ANGLE replaces PLAYBACK_PIVOT_POSITION 
<BR> END_MEASURED_ANGLE replaces PLAYBACK_END_POSITION 
<BR> ANGLE_MEASURED_ANGLE replaces PLAYBACK_ANGLE_POSITION 
<BR> ROT_MEASURED_ANGLE replaces PLAYBACK_ROT_POSITION 

## Returned data
Address | Description | Sample
--- | --- | ---
 00 | Job number | 1
 01 | Instruction number | 0
 02 | Start time <sup><a href="#times">1</a></sup> | 1531787828
 03 | End time <sup><a href="#times">1</a></sup> | 349602
 04 | Oplet (e.g. 'g') | 103
 05 | [ERROR](status-errors) | 0
 06 | DMA_READ_DATA | 0
 07 | DMA_READ_DATA | 0
 08 | RECORD_BLOCK_SIZE | 0
 09 | END_EFFECTOR_IO_IN (Type of end effector installed?) | 0
 &nbsp; | **Joint 1: BASE**
 10 | BASE_POSITION_AT | 0
 11 | BASE_POSITION_DELTA | 0
 12 | BASE_POSITION_PID_DELTA | 0
 13 | BASE_POSITION_FORCE_DELTA | 0
 14 | BASE_SIN | 3703
 15 | BASE_COS | 2967
 16 | PLAYBACK_BASE_POSITION (Now BASE_MEASURED_ANGLE) | 0
 17 | SENT_BASE_POSITION | 0
 18 | SLOPE_BASE_POSITION (Now Joint 6 measured angle) | 0
 19 | empty | 2147483647
 &nbsp; | **Joint 2: PIVOT**
 20 | PIVOT_POSITION_AT | 0
 21 | PIVOT_POSITION_DELTA | 0
 22 | PIVOT_POSITION_PID_DELTA | 0
 23 | PIVOT_POSITION_FORCE_DELTA | 0
 24 | PIVOT_SIN | 293
 25 | PIVOT_COS | 56
 26 | PLAYBACK_PIVOT_POSITION (Now PIVOT_MEASURED_ANGLE) | 0
 27 | SENT_PIVOT_POSITION | 0
 28 | SLOPE_PIVOT_POSITION (Now Joint 6 torque) | 0
 29 | empty | 2147483647
 &nbsp; | **Joint 3: END**
 30 | END_POSITION_AT | 0
 31 | END_POSITION_DELTA | 0
 32 | END_POSITION_PID_DELTA | 0
 33 | END_POSITION_FORCE_DELTA | 0
 34 | END_SIN | 809
 35 | END_COS | 3063
 36 | PLAYBACK_END_POSITION (Now END_MEASURED_ANGLE) | 0
 37 | SENT_END_POSITION | 0
 38 | SLOPE_END_POSITION (Now Joint 7 measured angle)| 0
 39 | empty | 2147483647
 &nbsp; | **Joint 4: ANGLE**
 40 | ANGLE_POSITION_AT | 0
 41 | ANGLE_POSITION_DELTA | 0
 42 | ANGLE_POSITION_PID_DELTA | 0
 43 | ANGLE_POSITION_FORCE_DELTA | 0
 44 | ANGLE_SIN | 1682
 45 | ANGLE_COS | 3675
 46 | PLAYBACK_ANGLE_POSITION (Now ANGLE_MEASURED_ANGLE) | 0
 47 | SENT_ANGLE_POSITION | 0
 48 | SLOPE_ANGLE_POSITION (Now Joint 7 torque)| 0
 49 | empty | 2147483647
 &nbsp; | **Joint 5: ROT**
 50 | ROT_POSITION_AT | 0
 51 | ROT_POSITION_DELTA | 0
 52 | ROT_POSITION_PID_DELTA | 0
 53 | ROT_POSITION_FORCE_DELTA | 0
 54 | ROT_SIN | 1990
 55 | ROT_COS | 218
 56 | PLAYBACK_ROT_POSITION (Now ROT_MEASURED_ANGLE) | 0
 57 | SENT_ROT_POSITION | 0
 58 | SLOPE_ROT_POSITION (Now J6/7 servo error codes)| 0
 59 | empty | 2147483647

## Notes:

### Times
Times are in seconds since 1970. e.g. 1531787828 is GMT: Tuesday, July 17, 2018 12:37:08 AM as per [epochconverter.com](https://www.epochconverter.com/)

Note that Dexter may not have the correct current time. e.g. 349602 is GMT: Monday, January 5, 1970 1:06:42 AM or, in other words, a Dexter which has been running for a day, six minutes and 42 seconds. 
