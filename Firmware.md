# [Dexter Firmware](../blob/master/Firmware)

_Note: To update the onboard firmware, follow the directions in the [/Firmware/README.md](../blob/master/Firmware#readme)_

Dexter runs "Ubuntu 16.04 LTS" from the [micro SD card](SD-Card-Image) on it's [MicroZed](MicroZed) board and automatically starts a program called [DexRun.c](../blob/master/Firmware/DexRun.c) (used to be iotest.c) in the /srv/samba/share folder.

DexRun.c is the interface between the ethernet port and the [FPGA](Gateware) which controls Dexters hardware (motors, encoders, etc...). It opens a socket and accepts [commands](Command-oplet-instruction) from [DDE](DDE) and sends data back. See [DexRun-DDE-communications](DexRun-DDE-communications) for more detail.

DexRun.c also provides a few local functions. For example, onboard motion planning is being developed. 

Other files in the /srv/samba/share folder include
- `autoexec.make_ins` which calls `Defaults.make_ins` which sets the default values for several items in the robot. e.g. "LengthLength" which are the length of each segment (or link) of the arm. This file can be read and parsed to ensure the control software (such as DDE) is aware of the robots settings. These .make_ins files contain [oplets](Command-oplet-instruction) in the same format used to control Dexter, but without the job, instruction, and start and end time values. They /start/ with the oplet letter. Other make_ins files contain sequences of instructions to set modes, re-calibrate, etc...
- `RunDexRun` is a bash script that checks versions and writes them to Defaults.make_ins, updates the DexRun program when it finds a new DexRun.c file, and optionally starts jobs in the onboard [DDE Job Engine](DDE#job-engine-on-dexter)
- `autoexec.jobs` if present, lists [Job Engine](DDE#job-engine-on-dexter) jobs to be started as soon as the robot boots.
- `AdcCenters.txt` contains the X and Y locations of the center of each [encoder](Encoders) "eye". 
- `AxisCal.txt` is calculated from from Gear Ratio and Microstepping as follows (in DDE)
````
//Input:
var diff_pulley_small_num_teeth = 16
var diff_pulley_large_num_teeth = 90
var micro_step = 16
var motor_steps = 400
var gear_ratios = [
	52,
    52,
    52,
    diff_pulley_large_num_teeth / diff_pulley_small_num_teeth,
    diff_pulley_large_num_teeth / diff_pulley_small_num_teeth
]
//Output:
var AxisCal_string = ""
for(let i = 0; i < 5; i++){
	AxisCal_string += -(gear_ratios[i]*micro_step*motor_steps) / (3600*360) + "\n"
}
AxisCal_string += -Math.round(gear_ratios[3] / gear_ratios[1] * Math.pow(2, 24))
````

## Other Firmware
It is very possible to install and run other programs on Dexter. For example, you can install [Node.js and run a web server / socket interface](nodejs-webserver) in order to access the robot without any installed software on your PC. 

[ROS Kenetic](http://wiki.ros.org/kinetic/Installation/Ubuntu) can probably be installed. Or the node.js httpd web server / port gateway can be updated to [provide ROS messaging](https://github.com/RethinkRobotics-opensource/rosnodejs/issues/131) as well. 