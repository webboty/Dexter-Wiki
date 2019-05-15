# [Dexter Gateware](https://github.com/HaddingtonDynamics/Dexter/tree/master/Gateware)

"Gateware" is what we call the FPGA programming that makes Dexter stunningly responsive. It allows feedback from [encoder](Encoders) to motor in as little as 200 nanoseconds. 

Dexters gateware configures the [Xilinx Inc. XC7Z010-1CLG400C FPGA](http://www.xilinx.com/support/documentation/data_sheets/ds190-Zynq-7000-Overview.pdf) from the image of the [micro SD card](SD-Card-Image) on the [microzed](http://zedboard.org/product/microzed) board. 

The [SD card image](SD-Card-Image) has multiple partitions, one is the actual hard drive image when running in the robot. Another is the FAT partition which contains UBOOT, ZIMAGE, DTS (Device Tree) and the Gateware .BIT file. [Those can be updated individually](../blob/master/Gateware/README.md). 

The DexRun.c [firmware](Firmware) communicates with the gateware by setting and reading [memory mapped registers](oplet-write) through the ['w' command oplet](oplet-write)

The ultra fast update has several benefits:
1. We can run the P term in the PID controller up and not have oscillations. Actually since it’s integrated, and the time base is so fast, the P term is less than 1, but the point is that it tracks so close that in a slower system, it would oscillate. It doesn’t have time to overshoot. And that gives us better accuracy.

2. Small perturbations in the sensor system just get swamped by the good data around them. The mechanical system filters them out because it doesn’t have time to jerk. The motor inertia starts filtering mechanically at a few kHz, but If you get the update speed up over 50 kHz the motor inductance filters out the control signals as well.

3. The position of the motor is actually being dithered around the point it needs to be. We are stepping back and forth over points in between even the microstepping points. Especially on joints 4 and 5. This is where the inductive filtering helps us.

Mainly the group delay is so low that we don’t need any filtering.

Dexters gateware is written in Viva, a graphical system for defining logic. This (very) large picture shows the overview of that logic diagram: [Dexter FPGA logic diagram overview](https://user-images.githubusercontent.com/419392/57746151-be2ea780-7684-11e9-80b5-95490f015973.png)