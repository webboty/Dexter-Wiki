High level Parameters in the Dexter [Firmware](Firmware) are set with the "S" [Oplet](Command-oplet-instruction). For direct control of low level [FPGA](Gateware) parameters, see the ["w" Oplet](oplet-write)

Instructions marked as "**Queued**" are stored in the FPGA in the 16 level internal movement FIFO and executed sequentially.

<table style="overflow-y: auto; height: 100px;">
 <tr>
   <td colspan=3 style="position: sticky; top: 0;"><b>Description</b></td>
   </tr>
 <tr>
   <td style="position: sticky; top: 50;"><b>#, Name</b> <br>(use either)</td>
   <td style="position: sticky; top: 50;"> min | initial | max <br>in <a href="DDE">DDE</a> units</td>
   <td style="position: sticky; top: 50;"> min | initial | max <br>in Dexter <a href="Firmware">Firmware</a> units</td>
   </tr>
 <tr>
   <td colspan=3><a name="MaxSpeed"><b>MaxSpeed</b></a>: Queued. Maximum velocity of next move in "nbits"<sup><a href="#1">1</a></sup>. Replaced by <a href="#AngularSpeed">AngularSpeed</a>. See also <a href="#StartSpeed">StartSpeed</a> below. </td>
   </tr>
 <tr>
   <td>0,&nbsp;MaxSpeed</td>
   <td>0.001&nbsp;|&nbsp;30&nbsp;|&nbsp;45 deg/s</td>
   <td>8&nbsp;|&nbsp;<a href="https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF">250000</a>&nbsp;|&nbsp;348963 integer nbits</td>
   </tr>
 <tr>
   <td colspan=3><a name="Acceleration"><b>Acceleration</b></a>: Queued. Maximum acceleration of next move in "nbits"<sup><a href="#1">1</a></sup>. Replaced by <a href="#AngularAcceleration">AngularAcceleration</a> below</td>
   </tr>
 <tr>
   <td>1,&nbsp;Acceleration </td>
   <td> 0.0001&nbsp;|&nbsp;0.0001&nbsp;|&nbsp;0.1 deg/s<sup>2</sup> </td>
   <td> 1&nbsp;|&nbsp;<a href="https://github.com/HaddingtonDynamics/Dexter/search?q=ACCELERATION_MAXSPEED_DEF&unscoped_q=ACCELERATION_MAXSPEED_DEF">3</a>&nbsp;|&nbsp;775 integer nbits </td>
   </tr>
 <tr>
   <td colspan=3><a name="JointForces"><b>Joint Forces</b></a>: How hard each joint tries to return to commanded position in force protect modes. Useful for removing the force of gravity. Also used to provide haptic force feedback in teleoperation or VR systems. </td>
   </tr>
 <tr>
   <td>2,&nbsp;J1Force </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>3,&nbsp;J3Force</td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>4,&nbsp;<b>J2Force</b> </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>5,&nbsp;J4Force</td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td>6,&nbsp;J5Force</td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td colspan=3><a name="JointFrictions"><b>Joint Frictions</b></a>: <sup><a href="#2">2</a></sup> The friction felt during FollowMe mode. Coefficient to scale the position error; feels like friction. Can be negative. Used in most modes, very important in follow mode. Frictions can be fractional, but are limited to a range of +-(256 + 1/256). E.g. 1.1 becomes 1 plus a fractional value of 25/256.</td>
   </tr>
 <tr>
   <td>7,&nbsp;J1Friction </td>
   <td> 5 unitless </td>
   <td> 0-50 fixed point 8.8 </td>
   </tr>
 <tr>
   <td>8,&nbsp;J3Friction </td>
   <td> 5 unitless </td>
   <td> 0-50 fixed point 8.8  </td>
   </tr>
 <tr>
   <td>9,&nbsp;J2Friction </td>
   <td> 5 unitless </td>
   <td> 0-50 fixed point 8.8  </td>
   </tr>
 <tr>
   <td>10,&nbsp;J4Friction </td>
   <td> 15 unitless </td>
   <td> 0-150 fixed point 8.8  </td>
   </tr>
 <tr>
   <td>11,&nbsp;J5Friction </td>
   <td> 15 unitless </td>
   <td> 0-150 fixed point 8.8  </td>
   </tr>
 <tr>
   <td colspan=3><a name="JointBoundaries"><b>Joint Boundaries</b></a>: Set all Boundaries at once with "B" <a href="Command-oplet-instruction">Oplet</a>. Maximum position past which the force position override will be reduced for each joint.
