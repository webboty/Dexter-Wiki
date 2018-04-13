# [Dexter Firmware](../blob/master/Firmware/README.md)

Dexter runs Linux on it's [microzed](http://zedboard.org/product/microzed) board and automatically starts a program called DexRun.c (used to be iotest.c)

DexRun.c is the interface between the ethernet port and the [FPGA](Gateware) which controls Dexters hardware (motors, encoders, etc...). It opens a socket and accept commands from [DDE](DDE) and sends data back. See [DexRun-DDE-communications](DexRun-DDE-communications) It also provides a few local low level functions. 

