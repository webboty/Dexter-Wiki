# [Dexter Gateware](https://github.com/HaddingtonDynamics/Dexter/tree/master/Gateware)

"Gateware" is what we call the FPGA programming that makes Dexter stunningly responsive. It allows feedback from encoder to motor in as little as 200 nanoseconds. 

Dexters gateware configures the [Xilinx Inc. XC7Z010-1CLG400C FPGA](http://www.xilinx.com/support/documentation/data_sheets/ds190-Zynq-7000-Overview.pdf) from the image of the [micro SD card](SD-Card-Image) on the [microzed](http://zedboard.org/product/microzed) board. 

The DexRun.c [firmware](Firmware) communicates with the gateware by setting and reading memory mapped registers. 

Addr | Function
---- | ---------
42 | Command register<BR> Bits:<BR>1:CapCalibrateBase<BR>2:CapCalibrateEnd<BR>3:CapCalibratePivot<BR>4:GoMove<BR>5: SelectMoveChange<BR>6:EnableLoop<BR>7: AClrLoop <BR>8: CAL_RUN <BR>9: ResetForce