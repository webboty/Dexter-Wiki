The list of commands or "oplets" that Dexter knows are defined by the HashInputCMD in DexRun.c currently here:
https://github.com/HaddingtonDynamics/Dexter/blob/master/Firmware/DexRun.c#L2713
and are described by Dexter.instruction_type_to_function_name_map in DDE here:
https://github.com/cfry/dde/blob/master/robot.js#L1513

This wiki page attempts to expand on those source documents to explain the instructions.

Code | DDE name | Description
--- | --- | ---
a|"move_all_joints"|
b|"move_to"| obsolete
B|"set_boundries"|10 args: j1BoundryHigh, j1Boundrylow,  j2BoundryHigh, j2Boundrylow, j3BoundryHigh, j3Boundrylow, j4BoundryHigh, j4Boundrylow, j5BoundryHigh, j5Boundrylow,
c|"capture_ad"| 
d|"dma_read"|
e|"cause_dexter_error"| //fry
E|"empty_instruction_queue_immediately"|new Sept 1, 2016
F|"empty_instruction_queue"|new Sept 1, 2016
f|"find_home"|
G|"get_robot_status_immediately"|new Sept 1, 2016
g|"get_robot_status"|   //fry
h|"get_robot_status_heartbeat"| //fry
i|"capture_points"|
l|"load_tables"|
m|"record_movement"|
n|"find_index"|
o|"replay_movement"|
P|"pid_move_all_joints"|
p|"find_home_rep"|
R|"move_all_joints_relative"|
s|"slow_move"|
S|"set_parameter"|
t|"dma_write"|
w|"write"|
W|"write_to_robot"|
x|"exit"|
z|"sleep"|
