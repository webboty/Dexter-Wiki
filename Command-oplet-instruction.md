The list of commands or "oplets" that Dexter knows are defined by the [HashInputCMD function](https://github.com/HaddingtonDynamics/Dexter/search?utf8=%E2%9C%93&q=HashInputCMD+filename%3ADexRun.c&type=) in the [DexRun.c](../blob/master/Firmware/DexRun.c) [Firmware](firmware) and are described by [Dexter.instruction_type_to_function_name_map](https://github.com/cfry/dde/search?q=Dexter.instruction_type_to_function_name_map+filename%3Arobot.js&unscoped_q=Dexter.instruction_type_to_function_name_map+filename%3Arobot.js) in DDE. 

This wiki page attempts to expand on those source documents to explain the instructions. In addition to this list, the ['w' Write FPGA](oplet-write), and ['S' SetParameter](set-parameter-oplet) oplets also have lists of addresses and sub-commands.


**Queued** instructions are stored in the FPGA in an internal movement FIFO and executed sequentially. Other instructions are executed as soon as they are recieved. When the queue is full, that last movement command submitted will not return a status, and DexRun will not respond to _any_ commands until a movement is complete, opening a space on the queue. 

Note that non-movement instructions may not have any effect until a move instruction is sent. This helps to coordinate movement with things like end effector actuation. However, it can be confusing if you just want to e.g. turn a laser end effector on and off and nothing happens. Just send a movement command with the current positions to enable your other commands.

### Wait for Movement
To ensure that a movement has completed, use the <a href="#f">'F' oplet</a?, as it will not return a status until the que is empty. Note that the robot may still be moving when the que is empty, because it can take some time for the movement to finish. To wait for the movement to be completed, send a second movement command, moving to the same location before the 'F'. The 2nd movement command will stay in the que until the 1st movement command is completed, and will take no time to complete itself, but will then release the 'F' oplet to return a status.

See [DDE](DDE) documentation for the use of the DDE version of each command. E.g. for `move_to` use `Dexter.move_to (...)`

These oplets are sent to Dexter via a raw socket connection on port 50000 in a format fully documented in [DexRun-DDE-communications](DexRun-DDE-communications). Basically, a job number, instruction number, start POSIX time, end time (unknown) and the oplet and it's parameters are sent followed by a ';' to close the line. e.g. for the 'g' (get_robot_status) oplet you might send `1 2 1528438131 undefined g ;` where this is the first job, the 2nd instruction in that job, it was sent at 06/08/2018 @ 6:08am (UTC), the end time is not known (it will be set by Dexter in the returned status). Note: All these values must be present, but are not checked. e.g. `1 1 1 1 g ;` works just fine; the return status just won't tell you what instruction # it's for, or when the command was started. 

&nbsp; | DDE name | DexRun | Description
--- | --- | --- | ---
<a name="a">**a**</a>|"move_all_joints"|MOVEALL_CMD|Queued. Arguments: 5 to 7 goal joint angles. The goal can not be changed during the move. Trapezoidal speed ramping is used with coordination so the first 5 joints arrive at their goals at the same time. Acceleration, MaxSpeed, and StartSpeed can be set via the ["S" SetParameter oplet](set-parameter-oplet). Does not require calibration, works even in Open Loop mode (w/ lower accuracy). <br>Since [2018.11.07](https://github.com/HaddingtonDynamics/Dexter/commit/c70abf71bca34aadf0850fbdd6b9b2bac969d221) accepts joint 6 and 7 positions.
<a name="b">**b**</a>|"move_to"|n/a|Queued. Use "Dexter.move_to" in DDE. In DexRun, this oplet is obsolete. See "M"
<a name="B">B</a>|"set_boundries"|SET_ALL_BOUNDRY|10 args: j1BoundryHigh, j1Boundrylow,  j2BoundryHigh, j2Boundrylow, j3BoundryHigh, j3Boundrylow, j4BoundryHigh, j4Boundrylow, j5BoundryHigh, j5Boundrylow. Set individually with ["S" oplet subcommands](set-parameter-oplet)
<a name="c">c</a>|"capture_ad"|CAPTURE_AD_CMD|
<a name="d">d</a>|"dma_read"|DMAREAD_CMD|Writes the FPGA DMA data into the specified file. 3 args: Address, Length, Filename.
<a name="e">e</a>|"cause_dexter_error"|n/a|Used only in DDE
<a name="E">E</a>|"empty_instruction_ queue_immediately"<br>Since 2016.09.01|n/a|Apparently never implemented in firmware?
<a name="F">**F**</a>|"empty_instruction_ queue"|HEART_BEAT<br>Since 2016.09.01|Queued. Does `wait_fifo_flush()` first. Was SET_FORCE_MOVE_POINT. This instruction will not return a status until it actually executes. Instructions before this one will be executed first. Very useful to synchronize robot motion with control software. To <a href="wait_for_movement">wait until a move is complete</a> send TWO moment commands to the same location before the 'F'.
<a name="f">f</a>|"find_home"|FIND_HOME_CMD|_Depreciated_ Use job engine.|
<a name="G">G</a>|"get_robot_status_ immediately"|Since 2016.09.01 HEART_BEAT| (Same as 'g'?) 
<a name="g">g</a>|"[get_robot_status](status-data)"|SEND_HEARTBEAT|Returns the current [status](status-data) of the robot.//fry
<a name="h">h</a>|"get_robot_status_ heartbeat"|HEART_BEAT|obsolete //fry
<a name="i">i</a>|"capture_points"|CAPTURE_POINTS_CMD| Enters a sub mode where points are captured to a file on Dexter. 1 arg: Filename. Expects an input stream of commands from stdin via getchar(). No timeout.
<a name="l">l</a>|"load_tables"|LOAD_TABLES|Writes [encoder](Encoders) CalTables from [FPGA](Gateware) to /srv/samba/share/HiMem.dta
<a name="M">**M**</a>|n/a<sup>1</sup>|MOVETO_CMD<br>[Since 2018.10.9](https://github.com/HaddingtonDynamics/Dexter/commit/78bc04dfae5166d8889e1fba36540a3de3ea836b#diff-691272021fae98368efb598f8e089c16)|Queued Onboard [kinematics](Kinematics) move to XYZ (integer microns), with end effector in XYZ direction (unit vector), using configuration (booleans). For example to move to [0,0.5,1.0] (in meters) with the end effector pointed straight down and right_arm, elbow_up, and wrist_out:<tt>1 1 1 1 M 0 500000 100000 0 0 -1 1 1 1</tt>. See 'T'
<a name="m">m</a>|"record_movement"|RECORD_MOVEMENT||
<a name="n">n</a>|"find_index"|FIND_INDEX_CMD||
<a name="o">o</a>|"replay_movement"|REPLAY_MOVEMENT||
<a name="P">P</a>|"pid_move_all_joints"|PID_FINEMOVE|Chases a goal position on each joint using the PID control system which must be enabled. Requires [calibration](Encoder-calibration). The goal can change in the middle of the move. Movement is not coordinated so each joint may arrive at the goal at a different time. The standard trapezoidal speed ramping is not used. See "a". Modified 201909 to optionally accept joint 6/7 angles.|
<a name="p">p</a>|"find_home_rep"|FIND_HOME_REP_CMD|_Depreciated_ Use job engine.|
<a name="R">**R**</a>|"move_all_joints_ relative"|MOVEALL_RELATIVE|Queued. Like 'a' but the joint is moved relative to it's prior position. E.g. `R 0 0 -36000 0 0;` will move joint 3 ccw 10 degrees.
<a name="r">r</a>|"[read_from_robot](read-from-robot)"<br>[Since v2.3.16](https://github.com/cfry/dde/releases/tag/untagged-5d86b61c13b61d266905)|READ_CMD<br>[Since 2018.07.26](https://github.com/HaddingtonDynamics/Dexter/commit/243ac0fa3c995effd9c75731d3a9c7ecb70cc73e)|Reads files or string data from the robot. Also reads and prints locally values from the memory mapped FPGA interface.
<a name="s">s</a>|"slow_move"|SLOWMOVE_CMD<br>[Since 2018.10.9](https://github.com/HaddingtonDynamics/Dexter/commit/78bc04dfae5166d8889e1fba36540a3de3ea836b#diff-691272021fae98368efb598f8e089c16)||
<a name="S">S</a>|"[set_parameter](set-parameter-oplet)"|SET_PARAM|Sets many different values
<a name="T">**T**</a>|n/a<sup>1</sup>|MOVETOSTRAIGHT_CMD|Onboard [kinematics](Kinematics) move in a straight line from prior 'M' oplet position to new XYZ position, direction, and configuration. 
<a name="t">t</a>|"dma_write"|DMAWRITE_CMD||
<a name="w">w</a>|"[write](oplet-write)"|WRITE_CMD|Writes to the [FPGA / Gateware](Gateware). Does `wait_fifo_flush()` first.
<a name="W">W</a>|"[write_to_robot](write-to-robot)"<br>Since [v2.3.15-16](https://github.com/cfry/dde/releases/tag/untagged-5d86b61c13b61d266905)|WRITE_TO_ROBOT<br>[Since 20180418](https://github.com/HaddingtonDynamics/Dexter/commit/ef39cfe6effd36faeb9bd96b21a11a3dcd6b84ef)|Send files to the Dexter file system. 
<a name="X">x</a>|"exit"|EXIT_CMD||
<a name="z">**z**</a>|"sleep"|SLEEP_CMD|Queued. Pause the robot for the specified uSeconds|

1. The 'M' and 'T' oplets are not used in DDE because DDE does it's own [kinematics](Kinematics). 