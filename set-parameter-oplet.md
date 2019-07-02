High level Parameters in the Dexter [Firmware](Firmware) are set with the "S" [Oplet](Command-oplet-instruction). For direct control of low level [FPGA](Gateware) parameters, see the ["w" Oplet](oplet-write)

**Queued** instructions are stored in the FPGA in an internal movement FIFO and executed sequentially.

<table>
 <tr>
   <td>#, <b>Name</b> <br>(use either)</td>
   <td> min | initial | max <br>in <a href="DDE">DDE</a> units</td>
   <td> min | initial | max <br>in Dexter <a href="Firmware">Firmware</a> units</td>
   </tr>
 <tr>
   <td colspan=3>Description</td>
   </tr>
 <tr>
   <td>0,&nbsp;<b>MaxSpeed</b></td>
   <td>0.001&nbsp;|&nbsp;30&nbsp;|&nbsp;45 deg/s</td>
   <td>8&nbsp;|&nbsp;<a href="https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF">250000</a>&nbsp;|&nbsp;348963 integer nbits</td>
   </tr>
 <tr>
   <td colspan=3>Queued. Maximum velocity of next move in "nbits"<sup><a href="#1">1</a></sup>. See also StartSpeed below. </td>
   </tr>
 <tr>
   <td>1,&nbsp;<b>Acceleration</b> </td>
   <td> 0.0001&nbsp;|&nbsp;0.0001&nbsp;|&nbsp;0.1 deg/s<sup>2</sup> </td>
   <td> 1&nbsp;|&nbsp;<a href="https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF">3</a>&nbsp;|&nbsp;775 integer nbits </td>
   </tr>
 <tr>
   <td colspan=3>Queued. Maximum acceleration of next move in "nbits"<sup><a href="#1">1</a></sup>. </td>
   </tr>
 <tr>
   <td>2,&nbsp;<b>J1Force</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>3,&nbsp;<b>J3Force</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>4,&nbsp;<b>J2Force</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>5,&nbsp;<b>J4Force</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>6,&nbsp;<b>J5Force</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td colspan=3>Joint Forces: How hard each joint tries to return to commanded position in force protect modes </td>
   </tr>
 <tr>
   <td>7,&nbsp;<b>J1Friction</b> </td>
   <td> 5 unitless </td>
   <td> 0-50 fixed point 8.8 </td>
   </tr>
 <tr>
   <td>8,&nbsp;<b>J3Friction</b> </td>
   <td> 5 unitless </td>
   <td> 0-50 fixed point 8.8  </td>
   </tr>
 <tr>
   <td>9,&nbsp;<b>J2Friction</b> </td>
   <td> 5 unitless </td>
   <td> 0-50 fixed point 8.8  </td>
   </tr>
 <tr>
   <td>10,&nbsp;<b>J4Friction</b> </td>
   <td> 15 unitless </td>
   <td> 0-150 fixed point 8.8  </td>
   </tr>
 <tr>
   <td>11,&nbsp;<b>J5Friction</b> </td>
   <td> 15 unitless </td>
   <td> 0-150 fixed point 8.8  </td>
   </tr>
 <tr>
   <td colspan=3>Joint Frictions: <sup><a href="#2">2</a></sup> The friction felt during FollowMe mode. Frictions can be fractional, but are limited to a range of +-(256 + 1/256). E.g. 1.1 becomes 1 plus a fractional value of 25/256.</td>
   </tr>
 <tr>
   <td>12,&nbsp;<b>J1BoundryHigh</b> </td>
   <td> -180&nbsp;|&nbsp;150&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;540000&nbsp;|&nbsp;648000 integer arcsec  </td>
   </tr>
 <tr>
   <td>13,&nbsp;<b>J1BoundryLow</b></td>
   <td>-150 (-180 to 180) deg</td>
   <td>-648000 | -540000 | 648000 integer arcsec</td>
   </tr>
 <tr>
   <td>14,&nbsp;<b>J3BoundryHigh</b> </td>
   <td> (150) -180 to 180 deg </td>
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
   <td> 0|0|1&nbsp;ON/OFF, 0|0|100&nbsp;ON_PWM, 0|0|100&nbsp;OFF_PWM </td>
   <td> 0=off 1=on,<br>(0) 0 to 12000 on time, <br>(0) 0 to 12000 off time</td>
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
   <td colspan=3>7th axis position / <a href="End-Effectors">End Effector</a> actuation (was standard servo, <a href="../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93">May 29,2018</a> changed to <a href="End-Effector-Servos">Dynamixel</a>) </td>
   </tr>
 <tr>
   <td>25,&nbsp;<b>StartSpeed</b></td>
   <td> 0.0001|1|? degrees/second</td>
   <td> 1|7754|? nbits </td>
   </tr>
 <tr>
   <td colspan=3>Queued. Velocity at start (and currently, the end as well) of the next move in "nbits"<sup><a href="#1">1</a></sup>. See #36 "AngularSpeed" below.</td>
   </tr>
 <tr>
   <td>26,&nbsp;<b>EndSpeed</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td colspan=3>(not implemented) Currently StartSpeed is the speed for both the start and end of each move. See #37 "AngularSpeedStartAndEnd" below.</td>
   </tr>
 <tr>
   <td>27,&nbsp;<b>ServoSet2X</b></td>
   <td></td>
   <td>Servo Address (3=J6, 1=J7), <a href="http://emanual.robotis.com/docs/en/dxl/x/xl320/#control-table-of-eeprom-area">Register</a>, Value</td>
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
   </tr>
 <tr>
   <td colspan=3><a href="../commit/e3cccb88cf9b1671c5b2f13c77d1ee00a967a528#diff-691272021fae98368efb598f8e089c16">2018/08/12</a> Set Joint PID P with a floating point number. Helps with <a href="https://github.com/HaddingtonDynamics/Dexter/issues/33#issuecomment-427921288">Issue #33</a>
   </tr>

 <tr>
   <td>36,&nbsp;<b>AngularSpeed</b></td>
   <td>1 | 30 | 45 deg/s</td>
   <td>3600 | 108000 | 162000 integer arcsec/s</td>
   </tr>
 <tr>
   <td colspan=3>Speed to ramp up to at given acceleration. Same as MaxSpeed but with human readable units.</td>
   </tr>
 <tr>
   <td>37,&nbsp;<b>AngularSpeedStartAndEnd</b></td>
   <td>1 | 30 | 45 float deg/s</td>
   <td>3600 | 108000 | 162000 integer arcsec/s</td>
   </tr>
 <tr>
   <td colspan=3>Speed to ramp up from and down to. Same as StartSpeed but with human readable units. </td>
   </tr>
 <tr>
   <td>38,&nbsp;<b>AngularAcceleration</b></td>
   <td>0.0003 | 0.1 | 0.1? float deg/s<sup>2</sup></td>
   <td>1 | 360 | 360? integer arcsec/s<sup>2</sup></td>
   </tr>
 <tr>
   <td colspan=3>Replaces Acceleration, specifying with human readable units. </td>
   </tr>
 <tr>
   <td>39,&nbsp;<b>CartesianSpeed</b></td>
   <td>0.01 | 0.3 | 0.5 meters/s</td>
   <td>10000 | 300000 | 500000 integer micron/s</td>
   </tr>
 <tr>
   <td colspan=3></td>
   </tr>
 <tr>
   <td>40,&nbsp;<b>CartesianSpeedStart</b></td>
   <td>0 | 0 | 0.5 meters/s</td>
   <td>0 | 0 | 500000 integer micron/s</td>
   </tr>
 <tr>
   <td colspan=3></td>
   </tr>
 <tr>
   <td>41,&nbsp;<b>CartesianSpeedEnd</b></td>
   <td>0 | 0 | 0.5 meters/s</td>
   <td>0 | 0 | 500000 integer micron/s</td>
   </tr>
 <tr>
   <td colspan=3></td>
   </tr>
 <tr>
   <td>42,&nbsp;<b>CartesianAcceleration</b></td>
   <td>0 | 0.1 | 0.5 meters/s<sup>2</sup></td>
   <td>0 | 1000000 | 500000 integer micron/s<sup>2</sup></td>
   </tr>
 <tr>
   <td colspan=3></td>
   </tr>
 <tr>
   <td>43,&nbsp;<b>CartesianStepSize</b></td>
   <td>0.000001 | 0.00001 | 0.01 meter</td>
   <td>1 | 10 | 10000 integer micron</td>
   </tr>
 <tr>
   <td colspan=3></td>
   </tr>
 <tr>
   <td>44,&nbsp;<b>CartesianPivotSpeed</b></td>
   <td>0.00027777 | 30 | 100 float deg</td>
   <td>1 | 108000 | 360000 integer arcsec/s</td>
   </tr>
 <tr>
   <td colspan=3>The angular speed of rotation around a fixed point by the end effector.</td>
   </tr>
 <tr>
   <td>45,&nbsp;<b>CartesianPivotSpeedStart</b></td>
   <td>0 | 0 | 100 deg/s</td>
   <td>0 | 0 | 360000 integer arcsec/s</td>
   </tr>
 <tr>
   <td colspan=3>The angular starting speed of rotation around a fixed point by the end effector.</td>
   </tr>
 <tr>
   <td>46,&nbsp;<b>CartesianPivotSpeedEnd</b></td>
   <td>0 | 0 | 100 deg/s</td>
   <td>0 | 0 | 360000 |integer arcsec/s</td>
   </tr>
 <tr>
   <td colspan=3>The angular ending speed of rotation around a fixed point by the end effector.</td>
   </tr>
 <tr>
   <td>47,&nbsp;<b>CartesianPivotAcceleration</b></td>
   <td></td>
   <td>1 | 10800000 | 10800000 integer arcsec/s<sup>2</sup></td>
   </tr>
 <tr>
   <td colspan=3>The angular acceleration of rotation around a fixed point by the end effector.</td>
   </tr>
 <tr>
   <td>48,&nbsp;<b>CartesianPivotStepSize</b></td>
   <td></td>
   <td> ? / ? | arcsec integer</td>
   </tr>
 <tr>
   <td colspan=3>TBD. Granularity of rotation around a fixed point by the end effector</td>
   </tr>
 <tr>
   <td>49,&nbsp;<b>EyeNumbers</b></td>
   <td></td>
   <td> 0 | 255 | 512 (5 integers)</td>
   </tr>
 <tr>
   <td colspan=3>Sets the index for which Eye Dexter thinks it is at. Where it is within an eye is calculated with encoder measurements and effectively cannot be set. Takes 5 signed integer arguments where 0 is the circle that contains 0 degrees. See <a href="https://github.com/HaddingtonDynamics/Dexter/wiki/read-from-robot#keywords">read_from_robot, #EyeNumbers</a>. </td>
   </tr>
 <tr>
   <td>50,&nbsp;<b>CommandedAngles</b></td>
   <td></td>
   <td>arcsec (5 integers)</td>
   </tr>
 <tr>
   <td colspan=3>Sets where Dexter thinks it is without actually moving the robot. Takes 5 angles in integer arcseconds. These are the same values that are returned in the robot status under <a href="https://github.com/HaddingtonDynamics/Dexter/wiki/status-data#joint-data-meanings">*_AT</a>. See boundaries for limits.</td>
   </tr>
 <tr>
   <td>51,&nbsp;<b>LinkLengths</b></td>
   <td></td>
   <td> 82551, 50801, 330201, 320676, 228600 microns (5 integers, L5 to L1)</td>
   </tr>
 <tr>
   <td colspan=3><a href="https://github.com/HaddingtonDynamics/Dexter/commit/08fb9a8ca9cee5ed71d580e834f16daa9561f4da#diff-691272021fae98368efb598f8e089c16">2019/05/29</a> Sets the lengths of each link in the arm for the onboard kinematics. Parameters:<br>L1: Base mount to Joint 2 pivot.<br>L2: Joint 2 pivot to Joint 3 pivot.<br>L3: Joint 3 pivot to Joint 4 pivot. <br>L4: Joint 4 pivot to axis of tool interface. <br>L5: Axis of Joint 4 to end effector (note: this depends on the end effector and is most likely to change). <br>Note: <b>Order of parameters is L5 to L1 so that if only 1 is specified, it changes L5 vs L1.</b></td>
   </tr>
 <tr>
   <td>52,&nbsp;<b>RawEncoderErrorLimits</b></td>
   <td></td>
   <td>0 | 3600 | 1296000 arcsec (5 integers)</td>
   </tr>
 <tr>
   <td colspan=3><a href="https://github.com/HaddingtonDynamics/Dexter/commit/8efff90396c50680f40f52a196b21d2c92cc0088">2019/04/22</a> The largest error allowed between the raw encoder reading of position and the expected position for each joint. Part of the status monitor. Will inject a returned error status on the next command and write out and entry with more information to /srv/samba/share/errors.log </td>
   </tr>
 <tr>
   <td>53,&nbsp;<b>RawVelocityLimits</b></td>
   <td>0 | 100 | deg/s</td>
   <td>0 | 360000 | 1296000 | arcsec/s (5 integers)</td>
   </tr>
 <tr>
   <td colspan=3><a href="https://github.com/HaddingtonDynamics/Dexter/commit/8efff90396c50680f40f52a196b21d2c92cc0088">2019/04/22</a> The largest velocity allowed between subsequent readings of the raw encoder position. Part of the status monitor. Will inject a returned error status on the next command and write out and entry with more information to /srv/samba/share/errors.log <b>and will switch to open loop mode</b>.</td>
   </tr>

 <tr>
   <td></td>
   <td></td>
   <td></td>
   </tr>
 <tr>
   <td colspan=3></td>
   </tr>



 </table>


<a name="1">Note 1</a> nbits are a value added to a register every clock cycle for a 128Khz clock such that once it overflows, an action will be taken. Converted automatically by DDE [in the socket communications code](https://github.com/cfry/dde/blob/33f06f03167a8c7b443a42ad0b3560df39ed7a17/core/socket.js#L98) for the "MaxSpeed", "StartSpeed", and "Acceleration" parameters. The global constant "_nbits_cf" is 7754.73550222 and the values given are multiplied by that constant. This converts degrees / second (or degrees / second / second in the case of Acceleration) into nbits. Setting these values too low with effectively lockup the FPGA as it will take far too long to complete a movement.

<a name="2">Note 2</a> Frictions can be fractional, but are limited to a range of +-(256 + 1/256). E.g. 1.1 becomes 1 plus a fractional value of 25/256