High level Parameters in the Dexter [Firmware](Firmware) are set with the "S" [Oplet](Command-oplet-instruction). For direct control of low level [FPGA](Gateware) parameters, see the ["w" Oplet](oplet-write)

|# | Name
| --- | ----
|0 | "**MaxSpeed**" Maximum velocity of next move in "nbits"<sup><a href="#1">1</a></sup>. [Default](https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF) is 250000. See also StartSpeed below.
|1 | "**Acceleration**" Maximum acceleration of next move in  "nbits"<sup><a href="#1">1</a></sup>. [Default](https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF) is 3.
|2 | "**J1Force**"  How hard each joint tries to return to commanded position in force protect modes
|3 | "**J3Force**"
|4 | "**J2Force**"
|5 | "**J4Force**"
|6 | "**J5Force**"
|7 | "**J1Friction**"  <sup><a href="#2">2</a></sup>
|8 | "**J3Friction**"
|9 | "**J2Friction**"
|10 | "**J4Friction**"
|11 | "**J5Friction**"
|12 | "**J1BoundryHigh**"
|13 | "**J1BoundryLow**"
|14 | "**J3BoundryHigh**"
|15 | "**J3BoundryLow**"
|16 | "**J2BoundryHigh**"
|17 | "**J2BoundryLow**"
|18 | "**J4BoundryHigh**"
|19 | "**J4BoundryLow**"
|20 | "**J5BoundryHigh**"
|21 | "**J5BoundryLow**"
|22 | "**GripperMotor**" Set mode to use standard servo (not Dynamixel) as the 6th / 7th axis for Tool interface.
|23 | "**EERoll**" 6th axis position / Tool Interface Roll (was standard servo, [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) changed to [Dynamixel](End-Effector-Servos))
|24 | "**EESpan**" 7th axis position / End Effector actuation (was standard servo, [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) changed to [Dynamixel](End-Effector-Servos))
|25 | "**StartSpeed**" Velocity at start (and currently, the end as well) of the next move in "nbits"<sup><a href="#1">1</a></sup>
|26 | "**EndSpeed**" (not implemented) Currently StartSpeed is the speed for both the start and end of each move. 
|27 | "**ServoSet2X**" Address, Register, Value (as of [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) was "End")  see: [Servo](End-Effector-Servos)
|28 | "**ServoSet**" Address, Register, String [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) (may not be working in FPGA?) [Needed](../issues/32) for [Screen](End-Effector-Screen)
|29 | "**RebootServo**" [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93)
|30 | "**RunFile**" FilePathName [Oct 12, 2018](../commit/e3cccb88cf9b1671c5b2f13c77d1ee00a967a528#diff-691272021fae98368efb598f8e089c16) If it exists, the file will be opened and all instructions from it parsed and executed. By convention, files end with `.make_ins` extension. Each line should be one instruction just like any sent over the socket connection (the job, instruction, start, and end time values are NOT included; start with the oplet). A sample line might be <BR>`S J1_PID_P 0.2 ; set base drive` <BR>Anything after a ';' is ignored. <BR>Was: "Ctrl" parms/values [Aug 24, 2018](../commit/f0d9fa772ba6c3eee979e62a071bca487a084c21) Compact setting of values related to operational mode. (**Depreciated, replaced with RunFile**)
|31 | "**J1_PID_P**" Float [Oct 12, 2018](../commit/e3cccb88cf9b1671c5b2f13c77d1ee00a967a528#diff-691272021fae98368efb598f8e089c16) Set Joint PID P with a floating point number. Helps with [Issue #33](https://github.com/HaddingtonDynamics/Dexter/issues/33#issuecomment-427921288)
|32 | "**J2_PID_P**" " 
|33 | "**J3_PID_P**" " 
|34 | "**J4_PID_P**" " 
|35 | "**J5_PID_P**" " 
|36 | "**EyeNumbers**" Sets the index for which Eye Dexter thinks it is at. Where it is within an eye is calculated with encoder measurements and effectively cannot be set. Takes 5 signed integer arguments where 0 is the circle that contains 0 degrees. See [read_from_robot, #EyeNumbers](https://github.com/HaddingtonDynamics/Dexter/wiki/read-from-robot#keywords).
|37 | "**CommandedAngles**" Sets where Dexter thinks it is without actually moving the robot. Takes 5 angles in integer arcseconds. These are the same values that are returned in the robot status under [*_AT]( https://github.com/HaddingtonDynamics/Dexter/wiki/status-data#joint-data-meanings).
|38 | "End" (not implemented)


<a name="1">Note 1</a> nbits are a value added to a register every clock cycle for a 128Khz clock such that once it overflows, an action will be taken. Converted automatically by DDE [in the socket communications code](https://github.com/cfry/dde/blob/33f06f03167a8c7b443a42ad0b3560df39ed7a17/core/socket.js#L98) for the "MaxSpeed", "StartSpeed", and "Acceleration" parameters. The global constant "_nbits_cf" is 7754.73550222 and the values given are divided by that constant. This converts degrees / second (or degrees / second / second in the case of Acceleration) into nbits. 

<a name="2">Note 2</a> Frictions can be fractional, but are limited to a range of +-(256 + 1/256). E.g. 1.1 becomes 1 plus a fractional value of 25/256

