# [Dexter Firmware](../blob/master/Firmware)

Dexter runs "Ubuntu 12.04 LTS" from the [micro SD card](SD-Card-Image) on it's [microzed](http://zedboard.org/product/microzed) board and automatically starts a program called [DexRun.c](../blob/master/Firmware/DexRun.c) (used to be iotest.c)

DexRun.c is the interface between the ethernet port and the [FPGA](Gateware) which controls Dexters hardware (motors, encoders, etc...). It opens a socket and accept commands from [DDE](DDE) and sends data back. See [DexRun-DDE-communications](DexRun-DDE-communications) for more detail.

DexRun.c also provides a few local functions. For example, onboard motion planning is being developed. 

To update the onboard firmware, follow the directions at:
https://github.com/HaddingtonDynamics/Dexter/blob/master/Firmware/README.md