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

## Tool Chain

### Viva
Dexters gateware is written in [Viva](https://github.com/vivaimagined/Viva), a graphical system for defining logic. This (very) large picture shows the overview of that logic diagram: [Dexter FPGA logic diagram overview](https://user-images.githubusercontent.com/419392/57746151-be2ea780-7684-11e9-80b5-95490f015973.png)

To install Viva, 
- Download the Azido.msi installer and install the program on your system. 
- Download the SystemDescriptions.zip file and copy the SystemDescriptions folder under the "Azido" install folder. e.g. `C:\Program Files (x86)\Azido\SystemDescriptions` (you may need administrative rights)
- Run VivaSD.exe, and on the System menu select "Open System" and select the .sd file for the desired system. For Dexter, it would be `SystemDescriptions\OpalKelly\XEM6002\PMODBCSxAXIuZNObcs.sd` file. This file contains the system descriptions for both the [MicroZed](MicroZed) board and the [Opal Kelly XEM6002 FPGA board](https://opalkelly.com/products/xem6002/). This board can be used as an ICE for the MicroZed board. Other settings are required for the ICE capacity, including an environment variable called XIL_INSTALL=D:\Xilinx\14.6\ISE_DS\ISE\bin\nt. This is NOT required to use Viva for Dexters FPGA.
- Set Tools / Preferences as follows<br>![](https://user-images.githubusercontent.com/419392/58519674-779f7980-8169-11e9-9146-c67a6d174f35.png)
- Add an Environment Variable called XIL_INSTALL which points to the Xilinx install folder and the \ISE_DS\ISE\bin\nt folder e.g. `D:\Xilinx\14.6\ISE_DS\ISE\bin\nt`


### Xilinx ISC
To program the FPGA on Dexter, you will need to install the Xilinx ISC. We know the 14.6 version works. (14.7 probably works as well, we just haven't tested it.)<br>
https://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/design-tools/archive.html
- Extract the Xilinx_ISE_DS_Win_14.6_P.68d_3.tar file to a folder.
- Run the xsetup.exe installer
- Accept the license terms.
- Select "ISE Webpack" for installation.
- Accept the default settings (you can change install location, but make sure it's off a root directory. E.g. D:\Xillinx is fine)
- Continue to setup the Visual C++ 2008 runtime system
- Continue to allow the device drivers to be installed

When the install finishes and the License Configuration Manager appears, 
- select "Get free Vivado/ISE WebPack license". 
- You will need to connect, and sign into Xilinx web site (create an account if you don't have one). 
- Select "ISE WebPack license" and click "Generate Node-Locked License". 
- Continue through the wizard and complete the license request. 
- On the "Manage Licenses" screen, press the download arrow icon (lower left corner). 
- Back in the License Configuration Manager, load the Xilinx.lic file and close.

 