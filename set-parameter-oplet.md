High level Parameters in the Dexter [Firmware](Firmware) are set with the "S" [Oplet](Command-oplet-instruction). For direct control of low level [FPGA](Gateware) parameters, see the ["w" Oplet](oplet-write)

|# | Name | Default / Range | DDE / Socket Units | Socket Type | Description 
| --- | --- | ------------- | ------------------ | ----------- | --------
|0 | "**MaxSpeed**" | 30 / 0.001-45 | deg/s / nbits | integer | Maximum velocity of next move in "nbits"<sup><a href="#1">1</a></sup>. [Default](https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF) is 250000. See also StartSpeed below.
|1 | "**Acceleration**" | 0.0001 / 0.0001 - 0.1 | deg/s<sup>2</sup> / nbits | integer | Maximum acceleration of next move in  "nbits"<sup><a href="#1">1</a></sup>. [Default](https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF) is 3.
|2 | "**J1Force**" |  |  | | How hard each joint tries to return to commanded position in force protect modes
|3 | "**J3Force**" |  |  | | 
|4 | "**J2Force**" |  |  | | 
|5 | "**J4Force**" |  |  | | 
|6 | "**J5Force**" |  |  | | 
|7 | "**J1Friction**" | 5 / 0-50 | unitless | fixed point? | <sup><a href="#2">2</a></sup> The friction felt during FollowMe mode
|8 | "**J3Friction**" | 5 / 0-50 | unitless | fixed point? | 
|9 | "**J2Friction**" | 5 / 0-50 | unitless | fixed point? | 
|10 | "**J4Friction**" | 15 / 0-150 | unitless | fixed point? | 
|11 | "**J5Friction**" | 15 / 0-150 | unitless | fixed point? | 
|12 | "**J1BoundryHigh**" | 180 / -180-180 | deg / arcsec| integer | Set all Boundaries at once with "B" [Oplet](Command-oplet-instruction)
|13 | "**J1BoundryLow**" | 180 / -180-180 | deg / arcsec| integer | 
|14 | "**J3BoundryHigh**" | 180 / -180-180 | deg / arcsec| integer | 
|15 | "**J3BoundryLow**" | 180 / -180-180 | deg / arcsec| integer | 
|16 | "**J2BoundryHigh**" | 180 / -180-180 | deg / arcsec| integer | 
|17 | "**J2BoundryLow**" | 180 / -180-180 | deg / arcsec| integer | 
|18 | "**J4BoundryHigh**" | 180 / -180-180 | deg / arcsec| integer | 
|19 | "**J4BoundryLow**" | 180 / -180-180 | deg / arcsec| integer | 
|20 | "**J5BoundryHigh**" | 180 / -180-180 | deg / arcsec| integer | 
|21 | "**J5BoundryLow**" | 180 / -180-180 | deg / arcsec| integer | 
|22 | "**GripperMotor**" | 500 / 0 - 12000 | PWM | | Set mode to use PWM output on J19 Start with 1, stop with 0.  TODO make ON and OFF widths parameters. See W command 73 - 74.
|23 | "**EERoll**" | 512 / 0~1023 | deg / 0.29*deg | | 6th axis position / Tool Interface Roll (was standard servo, [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) changed to [Dynamixel](End-Effector-Servos))
|24 | "**EESpan**" | 0 / 0~1023 | deg / 0.29*deg | | 7th axis position / End Effector actuation (was standard servo, [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) changed to [Dynamixel](End-Effector-Servos))
|25 | "**StartSpeed**" |  |  | | Velocity at start (and currently, the end as well) of the next move in "nbits"<sup><a href="#1">1</a></sup>
|26 | "**EndSpeed**" |  |  | | (not implemented) Currently StartSpeed is the speed for both the start and end of each move. 
|27 | "**ServoSet2X**" |  |  | | Address, Register, Value (as of [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) was "End")  see: [Servo](End-Effector-Servos)
|28 | "**ServoSet**" |  |  | | | Address, Register, String [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) (may not be working in FPGA?) [Needed](../issues/32) for [Screen](End-Effector-Screen)
|29 | "**RebootServo**" |  |  | | [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93)
|30 | "**RunFile**" |  |  | | FilePathName [Oct 12, 2018](../commit/e3cccb88cf9b1671c5b2f13c77d1ee00a967a528#diff-691272021fae98368efb598f8e089c16) If it exists, the file will be opened and all instructions from it parsed and executed. By convention, files end with `.make_ins` extension. Each line should be one instruction just like any sent over the socket connection (the job, instruction, start, and end time values are NOT included; start with the oplet). A sample line might be <BR>`S J1_PID_P 0.2 ; set base drive` <BR>Anything after a ';' is ignored. <BR>Was: "Ctrl" parms/values [Aug 24, 2018](../commit/f0d9fa772ba6c3eee979e62a071bca487a084c21) Compact setting of values related to operational mode. (**Depreciated, replaced with RunFile**)
|31 | "**J1_PID_P**" | 0.2 / 0 - 1 | unitless  | float | Float [Oct 12, 2018](../commit/e3cccb88cf9b1671c5b2f13c77d1ee00a967a528#diff-691272021fae98368efb598f8e089c16) Set Joint PID P with a floating point number. Helps with [Issue #33](https://github.com/HaddingtonDynamics/Dexter/issues/33#issuecomment-427921288)
|32 | "**J2_PID_P**" | 0.2 / 0 - 1 | unitless | float | 
|33 | "**J3_PID_P**" | 2 / 0 - 4 | unitless | float | 
|34 | "**J4_PID_P**" | 0.1 / 0 - 0.5 | unitless | float | 
|35 | "**J5_PID_P**" | 0.1 / 0 - 0.5 | unitless | float | 
|36 | <sup>"**AngularSpeed**"</sup> | 30 / 1 - 45 | deg/s / arcsec/s | integer | Speed to ramp up to at given acceleration. Same as MaxSpeed but with human readable units.
|37 | <sup>"**AngularSpeedStartAndEnd**"</sup> | 0.1 / 0 - 45 | deg/s / arcsec/s | integer | Speed to ramp up from and down to. Same as StartSpeed but with human readable units. 
|38 | <sup>"**AngularAcceleration**"</sup>  | 0.1 / 0.0003 - 0.1 | deg/s<sup>2</sup> | integer | 
|39 | <sup>"**CartesianSpeed**"</sup> | 300000 / 10000 - 500000 | micron/s | integer | 
|40 | <sup>"**CartesianSpeedStart**"</sup> | 0 / 0 - 500000 | micron/s | integer | 
|41 | <sup>"**CartesianSpeedEnd**"</sup> | 0 / 0 - 500000 | micron/s | integer | 
|42 | <sup>"**CartesianAcceleration**"</sup> | 1000000 / 0 - 500000 | micron/s<sup>2</sup> | integer | 
|43 | <sup>"**CartesianStepSize**"</sup> | 10 / 1 - 10000 | micron | integer | 
|44 | <sup>"**CartesianPivotSpeed**"</sup> | 108000 / 1 - 360000 | arcsec/s | integer | 
|45 | <sup>"**CartesianPivotSpeedStart**"</sup> | 0 / 1 - 360000 | arcsec/s | integer | 
|46 | <sup>"**CartesianPivotSpeedEnd**"</sup> | 0 / 1 - 360000 | arcsec/s | integer | 
|47 | <sup>"**CartesianPivotAcceleration**"</sup> | 10800000 / 1 - 10800000 | arcsec/s<sup>2</sup>                           | integer | 
|48 | <sup>"**CartesianPivotStepSize**"</sup> | ? / ? | arcsec | integer | 
|49 | "**EyeNumbers**" |  |  | array of 5 integers | Sets the index for which Eye Dexter thinks it is at. Where it is within an eye is calculated with encoder measurements and effectively cannot be set. Takes 5 signed integer arguments where 0 is the circle that contains 0 degrees. See [read_from_robot, #EyeNumbers](https://github.com/HaddingtonDynamics/Dexter/wiki/read-from-robot#keywords).
|50 | "**CommandedAngles**" |  | arcsec / arcsec | integer array of 5 | Sets where Dexter thinks it is without actually moving the robot. Takes 5 angles in integer arcseconds. These are the same values that are returned in the robot status under [*_AT]( https://github.com/HaddingtonDynamics/Dexter/wiki/status-data#joint-data-meanings).
|51 | "**LinkLengths**" | 165100, 340320, 321032, 050800, 140000 | microns | integer array of 5 | Sets the lengths of each link in the arm for the onboard kinematics. <br>Link 1: Base mount to Joint 2 pivot.<br>Link 2: Joint 2 pivot to Joint 3 pivot.<br>Link 3: Joint 3 pivot to Joint 4 pivot. <br>Link 4: Joint 4 pivot to axis of tool interface. <br>Link 5: Axis of Joint 4 to end effector (note: this depends on the end effector)


<a name="1">Note 1</a> nbits are a value added to a register every clock cycle for a 128Khz clock such that once it overflows, an action will be taken. Converted automatically by DDE [in the socket communications code](https://github.com/cfry/dde/blob/33f06f03167a8c7b443a42ad0b3560df39ed7a17/core/socket.js#L98) for the "MaxSpeed", "StartSpeed", and "Acceleration" parameters. The global constant "_nbits_cf" is 7754.73550222 and the values given are divided by that constant. This converts degrees / second (or degrees / second / second in the case of Acceleration) into nbits. 

<a name="2">Note 2</a> Frictions can be fractional, but are limited to a range of +-(256 + 1/256). E.g. 1.1 becomes 1 plus a fractional value of 25/256

