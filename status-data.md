# Dexter status data

The status of the Dexter robot arm is returned to [DDE](DDE) by the [Firmware](Firmware) after every command. All the _DELTA and _AT values are scaled by the calibration values in the AxisCal.txt file. [DDE](DDE) calculates additional status data and adds it to the status array<sup>[1](https://github.com/cfry/dde/blob/e53ec91c4c6e0d3755a4e370af5d62ac8dc84e22/doc/ref_man.html#L2730)</sup> available to jobs. This includes slope values for each joint.

In April 2018, the FPGA code was changed so that:
<BR> SLOPE_BASE_POSITION is the position of Axes 6,
<BR> SLOPE_PIVOT_POSITION is force of Axis 6,
<BR> SLOPE_END_POSITION is the position of Axis 7,
<BR> SLOPE_ANGLE_POSITION is force of Axis 7.

For each of the following:
<BR>\*\_AT - Current position goal. This is the position the motion controller has told the stepper to go to in this instant on it's way to the commanded position goal. 
<BR>\*\_DELTA - Instantaneous error in position. Difference between \_AT and actual position. 
<BR>\*\_PID_DELTA - Accumulated corrective error signal. Force due to inertia, gravity, friction, etc...  
<BR>\*\_FORCE_DELTA - In follow mode, this is the offset in position necessary to keep force at zero. 
<BR>\*\_SIN - The current A2D value from the sin sensor on that joints encoder. 
<BR>\*\_COS - The current A2D value from the cos sensor on that joints encoder. 
<BR>PLAYBACK\_ - Not used? The step and direction ticks sent to the motor in the time between status updates.
<BR>SENT\_  - Little used? Commanded position goal.
<BR>SLOPE\_ - Unused. Was rate of change of \_AT. Measurement of joint speed. 

The actually returned data includes:
* DMA_READ_DATA
* DMA_READ_DATA
* RECORD_BLOCK_SIZE
* END_EFFECTOR_IO_IN

***
BASE Joint 1

* BASE_POSITION_AT
* BASE_POSITION_DELTA 
* BASE_POSITION_PID_DELTA
* BASE_POSITION_FORCE_DELTA
* BASE_SIN
* BASE_COS
* PLAYBACK_BASE_POSITION 
* SENT_BASE_POSITION 
* SLOPE_BASE_POSITION

***
PIVOT Joint 2

* PIVOT_POSITION_AT
* PIVOT_POSITION_DELTA
* PIVOT_POSITION_PID_DELTA
* PIVOT_POSITION_FORCE_DELTA
* PIVOT_SIN
* PIVOT_COS
* PLAYBACK_PIVOT_POSITION
* SENT_PIVOT_POSITION
* SLOPE_PIVOT_POSITION

***
END Joint 3

* END_POSITION_AT
* END_POSITION_DELTA
* END_POSITION_PID_DELTA
* END_POSITION_FORCE_DELTA
* END_SIN
* END_COS
* PLAYBACK_END_POSITION
* SENT_END_POSITION
* SLOPE_END_POSITION

***
ANGLE Joint 4

* ANGLE_POSITION_AT
* ANGLE_POSITION_DELTA
* ANGLE_POSITION_PID_DELTA
* ANGLE_POSITION_FORCE_DELTA
* ANGLE_SIN
* ANGLE_COS
* PLAYBACK_ANGLE_POSITION
* SENT_ANGLE_POSITION
* SLOPE_ANGLE_POSITION

***
ROT Joint 5

* ROT_POSITION_AT
* ROT_POSITION_DELTA
* ROT_POSITION_PID_DELTA
* ROT_POSITION_FORCE_DELTA
* ROT_SIN
* ROT_COS
* PLAYBACK_ROT_POSITION
* SENT_ROT_POSITION
* SLOPE_ROT_POSITION

