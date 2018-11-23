The list of commands or "oplets" that Dexter knows are defined by the [HashInputCMD function](https://github.com/HaddingtonDynamics/Dexter/search?utf8=%E2%9C%93&q=HashInputCMD+filename%3ADexRun.c&type=) in the [DexRun.c](../blob/master/Firmware/DexRun.c) [Firmware](firmware) and are described by Dexter.instruction_type_to_function_name_map in DDE. (that is currently here:
https://github.com/cfry/dde/blob/master/robot.js#L1513  )

This wiki page attempts to expand on those source documents to explain the instructions. **Q'd** instructions are stored in an internal movement FIFO by DexRun.c and executed sequentially. Other instructions are executed as soon as they are recieved. 

&nbsp; | DDE name | DexRun | Q'd | Description
--- | --- | --- | --- | ---
a|"move_all_joints"|MOVEALL_CMD|x|5 args: j1deg, j2deg, j3deg, j4deg, j5deg
b|"move_to"||x| obsolete
B|"set_boundries"|SET_ALL_BOUNDRY||10 args: j1BoundryHigh, j1Boundrylow,  j2BoundryHigh, j2Boundrylow, j3BoundryHigh, j3Boundrylow, j4BoundryHigh, j4Boundrylow, j5BoundryHigh, j5Boundrylow,
c|"capture_ad"|CAPTURE_AD_CMD||
d|"dma_read"|DMAREAD_CMD||Writes the FPGA DMA data into the specified file. 3 args: Address, Length, Filename.
e|"cause_dexter_error"|n/a||Used only in DDE
E|"empty_instruction_ queue_immediately"|n/a||new Sept 1, 2016. Apparently never implemented in firmware?
F|"empty_instruction_ queue"|HEART_BEAT|x|new Sept 1, 2016 does `wait_fifo_flush()` first. Was SET_FORCE_MOVE_POINT. This instruction will not return a status until it actually executes. Instructions before this one will be executed first.
f|"find_home"|FIND_HOME_CMD||
G|"get_robot_status_ immediately"|HEART_BEAT|| (Same as 'g'?) new Sept 1, 2016
g|"[get_robot_status](status-data)"|SEND_HEARTBEAT||  Returns the current [status](status-data) of the robot.//fry
h|"get_robot_status_ heartbeat"|HEART_BEAT|| obsolete //fry
i|"capture_points"|CAPTURE_POINTS_CMD|| Enters a sub mode where points are captured to a file on Dexter. 1 arg: Filename. Expects an input stream of commands from stdin via getchar(). No timeout.
l|"load_tables"|LOAD_TABLES||Writes [encoder](Encoders) CalTables from [FPGA](Gateware) to /srv/samba/share/HiMem.dta
M|n/a<sup>1</sup>|MOVETO_CMD|x|Move to XYZ (in microns), with end effector pointed in XYZ direction (unit vector), using configuration (booleans), since [Oct 9](https://github.com/HaddingtonDynamics/Dexter/commit/78bc04dfae5166d8889e1fba36540a3de3ea836b#diff-691272021fae98368efb598f8e089c16). For example:<tt>1 1 1 1 M 0 500000 100000 0 0 -1 1 1 1</tt>
m|"record_movement"|RECORD_MOVEMENT||
n|"find_index"|FIND_INDEX_CMD||
o|"replay_movement"|REPLAY_MOVEMENT||
P|"pid_move_all_joints"|PID_FINEMOVE|?|
p|"find_home_rep"|FIND_HOME_REP_CMD||
R|"move_all_joints_ relative"|MOVEALL_RELATIVE||obsolete
r|[From v2.3.16](https://github.com/cfry/dde/releases/tag/untagged-5d86b61c13b61d266905)<BR>"[read_from_robot](read-from-robot)"|[TDint branch 20180726](https://github.com/HaddingtonDynamics/Dexter/commit/243ac0fa3c995effd9c75731d3a9c7ecb70cc73e)<BR>READ_CMD||Reads files or string data from the robot.
s|"slow_move"|SLOWMOVE_CMD||
S|"[set_parameter](set-parameter-oplet)"|SET_PARAM||Sets many different values
T|n/a<sup>1</sup>|MOVETOSTRAIGHT_CMD||Move in a straight line from prior 'M' oplet position to new XYZ position, direction, and configuration. since [Oct 9](https://github.com/HaddingtonDynamics/Dexter/commit/78bc04dfae5166d8889e1fba36540a3de3ea836b#diff-691272021fae98368efb598f8e089c16)
t|"dma_write"|DMAWRITE_CMD||
w|"[write](oplet-write)"|WRITE_CMD||Writes to the [FPGA / Gateware](Gateware). Does `wait_fifo_flush()` first.
W|[From v2.3.15-16](https://github.com/cfry/dde/releases/tag/untagged-5d86b61c13b61d266905)<BR>"[write_to_robot](write-to-robot)"|[From 20180418](https://github.com/HaddingtonDynamics/Dexter/commit/ef39cfe6effd36faeb9bd96b21a11a3dcd6b84ef)<BR>WRITE_TO_ROBOT||Send files to the Dexter file system. 
x|"exit"|EXIT_CMD||
z|"sleep"|SLEEP_CMD||

1. The 'M' and 'T' oplets are not used in DDE because DDE does it's own kinematics. 