High level Parameters in the Dexter [Firmware](Firmware) are set with the "S" [Oplet](Command-oplet-instruction). For direct control of low level [FPGA](Gateware) parameters, see the ["w" Oplet](oplet-write)

|# | Name | Default / Range | DDE / Socket Units | Socket Type | Description 
| --- | --- | ------------- | ------------------ | ----------- | --------
|0 | **MaxSpeed** | 30 / 0.001-45 | deg/s / nbits | integer | Maximum velocity of next move in "nbits"<sup><a href="#1">1</a></sup>. [Default](https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF) is 250000. See also StartSpeed below.
|1 | **Acceleration** | 0.0001 / 0.0001 - 0.1 | deg/s<sup>2</sup> / nbits | integer | Maximum acceleration of next move in  "nbits"<sup><a href="#1">1</a></sup>. [Default](https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF) is 3.
|2 | **J1Force** |  |  | | How hard each joint tries to return to commanded position in force protect modes
|3 | **J3Force** |  |  | | 
|4 | **J2Force** |  |  | | 
|5 | **J4Force** |  |  | | 
|6 | **J5Force** |  |  | | 
|7 | **J1Friction** | 5 / 0-50 | unitless | fixed point? | <sup><a href="#2">2</a></sup> The friction felt during FollowMe mode
|8 | **J3Friction** | 5 / 0-50 | unitless | fixed point? | 
|9 | **J2Friction** | 5 / 0-50 | unitless | fixed point? | 
|10 | **J4Friction** | 15 / 0-150 | unitless | fixed point? | 
|11 | **J5Friction** | 15 / 0-150 | unitless | fixed point? | 
|12 | **J1BoundryHigh** | 180 / -180-180 | deg / arcsec| integer | Set all Boundaries at once with "B" [Oplet](Command-oplet-instruction)
|13 | **J1BoundryLow** | 180 / -180-180 | deg / arcsec| integer | 
|14 | **J3BoundryHigh** | 180 / -180-180 | deg / arcsec| integer | 
|15 | **J3BoundryLow** | 180 / -180-180 | deg / arcsec| integer | 
|16 | **J2BoundryHigh** | 180 / -180-180 | deg / arcsec| integer | 
|17 | **J2BoundryLow** | 180 / -180-180 | deg / arcsec| integer | 
|18 | **J4BoundryHigh** | 180 / -180-180 | deg / arcsec| integer | 
|19 | **J4BoundryLow** | 180 / -180-180 | deg / arcsec| integer | 
|20 | **J5BoundryHigh** | 180 / -180-180 | deg / arcsec| integer | 
|21 | **J5BoundryLow** | 180 / -180-180 | deg / arcsec| integer | 
|22 | **GripperMotor** | 0/0-1, 0/0-12000, 0/0-12000 | ON/OFF, ON_PWM, OFF_PWM | bool, int, int | Set PWM output on J19 1st parameter starts PWM with 1, stop with 0. After 2019/05/23: 2nd parameter is PWM on time, 3rd is off time. See W commands 73 - 74.
|23 | **EERoll** | 512 / 0~1023 | deg / 0.29*deg | | 6th axis position / Tool Interface Roll (was standard servo, [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) changed to [Dynamixel](End-Effector-Servos))
|24 | **EESpan** | 0 / 0~1023 | deg / 0.29*deg | | 7th axis position / End Effector actuation (was standard servo, [May 29,2018](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) changed to [Dynamixel](End-Effector-Servos))
|25 | **StartSpeed** |  |  | | Velocity at start (and currently, the end as well) of the next move in "nbits"<sup><a href="#1">1</a></sup>
|26 | **EndSpeed** |  |  | | (not implemented) Currently StartSpeed is the speed for both the start and end of each move. 
|27 | **ServoSet2X** |  |  | | Address, Register, Value (as of [2018/05/29](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) was "End")  see: [Servo](End-Effector-Servos)
|28 | **ServoSet** |  |  | | | Address, Register, String [2018/05/29](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93). Needs work see [Issue 32](https://github.com/HaddingtonDynamics/Dexter/issues/32) [Needed](../issues/32) for [Screen](End-Effector-Screen)
|29 | **RebootServo** |  |  | | [2018/05/29](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93)
|30 | **RunFile** |  |  | | FilePathName [2018/10/12](../commit/e3cccb88cf9b1671c5b2f13c77d1ee00a967a528#diff-691272021fae98368efb598f8e089c16) If it exists, the file will be opened and all instructions from it parsed and executed. By convention, files end with `.make_ins` extension. Each line should be one instruction just like any sent over the socket connection (the job, instruction, start, and end time values are NOT included; start with the oplet). A sample line might be <BR>`S J1_PID_P 0.2 ; set base drive` <BR>Anything after a ';' is ignored allowing comments. <BR>Was [2018/08/24](../commit/f0d9fa772ba6c3eee979e62a071bca487a084c21) `"Ctrl" parms/values` for compact setting of values related to operational mode but this was depreciated and replaced with RunFile.
|31 | **J1_PID_P** | 0.2 / 0 - 1 | unitless  | float | Float [Oct 12, 2018](../commit/e3cccb88cf9b1671c5b2f13c77d1ee00a967a528#diff-691272021fae98368efb598f8e089c16) Set Joint PID P with a floating point number. Helps with [Issue #33](https://github.com/HaddingtonDynamics/Dexter/issues/33#issuecomment-427921288)
|32 | **J2_PID_P** | 0.2 / 0 - 1 | unitless | float | 
|33 | **J3_PID_P** | 2 / 0 - 4 | unitless | float | 
|34 | **J4_PID_P** | 0.1 / 0 - 0.5 | unitless | float | 
|35 | **J5_PID_P** | 0.1 / 0 - 0.5 | unitless | float | 
|36 | <sup>**AngularSpeed**</sup> | 30 / 1 - 45 | deg/s / arcsec/s | integer | Speed to ramp up to at given acceleration. Same as MaxSpeed but with human readable units.
|37 | <sup>**AngularSpeedStartAndEnd**</sup> | 0.1 / 0 - 45 | deg/s / arcsec/s | integer | Speed to ramp up from and down to. Same as StartSpeed but with human readable units. 
|38 | <sup>**AngularAcceleration**</sup>  | 0.1 / 0.0003 - 0.1 | deg/s<sup>2</sup> | integer | 
|39 | <sup>**CartesianSpeed**</sup> | 300000 / 10000 - 500000 | micron/s | integer | 
|40 | <sup>**CartesianSpeedStart**</sup> | 0 / 0 - 500000 | micron/s | integer | 
|41 | <sup>**CartesianSpeedEnd**</sup> | 0 / 0 - 500000 | micron/s | integer | 
|42 | <sup>**CartesianAcceleration**</sup> | 1000000 / 0 - 500000 | micron/s<sup>2</sup> | integer | 
|43 | <sup>**CartesianStepSize**</sup> | 10 / 1 - 10000 | micron | integer | 
|44 | <sup>**CartesianPivotSpeed**</sup> | 108000 / 1 - 360000 | arcsec/s | integer | 
|45 | <sup>**CartesianPivotSpeedStart**</sup> | 0 / 1 - 360000 | arcsec/s | integer | 
|46 | <sup>**CartesianPivotSpeedEnd**</sup> | 0 / 1 - 360000 | arcsec/s | integer | 
|47 | <sup>**CartesianPivotAcceleration**</sup> | 10800000 / 1 - 10800000 | arcsec/s<sup>2</sup>                           | integer | 
|48 | <sup>**CartesianPivotStepSize**</sup> | ? / ? | arcsec | integer | 
|49 | **EyeNumbers** | 0/-200 to +200 |  | 5 integers | Sets the index for which Eye Dexter thinks it is at. Where it is within an eye is calculated with encoder measurements and effectively cannot be set. Takes 5 signed integer arguments where 0 is the circle that contains 0 degrees. See [read_from_robot, #EyeNumbers](https://github.com/HaddingtonDynamics/Dexter/wiki/read-from-robot#keywords).
|50 | **CommandedAngles** |  | arcsec / arcsec | 5 integers | Sets where Dexter thinks it is without actually moving the robot. Takes 5 angles in integer arcseconds. These are the same values that are returned in the robot status under [*_AT]( https://github.com/HaddingtonDynamics/Dexter/wiki/status-data#joint-data-meanings).
|51 | **LinkLengths** | 228600, 320676, 330201, 50801, 82551 | microns | 5 integers | Sets the lengths of each link in the arm for the onboard kinematics. <br>Link 1: Base mount to Joint 2 pivot.<br>Link 2: Joint 2 pivot to Joint 3 pivot.<br>Link 3: Joint 3 pivot to Joint 4 pivot. <br>Link 4: Joint 4 pivot to axis of tool interface. <br>Link 5: Axis of Joint 4 to end effector (note: this depends on the end effector)
|52 | <sup>**RawEncoderErrorLimits**</sup> | 3600 / 0-1296000 | arcsec | 5 integers | [2019/04/22](https://github.com/HaddingtonDynamics/Dexter/commit/8efff90396c50680f40f52a196b21d2c92cc0088) The largest error allowed between the raw encoder reading of position and the expected position for each joint. Part of the status monitor. Will inject a returned error status on the next command and write out and entry with more information to /srv/samba/share/errors.log
|53 | **RawVelocityLimits** | 3600 / 0-1296000 | arcsec | 5 integers | [2019/04/22](https://github.com/HaddingtonDynamics/Dexter/commit/8efff90396c50680f40f52a196b21d2c92cc0088) The largest velocity allowed between subsequent readings of the raw encoder position. Part of the status monitor. Will inject a returned error status on the next command and write out and entry with more information to /srv/samba/share/errors.log

<a name="1">Note 1</a> nbits are a value added to a register every clock cycle for a 128Khz clock such that once it overflows, an action will be taken. Converted automatically by DDE [in the socket communications code](https://github.com/cfry/dde/blob/33f06f03167a8c7b443a42ad0b3560df39ed7a17/core/socket.js#L98) for the "MaxSpeed", "StartSpeed", and "Acceleration" parameters. The global constant "_nbits_cf" is 7754.73550222 and the values given are multiplied by that constant. This converts degrees / second (or degrees / second / second in the case of Acceleration) into nbits. Setting these values too low with effectively lockup the FPGA as it will take far too long to complete a movement.

<a name="2">Note 2</a> Frictions can be fractional, but are limited to a range of +-(256 + 1/256). E.g. 1.1 becomes 1 plus a fractional value of 25/256

Test for new table format:

<table>
 <tr>
   <td>#, <b>Name</b> <br>(use either) </td>
   <td> min|initial|max <br>in DDE units </td>
   <td> min|initial|max <br>in Dexter units  </td>
   </tr>
 <tr>
   <td colspan=3>Description </td>
   </tr>
 <tr>
   <td>0,&nbsp;<b>MaxSpeed</b></td>
   <td>0.001&nbsp;|&nbsp;30&nbsp;|&nbsp;45 <br>deg/s</td>
   <td>8&nbsp;|&nbsp;<a href="https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF">250000</a>&nbsp;|&nbsp;348963 <br>integer nbits</td>
   </tr>
 <tr>
   <td colspan=3>Maximum velocity of next move in "nbits"<sup><a href="#1">1</a></sup>. See also StartSpeed below. </td>
   </tr>
 <tr>
   <td>1,&nbsp;<b>Acceleration</b> </td>
   <td> 0.0001&nbsp;|&nbsp;0.0001&nbsp;|&nbsp;0.1 <br>deg/s<sup>2</sup> </td>
   <td> 1&nbsp;|&nbsp;<a href="https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF">3</a>&nbsp;|&nbsp;775 <br>integer nbits </td>
   </tr>
 <tr>
   <td colspan=3>Maximum acceleration of next move in "nbits"<sup><a href="#1">1</a></sup>. </td>
   </tr>
 <tr>
   <td>2, <b>J1Force</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>3, <b>J3Force</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>4, <b>J2Force</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>5, <b>J4Force</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>6, <b>J5Force</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td colspan=3>Joint Forces: How hard each joint tries to return to commanded position in force protect modes </td>
   </tr>
 <tr>
   <td>7, <b>J1Friction</b> </td>
   <td> 5 unitless </td>
   <td> 0-50 fixed point 8.8 </td>
   </tr>
 <tr>
   <td>8, <b>J3Friction</b> </td>
   <td> 5 unitless </td>
   <td> 0-50 fixed point 8.8  </td>
   </tr>
 <tr>
   <td>9, <b>J2Friction</b> </td>
   <td> 5 unitless </td>
   <td> 0-50 fixed point 8.8  </td>
   </tr>
 <tr>
   <td>10, <b>J4Friction</b> </td>
   <td> 15 unitless </td>
   <td> 0-150 fixed point 8.8  </td>
   </tr>
 <tr>
   <td>11, <b>J5Friction</b> </td>
   <td> 15 unitless </td>
   <td> 0-150 fixed point 8.8  </td>
   </tr>
 <tr>
   <td colspan=3><sup><a href="#2">Joint Frictions: 2</a></sup> The friction felt during FollowMe mode </td>
   </tr>
 <tr>
   <td>12,&nbsp;<b>J1BoundryHigh</b> </td>
   <td> -180&nbsp;|&nbsp;150&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;540000&nbsp;|&nbsp;648000 integer arcsec  </td>
   </tr>
 <tr>
   <td>13,&nbsp;<b>J1BoundryLow</b></td>
   <td>-180 | -150 | 180 deg</td>
   <td>-648000 | -540000 | 648000 integer arcsec</td>
   </tr>
 <tr>
   <td colspan=3></td>
   </tr>
 <tr>
   <td>14,&nbsp;<b>J3BoundryHigh</b> </td>
   <td> -180&nbsp;|&nbsp;150&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;540000&nbsp;|&nbsp;648000 integer arcsec  </td>
   </tr>
 <tr>
   <td>15,&nbsp;<b>J3BoundryLow</b> </td>
   <td> -180&nbsp;|&nbsp;-150&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;-540000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>16,&nbsp;<b>J2BoundryHigh</b> </td>
   <td> -180&nbsp;|&nbsp;90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>17,&nbsp;<b>J2BoundryLow</b> </td>
   <td> -180&nbsp;|&nbsp;-90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;-324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>18,&nbsp;<b>J4BoundryHigh</b> </td>
   <td> -180&nbsp;|&nbsp;90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>19,&nbsp;<b>J4BoundryLow</b> </td>
   <td> -180&nbsp;|&nbsp;-90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;-324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>20,&nbsp;<b>J5BoundryHigh</b> </td>
   <td> -180&nbsp;|&nbsp;90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>21,&nbsp;<b>J5BoundryLow</b> </td>
   <td> -180&nbsp;|&nbsp;-90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;-324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td colspan=3>Joint Boundaries: Set all Boundaries at once with "B" <a href="Command-oplet-instruction">Oplet</a>. (note order if referencing by number, Joint 3 and 2 are swapped here) </td>
   </tr>
 <tr>
   <td>22,&nbsp;<b>GripperMotor</b></td>
   <td> 0|0|1&nbsp;ON/OFF, 0|0|100%&nbsp;ON_PWM, 0|0|100%&nbsp;OFF_PWM </td>
   <td> 0|0|1&nbsp;bool, 0|0|12000&nbsp;int, 0|0|12000&nbsp;int</td>
   </tr>
 <tr>
   <td colspan=3>Set PWM output on J19 1st parameter starts PWM with 1, stop with 0. After 2019/05/23: 2nd parameter is PWM on time, 3rd is off time. See W commands 73 - 74. </td>
   </tr>
 <tr>
   <td>23,&nbsp;<b>EERoll</b></td>
   <td> 0-300 deg </td>
   <td> 0|512|~1023 servo units (0.29*deg) </td>
   </tr>
 <tr>
   <td colspan=3>6th axis position / Tool Interface Roll (was standard servo, <a href="../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93">May 29,2018</a> changed to <a href="End-Effector-Servos">Dynamixel</a>) </td>
   </tr>
 <tr>
   <td>24,&nbsp;<b>EESpan</b> </td>
   <td> 0-300 deg </td>
   <td> 0|0|~1023 servo units (0.29*deg) </td>
   </tr>
 <tr>
   <td colspan=3>7th axis position / End Effector actuation (was standard servo, <a href="../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93">May 29,2018</a> changed to <a href="End-Effector-Servos">Dynamixel</a>) </td>
   </tr>
 <tr>
   <td>25,&nbsp;<b>StartSpeed</b></td>
   <td> 0.0001|1|? degrees/second</td>
   <td> 1|7754|? nbits </td>
   </tr>
 <tr>
   <td colspan=3>Velocity at start (and currently, the end as well) of the next move in "nbits"<sup><a href="#1">1</a></sup> </td>
   </tr>
 <tr>
   <td>26,&nbsp;<b>EndSpeed</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td colspan=3>(not implemented) Currently StartSpeed is the speed for both the start and end of each move.  </td>
   </tr>
 <tr>
   <td>27,&nbsp;<b>ServoSet2X</b></td>
   <td></td>
   <td>Servo Address, Register, Value</td>
   </tr>
 <tr>
   <td colspan=3>Low level servo register write (as of <a href="../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93">2018/05/29</a> was "End")  see: <a href="End-Effector-Servos">Servo</a> </td>
   </tr>
 <tr>
   <td>28,&nbsp;<b>ServoSet</b></td>
   <td>  </td>
   <td>Servo Address, Register, String</td>
   </tr>
 <tr>
   <td colspan=3>Send string to servo <a href="../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93">2018/05/29</a>. Needs work see <a href="https://github.com/HaddingtonDynamics/Dexter/issues/32">Issue 32</a> <a href="../issues/32">Needed</a> for <a href="End-Effector-Screen">Screen</a> </td>
   </tr>
 <tr>
   <td>29,&nbsp;<b>RebootServo</b></td>
   <td></td>
   <td>Servo Address</td>
      </tr>
 <tr>
   <td colspan=3>Reset servo. <a href="../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93">2018/05/29</a></td>
   </tr>
 <tr>
   <td>30,&nbsp;<b>RunFile</b></td>
   <td>FilePathName</td>
   <td>FilePathName</td>
   </tr>
 <tr>
   <td colspan=3>RunFile: Added<a href="../commit/e3cccb88cf9b1671c5b2f13c77d1ee00a967a528#diff-691272021fae98368efb598f8e089c16">2018/10/12</a> If it exists, the file will be opened and all instructions from it parsed and executed. By convention, files end with <tt>.make_ins</tt> extension. Each line should be one instruction just like any sent over the socket connection (the job, instruction, start, and end time values are NOT included; start with the oplet). A sample line might be <BR><tt>S J1_PID_P 0.2 ; set base drive</tt> <BR>Anything after a ';' is ignored allowing comments. <BR>Was <a href="../commit/f0d9fa772ba6c3eee979e62a071bca487a084c21">2018/08/24</a> <tt>"Ctrl" parms/values</tt> for compact setting of values related to operational mode but this was depreciated and replaced with RunFile. </td>
   </tr>
 <tr>
   <td>31, <b>J1_PID_P</b></td>
   <td>0 | 0.2 | 1 unitless</td>
   <td>0 | 0.2 | 1 float</td>
   </tr>
 <tr>
   <td>32, <b>J2_PID_P</b></td>
   <td>0 | 0.2 | 1 unitless</td>
   <td>0 | 0.2 | 1 float</td>
   </tr>
 <tr>
   <td>33, <b>J3_PID_P</b></td>
   <td>0 | 2 | 4 unitless</td>
   <td>0 | 2 | 4 float</td>
   </tr>
 <tr>
   <td>34, <b>J4_PID_P</b></td>
   <td>0 | 0.1 | 0.5 unitless</td>
   <td>0 | 0.1 | 0.5 float</td>
   </tr>
 <tr>
   <td>35, <b>J5_PID_P</b></td>
   <td>0 | 0.1 | 0.5 unitless</td>
   <td>0 | 0.1 | 0.5 float</td>
 <tr>
   <td colspan=3><a href="../commit/e3cccb88cf9b1671c5b2f13c77d1ee00a967a528#diff-691272021fae98368efb598f8e089c16">2018/08/12</a> Set Joint PID P with a floating point number. Helps with <a href="https://github.com/HaddingtonDynamics/Dexter/issues/33#issuecomment-427921288">Issue #33</a>
   </tr>
 </table>