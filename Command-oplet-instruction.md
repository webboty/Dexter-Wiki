The list of commands or "oplets" that Dexter knows are defined by the [HashInputCMD function](https://github.com/HaddingtonDynamics/Dexter/search?utf8=%E2%9C%93&q=HashInputCMD+filename%3ADexRun.c&type=) in the [DexRun.c](../blob/master/Firmware/DexRun.c) [Firmware](firmware) and are described by Dexter.instruction_type_to_function_name_map in DDE. (that is currently here:
https://github.com/cfry/dde/blob/master/robot.js#L1513  )

This wiki page attempts to expand on those source documents to explain the instructions. **Q'd** instructions are stored in an internal movement FIFO by DexRun.c and executed sequentially. Other instructions are executed as soon as they are recieved. 

&nbsp; | DDE name | DexRun | Q'd | Description
--- | --- | --- | --- | ---
a|"move_all_joints"|MOVEALL_CMD|x|
b|"move_to"||x| obsolete
B|"set_boundries"|SET_ALL_BOUNDRY||10 args: j1BoundryHigh, j1Boundrylow,  j2BoundryHigh, j2Boundrylow, j3BoundryHigh, j3Boundrylow, j4BoundryHigh, j4Boundrylow, j5BoundryHigh, j5Boundrylow,
c|"capture_ad"|CAPTURE_AD_CMD||
d|"dma_read"|DMAREAD_CMD||Writes the FPGA DMA data into the specified file. 3 args: Address, Length, Filename.
e|"cause_dexter_error"|n/a||Used only the Dexter
E|"empty_instruction_ queue_immediately"|n/a||new Sept 1, 2016. Apparently never implemented in firmware?
F|"empty_instruction_ queue"|HEART_BEAT|x|new Sept 1, 2016 does `wait_fifo_flush()` first. Was SET_FORCE_MOVE_POINT. This instruction will not return a status until it actually executes. Instructions before this one will be executed first.
f|"find_home"|FIND_HOME_CMD||
G|"get_robot_status_ immediately"|HEART_BEAT|| (Same as 'g'?) new Sept 1, 2016
g|"get_robot_status"|SEND_HEARTBEAT||  Returns the current [status](status-data) of the robot.//fry
h|"get_robot_status_ heartbeat"|HEART_BEAT|| obsolete //fry
i|"capture_points"|CAPTURE_POINTS_CMD|| Enters a sub mode where points are captured to a file on Dexter. 1 arg: Filename. Expects an input stream of commands from stdin via getchar(). No timeout.
l|"load_tables"|LOAD_TABLES||
M|n/a|MOVETO_CMD|x|In development
m|"record_movement"|RECORD_MOVEMENT||
n|"find_index"|FIND_INDEX_CMD||
o|"replay_movement"|REPLAY_MOVEMENT||
P|"pid_move_all_joints"|PID_FINEMOVE|?|
p|"find_home_rep"|FIND_HOME_REP_CMD||
R|"move_all_joints_ relative"|MOVEALL_RELATIVE||obsolete
r|n/a|READ_CMD|||Implemented in DexRun.c April2018
s|"slow_move"|SLOWMOVE_CMD||
S|"set_parameter"|SET_PARAM||
T|n/a|MOVETOSTRAIGHT_CMD||In development
t|"dma_write"|DMAWRITE_CMD||
w|"write"|WRITE_CMD||Does `wait_fifo_flush()` first.
W|"[write_to_robot](write-to-robot)"|WRITE_TO_ROBOT||Send files to the Dexter file system. Implemented in DexRun.c April2018
x|"exit"|EXIT_CMD||
z|"sleep"|SLEEP_CMD||
