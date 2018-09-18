# [Dexter Gateware](https://github.com/HaddingtonDynamics/Dexter/tree/master/Gateware)

"Gateware" is what we call the FPGA programming that makes Dexter stunningly responsive. It allows feedback from encoder to motor in as little as 200 nanoseconds. 

Dexters gateware configures the [Xilinx Inc. XC7Z010-1CLG400C FPGA](http://www.xilinx.com/support/documentation/data_sheets/ds190-Zynq-7000-Overview.pdf) from the image of the [micro SD card](SD-Card-Image) on the [microzed](http://zedboard.org/product/microzed) board. 

The [SD card image](SD-Card-Image) has multiple partitions, one is the actual hard drive image when running in the robot. Another is the FAT partition which contains UBOOT, ZIMAGE, DTS (Device Tree) and the Gateware .BIT file. Those can be updated individually. 

The DexRun.c [firmware](Firmware) communicates with the gateware by setting and reading [memory mapped registers](oplet-write) through the ['w' command oplet](oplet-write)
