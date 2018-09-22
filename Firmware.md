# [Dexter Firmware](../blob/master/Firmware)

Dexter runs "Ubuntu 12.04 LTS" from the [micro SD card](SD-Card-Image) on it's [MicroZed](MicroZed) board and automatically starts a program called [DexRun.c](../blob/master/Firmware/DexRun.c) (used to be iotest.c)

DexRun.c is the interface between the ethernet port and the [FPGA](Gateware) which controls Dexters hardware (motors, encoders, etc...). It opens a socket and accept commands from [DDE](DDE) and sends data back. See [DexRun-DDE-communications](DexRun-DDE-communications) for more detail.

DexRun.c also provides a few local functions. For example, onboard motion planning is being developed. 

To update the onboard firmware, follow the directions at:
https://github.com/HaddingtonDynamics/Dexter/blob/master/Firmware/README.md

Although the version of the microzed board doesn't have a screen, your can [SSH into Dexter](SSH-into-Dexter) from any network connected PC. 

Once you are at the command prompt in Dexter, you can verify that DexRun is active with `pgrep DexRun` which will return the process id number. If nothing is returned DexRun isn't active. To stop DexRun (e.g. after compiling a new version) the quickest way is `pkill DexRun`. To start DexRun, the typically options are `./DexRun 1 3 0 &` after you `cd /srv/samba/share` to get to the directory where the program is kept. The `&` at the end allows it to run in the background, so your session is still available to take other commands. This can be a very useful way to run the program as it will still display messages about some commands. These debug messages aren't typically returned for commands which are time sensitive because they can cause serious slowdowns when DexRun does not have access to a session for those messages to be displayed into.

It is very possible to install and run other programs on Dexter. For example, you can install [Node.js and run a web server / socket interface](nodejs-webserver) in order to access the robot without any installed software on your PC.