Injects a negative force opposing the external force to limit the amount the joint will move to avoid external forces. (note order if referencing by number, Joint 3 and 2 are swapped here) </td>
   </tr>
 <tr>
   <td>12,&nbsp;J1BoundryHigh </td>
   <td> -180&nbsp;|&nbsp;150&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;540000&nbsp;|&nbsp;648000 integer arcsec  </td>
   </tr>
 <tr>
   <td>13,&nbsp;J1BoundryLow</td>
   <td>-150 (-180 to 180) deg</td>
   <td>-648000 | -540000 | 648000 integer arcsec</td>
   </tr>
 <tr>
   <td>14,&nbsp;J3BoundryHigh </td>
   <td> (150) -180 to 180 deg </td>
   <td> -648000&nbsp;|&nbsp;540000&nbsp;|&nbsp;648000 integer arcsec  </td>
   </tr>
 <tr>
   <td>15,&nbsp;J3BoundryLow </td>
   <td> -180&nbsp;|&nbsp;-150&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;-540000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>16,&nbsp;J2BoundryHigh </td>
   <td> -180&nbsp;|&nbsp;90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>17,&nbsp;J2BoundryLow </td>
   <td> -180&nbsp;|&nbsp;-90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;-324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>18,&nbsp;J4BoundryHigh </td>
   <td> -180&nbsp;|&nbsp;90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>19,&nbsp;J4BoundryLow </td>
   <td> -180&nbsp;|&nbsp;-90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;-324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>20,&nbsp;J5BoundryHigh </td>
   <td> -180&nbsp;|&nbsp;90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td>21,&nbsp;J5BoundryLow </td>
   <td> -180&nbsp;|&nbsp;-90&nbsp;|&nbsp;180 deg </td>
   <td> -648000&nbsp;|&nbsp;-324000&nbsp;|&nbsp;648000 integer arcsec </td>
   </tr>
 <tr>
   <td colspan=3><a name="GripperMotor"><b>Gripper Motor</b></a>: Set PWM output on [J19 pin 7](End-Effectors#j19-pin-7) 1st parameter starts PWM with 1, stop with 0. After 2019/05/23: 2nd parameter is PWM on time, 3rd is off time. Originally controlled by W commands 73 - 74. </td>
   </tr>
 <tr>
   <td>22,&nbsp;GripperMotor</td>
   <td> 0|0|1&nbsp;ON/OFF, 0|0|100&nbsp;ON_PWM, 0|0|100&nbsp;OFF_PWM </td>
   <td> 0=off 1=on,<br>(0) 0 to 12000 on time, <br>(0) 0 to 12000 off time</td>
   </tr>
 <tr>
   <td colspan=3><a name="EERoll"><b>EERoll</b></a>: Set 6th axis position / Tool Interface Roll (was standard servo, <a href="../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93">May 29,2018</a> changed to <a href="End-Effector-Servos">Dynamixel</a>) </td>
   </tr>
 <tr>
   <td>23,&nbsp;EERoll</td>
   <td> 0-300 deg </td>
   <td> 0|512|~1023 servo units (0.29*deg) </td>
   </tr>
 <tr>
   <td colspan=3><a name="EESpan"><b>EESpan</b></a>: Set 7th axis position / <a href="End-Effectors">End Effector</a> actuation (was standard servo, <a href="../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93">May 29,2018</a> changed to <a href="End-Effector-Servos">Dynamixel</a>) </td>
   </tr>
 <tr>
   <td>24,&nbsp;EESpan </td>
   <td> 0-300 deg </td>
   <td> 0|0|~1023 servo units (0.29*deg) </td>
   </tr>
 <tr>
   <td colspan=3><a name="StartSpeed"><b>StartSpeed</b></a>: Queued. Velocity at start (and currently, the end as well) of the next move in "nbits"<sup><a href="#1">1</a></sup>. See #36 "AngularSpeed" below.</td>
   </tr>
 <tr>
   <td>25,&nbsp;StartSpeed</td>
   <td> 0.0001|1|? degrees/second</td>
   <td> 1|7754|? nbits </td>
   </tr>
 <tr>
   <td colspan=3><a name="EndSpeed"><b>EndSpeed</b></a>: <i>(not implemented)</i> Currently StartSpeed is the speed for both the start and end of each move. See #37 "AngularSpeedStartAndEnd" below.</td>
   </tr>
 <tr>
   <td>26,&nbsp;EndSpeed </td>
   <td>  </td>
   <td>  </td>
   </tr>
 <tr>
   <td colspan=3><a name="ServoSet2X"><b>ServoSet2X</b></a>: Low level servo register write (as of <a href="../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93">2018/05/29</a> was "End")  see: <a href="End-Effector-Servos">Servo</a> </td>
   </tr>
 <tr>
   <td>27,&nbsp;ServoSet2X</td>
   <td></td>
   <td>Servo Address (3=J6, 1=J7), <a href="http://emanual.robotis.com/docs/en/dxl/x/xl320/#control-table-of-eeprom-area">Register</a>, Value</td>
   </tr>
 <tr>
   <td colspan=3><a name="ServoSet"><b>ServoSet</b></a>: Send string to servo <a href="../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93">2018/05/29</a>. Needs work see <a href="https://github.com/HaddingtonDynamics/Dexter/issues/32">Issue 32</a> <a href="../issues/32">Needed</a> for <a href="End-Effector-Screen">Screen</a> </td>
   </tr>
 <tr>
   <td>28,&nbsp;ServoSet</td>
   <td>  </td>
   <td>Servo Address, Register, String</td>
   </tr>
 <tr>
   <td colspan=3><a name="RebootServo"><b>RebootServo</b></a>: Reset servo to default values, clear errros. <a href="../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93">2018/05/29</a>. Joint 6 is address 3, Joint 7 is 1</td>
   </tr>
 <tr>
   <td>29,&nbsp;RebootServo</td>
   <td></td>
   <td><a href="End-Effector-Servos">Servo Address</a></td>
      </tr>
 <tr>
   <td colspan=3><a name="RunFile"><b>RunFile</b></a>: Added <a href="../commit/e3cccb88cf9b1671c5b2f13c77d1ee00a967a528#diff-691272021fae98368efb598f8e089c16">2018/10/12</a> If it exists, the file will be opened and all instructions from it parsed and executed. By convention, files end with <tt>.make_ins</tt> extension. Each line should be one instruction just like any sent over the socket connection (the job, instruction, start, and end time values are NOT included; start with the oplet). A sample line might be <BR><tt>S J1_PID_P 0.2 ; set base drive</tt> <BR>Anything after a ';' is ignored allowing comments. <BR>Was <a href="../commit/f0d9fa772ba6c3eee979e62a071bca487a084c21">2018/08/24</a> <tt>"Ctrl" parms/values</tt> for compact setting of values related to operational mode but this was depreciated and replaced with RunFile. <BR>Note: While executing a .make_ins file, Dexter will NOT respond to any input.</td>
   </tr>
 <tr>
   <td>30,&nbsp;RunFile</td>
   <td>FilePathName</td>
   <td>FilePathName</td>
   </tr>
 <tr>
   <td colspan=3><a name="JointPID"><b>Joint PID</b></a>: <a href="../commit/e3cccb88cf9b1671c5b2f13c77d1ee00a967a528#diff-691272021fae98368efb598f8e089c16">2018/08/12</a> Set Joint PID P with a floating point number. Helps with <a href="https://github.com/HaddingtonDynamics/Dexter/issues/33#issuecomment-427921288">Issue #33</a>
   </tr>
 <tr>
   <td>31, J1_PID_P</td>
   <td>0 | 0.2 | 1 unitless</td>
   <td>0 | 0.2 | 1 float</td>
   </tr>
 <tr>
   <td>32, J2_PID_P</td>
   <td>0 | 0.2 | 1 unitless</td>
   <td>0 | 0.2 | 1 float</td>
   </tr>
 <tr>
   <td>33, J3_PID_P</td>
   <td>0 | 2 | 4 unitless</td>
   <td>0 | 2 | 4 float</td>
   </tr>
 <tr>
   <td>34, J4_PID_P</td>
   <td>0 | 0.1 | 0.5 unitless</td>
   <td>0 | 0.1 | 0.5 float</td>
   </tr>
 <tr>
   <td>35, J5_PID_P</td>
   <td>0 | 0.1 | 0.5 unitless</td>
   <td>0 | 0.1 | 0.5 float</td>
   </tr>
 <tr>
   <td colspan=3><a name="AngularSpeed"><b>AngularSpeed</b></a>: Speed to ramp up to at given acceleration. Same as <a href="#MaxSpeed">MaxSpeed</a> but with human readable units.</td>
   </tr>
 <tr>
   <td>36,&nbsp;AngularSpeed</td>
   <td>1 | 30 | 45 deg/s</td>
   <td>3600 | 108000 | 162000 integer arcsec/s</td>
   </tr>
 <tr>
   <td colspan=3><a name="AngularSpeedStartAndEnd">Angular Speed Start And End</a>: Speed to ramp up from and down to. Same as StartSpeed but with human readable units. </td>
   </tr>
 <tr>
   <td>37,&nbsp;<b>AngularSpeedStartAndEnd</b></td>
   <td>1 | 30 | 45 float deg/s</td>
   <td>3600 | 108000 | 162000 integer arcsec/s</td>
   </tr>
 <tr>
   <td colspan=3><a name="AngularAcceleration"><b>Angular Acceleration</b></a>: Replaces Acceleration, specifying with human readable units. </td>
   </tr>
 <tr>
   <td>38,&nbsp;AngularAcceleration</td>
   <td>0.0003 | 0.1 | 0.1? float deg/s<sup>2</sup></td>
   <td>1 | 360 | 360? integer arcsec/s<sup>2</sup></td>
   </tr>
 <tr>
   <td colspan=3><a name="CartesianParameters"><b>Cartesian Parameters</b></a>: These affect the onboard kinematics.</td>
   </tr>
 <tr>
   <td>39,&nbsp;<a name="CartesianSpeed"><b>CartesianSpeed</b></a></td>
   <td>0.01 | 0.3 | 0.5 meters/s</td>
   <td>10000 | 300000 | 500000 integer micron/s</td>
   </tr>
 <tr>
   <td>40,&nbsp;<a name="CartesianSpeedStart"><b>CartesianSpeedStart</b></a></td>
   <td>0 | 0 | 0.5 meters/s</td>
   <td>0 | 0 | 500000 integer micron/s</td>
   </tr>
 <tr>
   <td>41,&nbsp;<a name="CartesianSpeedEnd"><b>CartesianSpeedEnd</b></a></td>
   <td>0 | 0 | 0.5 meters/s</td>
   <td>0 | 0 | 500000 integer micron/s</td>
   </tr>
 <tr>
   <td>42,&nbsp;<a name="CartesianAcceleration"><b>CartesianAcceleration</b></a></td>
   <td>0 | 0.1 | 0.5 meters/s<sup>2</sup></td>
   <td>0 | 1000000 | 500000 integer micron/s<sup>2</sup></td>
   </tr>
 <tr>
   <td>43,&nbsp;<a name="CartesianStepSize"><b>CartesianStepSize</b></a></td>
   <td>0.000001 | 0.00001 | 0.01 meter</td>
   <td>1 | 10 | 10000 integer micron</td>
   </tr>
 <tr>
   <td colspan=3><a name="CartesianPivot"><b>Cartesian Pivot Parameters</b></a>: The angular speed of rotation around a fixed point by the end effector. The start and ending speeds, and acceleration can also be set. Step Size is the granularity of rotation around a fixed point by the end effector, and is not yet implemented</td>
   </tr>
 <tr>
   <td>44,&nbsp;<a name="CartesianPivotSpeed"><b>CartesianPivotSpeed</b></a></td>
   <td>0.00027777 | 30 | 100 float deg</td>
   <td>1 | 108000 | 360000 integer arcsec/s</td>
   </tr>
 <tr>
   <td>45,&nbsp;<a name="CartesianPivotSpeedStart"><b>CartesianPivotSpeedStart</b></a></td>
   <td>0 | 0 | 100 deg/s</td>
   <td>0 | 0 | 360000 integer arcsec/s</td>
   </tr>
 <tr>
   <td>46,&nbsp;<a name="CartesianPivotSpeedEnd"><b>CartesianPivotSpeedEnd</b></a></td>
   <td>0 | 0 | 100 deg/s</td>
   <td>0 | 0 | 360000 |integer arcsec/s</td>
   </tr>
 <tr>
   <td>47,&nbsp;<a name="CartesianPivotAcceleration"><b>CartesianPivotAcceleration</b><a></td>
   <td>deg/s<sup>2</sup></td>
   <td>1 | 10800000 | 10800000 integer arcsec/s<sup>2</sup></td>
   </tr>
 <tr>
   <td>48,&nbsp;<a name="CartesianPivotStepSize"><b>CartesianPivotStepSize</b></a></td>
   <td>deg?</td>
   <td> ? / ? | arcsec integer</td>
   </tr>
 <tr>
   <td colspan=3><a name="EyeNumbers"><b>Eye Numbers</b></a>: Sets the index for which Eye Dexter thinks it is at. Where it is within an eye is calculated with encoder measurements and effectively cannot be set. Takes 5 signed integer arguments where 0 is the circle that contains 0 degrees. See <a href="https://github.com/HaddingtonDynamics/Dexter/wiki/read-from-robot#keywords">read_from_robot, #EyeNumbers</a>. </td>
   </tr>
 <tr>
   <td>49,&nbsp;EyeNumbers</td>
   <td> 0 | 255 | 512 (5 integers)</td>
   <td> 0 | 255 | 512 (5 integers)</td>
   </tr>
 <tr>
   <td colspan=3><a name="CommandedAngles"><b>Commanded Angles</b></a>: Sets where Dexter thinks it is without actually moving the robot. Takes 5 angles in integer arcseconds. These are the same values that are returned in the robot status under <a href="https://github.com/HaddingtonDynamics/Dexter/wiki/status-data#joint-data-meanings">*_AT</a>. See boundaries for limits.</td>
   </tr>
 <tr>
   <td>50,&nbsp;CommandedAngles</td>
   <td>degrees</td>
   <td>arcsec (5 integers)</td>
   </tr>
 <tr>
   <td colspan=3><a name="LinkLengths"><b>Link Lengths</b></a>: Added <a href="https://github.com/HaddingtonDynamics/Dexter/commit/08fb9a8ca9cee5ed71d580e834f16daa9561f4da#diff-691272021fae98368efb598f8e089c16">2019/05/29</a> Sets the lengths of each link in the arm for the onboard kinematics. Parameters:<br>L1: Base mount to Joint 2 pivot.<br>L2: Joint 2 pivot to Joint 3 pivot.<br>L3: Joint 3 pivot to Joint 4 pivot. <br>L4: Joint 4 pivot to axis of tool interface. <br>L5: Axis of Joint 4 to end effector (note: this depends on the end effector and is most likely to change). <br>Note: <i>Order of parameters is L5 to L1 so that if only 1 is specified, it changes L5 vs L1.</i></td>
   </tr>
 <tr>
   <td>51,&nbsp;LinkLengths</td>
   <td>meters (5 floats, L5 to L1)</td>
   <td> 82551, 50801, 330201, 320676, 228600 microns (5 integers, L5 to L1)</td>
   </tr>
 <tr>
   <td colspan=3><a name="RawEncoderErrorLimits"><b>Raw Encoder Error Limits</b></a>: Added <a href="https://github.com/HaddingtonDynamics/Dexter/commit/8efff90396c50680f40f52a196b21d2c92cc0088">2019/04/22</a> The largest error allowed between the raw encoder reading of position and the expected position for each joint. Part of the status monitor. Will inject a returned error status on the next command and write out and entry with more information to /srv/samba/share/errors.log. Default values set in Defaults.make_ins. </td>
   </tr>
 <tr>
   <td>52,&nbsp;RawEncoderErrorLimits</td>
   <td>deg</td>
   <td>0 | 3600 | 1296000 arcsec (5 integers)</td>
   </tr>
 <tr>
   <td colspan=3><a name="RawVelocityLimits"><b>Raw Velocity Limits</b></a>: Added <a href="https://github.com/HaddingtonDynamics/Dexter/commit/8efff90396c50680f40f52a196b21d2c92cc0088">2019/04/22</a> The largest velocity allowed between subsequent readings of the raw encoder position. Part of the status monitor. Will inject a returned error status on the next command and write out and entry with more information to /srv/samba/share/errors.log <b>and will switch to open loop mode</b>. Default values set in Defaults.make_ins.</td>
   </tr>
 <tr>
   <td>53,&nbsp;RawVelocityLimits</td>
   <td>0 | 100 | deg/s</td>
   <td>0 | 360000 | 1296000 | arcsec/s (5 integers)</td>
   </tr>

 <tr>
   <td colspan=3><a name="MaxTorque"><b>Max Torque</b></a>: Not yet released, don't use. Maximum Torque before each joint stops trying. 
      </td>
   </tr>
 <tr>
   <td>54,&nbsp;MaxTorque</td>
   <td><!--- min | initial | max in DDE units --></td>
   <td><!--- min | initial | max in Firmware units --></td>
   </tr>
 <tr>
   <td colspan=3><a name="AxisCal"><b>AxisCal</b></a>: Added <a href="https://github.com/HaddingtonDynamics/Dexter/commit/4c506d60a42ed31d4acc35f74e6f6262ac6a78e5#diff-a940a548ba41bc988f99d51fd02f21de">2019/10/10 on StepAngles branch</a>. Set JointsCal and ANGLE_END_RATIO via SetParameter instead of via AxisCal.txt. This allows it to be in the Defaults.make_ins file which then holds all the unique data about each robot. Values are the number of steps per joint rotation and can be calculated as gear ratio times the steps per revolution times the microstepping mode.<PRE>
;S, AxisCal, -332800, -332800, -332800, -36000, -36000 ;Defaults for Dexter HD
S, AxisCal, -332800, -332800, -332800, -86400, -86400 ;Defaults for Dexter HDI
</PRE>
      </td>
   </tr>
 <tr>
   <td>55,&nbsp;AxisCal</td>
   <td colspan=2>Gear_Ratio * Motor_Steps_per_Revolution * Microstepping</td>
   </tr>
 <tr>
   <td colspan=3><a name="Interpolation"><b>Interpolation</b></a>: Added <a href="https://github.com/HaddingtonDynamics/Dexter/commit/4c506d60a42ed31d4acc35f74e6f6262ac6a78e5#diff-a940a548ba41bc988f99d51fd02f21de">2019/10/10 on StepAngles branch</a>. Set interpolation factors for each joint. This defaults to `1 1 1 16 16` for the Dexter 1 and HD and is changed to all 1's for the HDI. Primarily to maintain backwards compatibility; the DexRun.C code will continue to work on the prior version, but can be set to work with the HDI by using this parameter.
      </td>
   </tr>
 <tr>
   <td>56,&nbsp;Interpolation</td>
   <td>1 | 1 | 16 sub-ustep per ustep</td>
   <td>1 | 1 | 16 sub-ustep per ustep</td>
   </tr>

 <tr>
   <td colspan=3><!--- <a name=""><b> </b></a>: Added <a href="">2019/mm/dd --></a> 
      </td>
   </tr>
 <tr>
   <td><!--- #,&nbsp;  --></td>
   <td><!--- min | initial | max in DDE units --></td>
   <td><!--- min | initial | max in Firmware units --></td>
   </tr>

 </table>


<a name="1">Note 1</a> nbits are a value added to a register every clock cycle for a 128Khz clock such that once it overflows, an action will be taken. Converted automatically by DDE [in the socket communications code](https://github.com/cfry/dde/blob/33f06f03167a8c7b443a42ad0b3560df39ed7a17/core/socket.js#L98) for the "MaxSpeed", "StartSpeed", and "Acceleration" parameters. The global constant "_nbits_cf" is 7754.73550222 and the values given are multiplied by that constant. This converts degrees / second (or degrees / second / second in the case of Acceleration) into nbits. Setting these values too low with effectively lockup the FPGA as it will take far too long to complete a movement.

<a name="2">Note 2</a> Frictions can be fractional, but are limited to a range of +-(256 + 1/256). E.g. 1.1 becomes 1 plus a fractional value of 25/256