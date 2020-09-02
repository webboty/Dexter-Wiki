The Write (`w`) [oplet](Command-oplet-instruction) writes low level values to the [FPGA / Gateware](Gateware) and in some cases also sets variables in the [firmware](Firmware). This is the lower case 'w'; the upper case 'W' is [Write to Robot](write_to_robot). The second parameter (the value) is written to the address of the FPGA specified by the first parameter.  e.g. `w 42 64` writes the value 64 into the address 42 in the FPGA. Some addresses are also "tracked" by the C firmware (DexRun) for example, `w 5 6516872` writes a new value to FPGA register #5, ACCELERATION_MAXSPEED but the maxSpeed and coupledAcceleration variables in DexRun will also be updated. For higher level settings in the [Firmware](Firmware), see the ['S' oplet](set-parameter-oplet)

_Note: This represents the "virtual" address list as used by the 'w' command. The actual FPGA addresses have been changed since [2018.05.29](https://github.com/HaddingtonDynamics/Dexter/commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) to use a "keyhole window" to reduce the number of mapped addresses used by the FPGA. In the keyhole system, one address in a block will select what the next address actually changes. DexRun.c still interprets 'w' commands with the original addresses when the function has not been converted to a keyhole system via the array "<a href="https://github.com/HaddingtonDynamics/Dexter/search?q=OldMemMapInderection&unscoped_q=OldMemMapInderection">OldMemMapInderection</a>". Items which have been struck out below are not available via 'w' and must be set via other commands which are linked here._

Addr | Name               | Description
---- | ------------------ | ----------------------------
 <a name="0">0</a> | BASE_POSITION_AT     | <strike>J1 Motor position index</strike> 
 <a name="1">1</a> | END_POSITION_AT      | <strike>J2 ...where the joint has been</strike>
 <a name="2">2</a> | PIVOT_POSITION_AT    | <strike>J3 ...commanded to go to</strike>
 <a name="3">3</a> | ANGLE_POSITION_AT    | <strike>J4</strike> ...Use the "a" [oplet](Command-oplet-instruction#a)
 <a name="4">4</a> | ROT_POSITION_AT      | <strike>J5</strike>
 <a name="5">5</a> | ACCELERATION_MAXSPEED| Acceleration [20:31] and max speed [0:19] (shared register). Use the [oplet S AngularAcceleration](set-parameter-oplet#AngularAcceleration) and Use [oplet S AngularSpeed](set-parameter-oplet#AngularSpeed)
 <a name="6">6</a> | BASE_SIN_CENTER      | <strike>J1 DC bias parameters</strike> 
 <a name="7">7</a> | BASE_COS_CENTER      | <strike>J1</strike> ...Use the "W" or [write-to-robot](write-to-robot) [oplet](Command-oplet-instruction) 
 <a name="8">8</a> | END_SIN_CENTER       | <strike>J2</strike> to write the [AdcCenters.txt](Encoders#adc-centers) file.
 <a name="9">9</a> | END_COS_CENTER       | <strike>J2</strike>
<a name="10">10</a> | PIVOT_SIN_CENTER    | <strike>J3</strike>
<a name="11">11</a> | PIVOT_COS_CENTER    | <strike>J3</strike>
<a name="12">12</a> | ANGLE_SIN_CENTER    | <strike>J4</strike>
<a name="13">13</a> | ANGLE_COS_CENTER    | <strike>J4</strike>
<a name="14">14</a> | ROT_SIN_CENTER      | <strike>J5</strike>
<a name="15">15</a> | ROT_COS_CENTER      | <strike>J5</strike>
<a name="16">16</a> | PID_DELTATNOT       | <strike>delta t not. Inverse of Delta t. 100,000KHz</strike>
<a name="17">17</a> | PID_DELTAT          | <strike>delta t. float * 0.0001 seconds</strike>
<a name="18">18</a> | PID_D               | <strike>PID Differential Term</strike>
<a name="19">19</a> | PID_I               | <strike>PID Integral Term</strike>
<a name="20">20</a> | PID_P               | PID Proportional Term <A HREF="https://github.com/HaddingtonDynamics/Dexter/issues/33#issuecomment-427921288">in Floating point</A>. Use [SetParameter oplet S J#PID_P](set-parameter-oplet#JointPID)
<a name="21">21</a> | PID_ADDRESS         | Sets the joint number for the above PID_ settings to be written into. <strike>Multiple sets are available for PID_SCHEDULE_INDEX</strike>
<a name="22">22</a> | BOUNDRY_BASE        | <strike>Maximum position past which the force position override will be reduced for each joint.</strike> 
<a name="23">23</a> | BOUNDRY_END         | <strike>Injects a negative force opposing the external force to limit the amount the joint will move to avoid external forces.</strike>
<a name="24">24</a> | BOUNDRY_PIVOT       | <strike>The low boundary is [0:15] and the high boundary is [16:31]</strike>
<a name="25">25</a> | BOUNDRY_ANGLE       | <strike>Changed along with TDInt branch via a keyhole to make low and high boundary +-18 bits (19 bits total) each.</strike> 
<a name="26">26</a> | BOUNDRY_ROT         | <strike>"</strike> Use [B oplet](Command-oplet-instruction#B) to set all joint boundaries or [SetParameter oplet: S J#Boundry\[High\|Low\]](set-parameter-oplet#JointBoundaries)
<a name="27">27</a> | SPEED_FACTORA       | Multiplier (coefficient / master gain) for the force calculator. Allows quick adjustments.
<a name="28">28</a> | </strike>SPEED_FACTORB</strike> BETA_XYZ | Was SPEED_FACTORB, now BETA_XYZ: Reactivity of the boundary for Joints 1-3. Resistance to movement will be like a marshmallow vs brick wall. See also <a href="#52">DIFF_FORCE_BETA</A>
<a name="29">29</a> | FRICTION_BASE       | <strike>Coefficient to scale the position error. Can be negative. </strike> 
<a name="30">30</a> | FRICTION_END        | <strike>Feels like friction. Used in most modes, very important in follow mode.</strike>
<a name="31">31</a> | FRICTION_PIVOT      | <strike>"</strike> Use [SetParameter oplet: S J#Friction](set-parameter-oplet#JointFrictions) 
<a name="32">32</a> | FRICTION_ANGLE      | <strike>"</strike>
<a name="33">33</a> | FRICTION_ROT        | <strike>"</strike>
<a name="34">34</a> | MOVE_TRHESHOLD      | Maximum force before joint 1,2,3 position starts changing. See follow mode.
<a name="35">35</a> | F_FACTOR            | Like SPEED_FACTORA, but for PID controller. Affects all joints at once. 
<a name="36">36</a> | MAX_ERROR           | Speed limit for PID controller. Clips the velocity of the step signals going to the motor. eg. `w, 36, (2000 ^ (PID_max_speed_degrees * 312))`
<a name="37">37</a> | FORCE_BIAS_BASE     | <strike>Adds to the force (position error) on each joint. Useful for removing the force of gravity.</strike> 
<a name="38">38</a> | FORCE_BIAS_END      | <strike>Also used to provide haptic force feedback in teleoperation or VR systems.</strike>
<a name="39">39</a> | FORCE_BIAS_PIVOT    | <strike>"</strike> Use [SetParameter oplet: S J#Force](set-parameter-oplet#JointForces) 
<a name="40">40</a> | FORCE_BIAS_ANGLE    | <strike>"</strike>
<a name="41">41</a> | FORCE_BIAS_ROT      | <strike>"</strike>
<a name="42">42</a> | COMMAND_REG         | control state register. Bits:<BR>0 CMD_CAPCAL_BASE 1 <BR>1 CMD_CAPCAL_END 2 <BR>2 CMD_CAPCAL_PIVOT 4 <BR>3 CMD_MOVEEN 8 <BR>4 CMD_MOVEGO 16 <BR>5 CMD_ENABLE_LOOP 32 <BR>6 CMD_CLEAR_LOOP 64 <BR>7 CMD_CALIBRATE_RUN 128 <BR>8 CMD_RESET_POSITION 256 <BR>9 CMD_RESET_FORCE 512 <BR>10 CMD_CAPCAL_ANGLE 1024 <BR>11 CMD_CAPCAL_ROT 2048 <BR>12 CMD_ANGLE_ENABLE 4096 <BR>13 CMD_ROT_ENABLE 8196 <BR>see <a href="http://JamesNewton.github.io/Dexter.html">cmd calc</a>
<a name="43">43</a> | DMA_CONTROL         | Used to read and write tables and other large data objects. Bits:<BR>0 DMA_WRITE_ENQUEUE 1<BR>1 DMA_WRITE_INITIATE 2<BR>2 DMA_READ_DEQUEUE 4<BR>3 DMA_READ_BLOCK 8<BR>4 DMA_RESET_ALL 16
<a name="44">44</a> |  DMA_WRITE_DATA     |
<a name="45">45</a> |  DMA_WRITE_PARAMS     |
<a name="46">46</a> |  DMA_WRITE_ADDRESS            |
<a name="47">47</a> |  DMA_READ_PARAMS              |
<a name="48">48</a> |  DMA_READ_ADDRESS             |
<a name="49">49</a> |  REC_PLAY_CMD                 | Bits:<BR>0 CMD_RESET_RECORD 1<BR>1 CMD_RECORD 2<BR>2 CMD_RESET_PLAY 4<BR>3 CMD_PLAYBACK 8<BR>4 CMD_RESET_PLAY_POSITION
<a name="50">50</a> |  REC_PLAY_TIMEBASE            |
<a name="51">51</a> |  <strike>DIFF_FORCE_TIMEBASE</strike> (Now MAXSPEED_XYZ)          | <strike>Timebase for PID and Force calculators. Depreciated</strike>
<a name="52">52</a> |  DIFF_FORCE_BETA              | Reactivity of the boundary for the differential. Resistance to movement will be like a marshmallow vs brick wall. See also <a href="#28">XYZ_BETA</A>
<a name="53">53</a> |  DIFF_FORCE_MOVE_THRESHOLD    | Maximum force before joint 4, 5 position starts changing. See follow mode.
<a name="54">54</a> |  DIFF_FORCE_MAX_SPEED         | Maximum speed the force calculator can generate. Like MAX_ERROR (but for force, not PID)
<a name="55">55</a> |  DIFF_FORCE_SPEED_FACTOR_ANGLE| Multiplier (coefficient / master gain) for the force calculator on J4. Like SPEED_FACTORA
<a name="56">56</a> |  DIFF_FORCE_SPEED_FACTOR_ROT  | Multiplier (coefficient / master gain) for the force calculator on J5. Like SPEED_FACTORA
<a name="57">57</a> |  <strike>DIFF_FORCE_ANGLE_COMPENSATE</strike> (Now EXTRUDER_CONTROL)  | <strike>Unused. </strike> Since 2019: Drive the extra stepper driver output labeled "external" on the green board<BR><TT>make_ins("w", 57, 300001)  //Spin backwards at speed 300000</TT><BR><TT>make_ins("w", 57, 300000) //Spin forward at speed 300000</TT><BR><TT>make_ins("w", 57, 0) //Stops</TT><BR>A good range is 50000 (slow) to 500000 (fast). Odd speeds are backwards, even are forwards. It spins until speed is set to 0 so position based control is not supported. Coordinated with other joints, so a move all joints (a) command is required after.
<a name="58">58</a> |  FINE_ADJUST_BASE             | <strike>Position offset to commanded joint angle into the PID. </strike>
<a name="59">59</a> |  FINE_ADJUST_END              | <strike>Has the same effect as offsetting the error signal. </strike>
<a name="60">60</a> |  FINE_ADJUST_PIVOT            | <strike>Allows moves to be made by the PID only vs the coupled acceleration calculator + PID</strike>
<a name="61">61</a> |  FINE_ADJUST_ANGLE            | <strike>"</strike> See PID_MOVE. Use the "P" [oplet](Command-oplet-instruction#P)
<a name="62">62</a> |  FINE_ADJUST_ROT              | <strike>"</strike>
<a name="63">63</a> |  RECORD_LENGTH                | <strike>Length of the recording to be played back</strike>
<a name="64">64</a> |  END_EFFECTOR_IO              | Controls the configuration of the bits that go to the end effector.  See [End-Effectors](End-Effectors), <a href="/HaddingtonDynamics/Dexter/issues/73">Issue 73</a>
<a name="65">65</a> |  SERVO_SETPOINT_A             | PWM value in PWM mode on end effector pins.
<a name="66">66</a> |  SERVO_SETPOINT_B             | " See <a href="/HaddingtonDynamics/Dexter/issues/73">Issue 73</a>
<a name="67">67</a> |  BASE_FORCE_DECAY             | How hard the joint tries to return to the commanded position when the position.
<a name="68">68</a> |  END_FORCE_DECAY              | has been changed to avoid exceeding the maximum force. 
<a name="69">69</a> |  PIVOT_FORCE_DECAY            | Subtracts out the offset introduced by the force controller.
<a name="70">70</a> |  ANGLE_FORCE_DECAY            | See Protect mode
<a name="71">71</a> |  ROTATE_FORCE_DECAY           | "
<a name="72">72</a> |  PID_SCHEDULE_INDEX           | <strike>Gain scheduling. Changes the offset to the PID parameters. Multiplies PID_ADDRESS. To quickly change settings.</strike>
<a name="73">73</a> |  GRIPPER_MOTOR_CONTROL        | PWM output from 7 pin SIL connector on the side (near bottom) of the motor board. Just the first bit is used to enable / disable.
<a name="74">74</a> |  GRIPPER_MOTOR_OFF_WIDTH      | " Use [oplet S GripperMotor](set-parameter-oplet#GripperMotor)
<a name="75">75</a> |  GRIPPER_MOTOR_ON_WIDTH       | " See <a href="/HaddingtonDynamics/Dexter/issues/73">Issue 73</a>
<a name="76">76</a> |  START_SPEED                  | <strike>Starting speed into this move.</strike> Use [oplet S AngularSpeedStartAndEnd](set-parameter-oplet#AngularSpeedStartAndEnd)
<a name="77">77</a> |  ANGLE_END_RATIO              | <strike>Difference in the transmission ratios for the differential joint.</strike>
<a name="78">78</a> | RESET_PID_AND_FLUSH_QUEUE     | bit 0 resets PID_DELTA. Bit 1 is E_STOP? Set to all zero for normal operation.
<a name="79">79</a> | XYZ_FORCE_TIMEBASE            | Timebase Divisor
<a name="80">80</a> | DIFFERENTIAL_FORCE_TIMEBASE   | Timebase Divisor
<a name="81">81</a> | PID_TIMEBASE                  | Timebase Divisor

