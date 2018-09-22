Dexter's "brain" is a [ZedBoard MicroZed](http://zedboard.org/product/microzed) 
## Specs
**Processor:** [Zynq™-7000](https://www.xilinx.com/products/silicon-devices/soc/zynq-7000.html) AP [SoC XC7Z010-CLG400-1](https://www.xilinx.com/support/documentation/data_sheets/ds190-Zynq-7000-Overview.pdf), with two ARM 9 cores and an [FPGA](Gateware)<BR>
**Memory:** - 1 GB DDR3 - 128 Mb Quad-SPI Flash - [microSD card](SD-Card-Image) contains the ["Xillinux" Ubuntu operating system](http://xillybus.com/xillinux), DexRun [firmware](Firmware), and the .bit files to load the [FPGA](Gateware) 
<BR>
**I/O:**
 - [10/100/1000 Ethernet](Dexter-Networking)
 - USB Host 2.0, for things like a [WebCam](WebCam) or [other IO](End-Effectors)
 - USB-UART
 - 2 MicroHeader connectors (108 single-ended, 48 differential pairs, Agile Mixed Signaling
(AMS)) which connect to the Motor Driver PCB [hardware](Hardware).
 - [Digilent Pmod™](https://store.digilentinc.com/pmod-expansion-modules/by-form-factor/2x4/) Compatible header (8 MIO)

**Clocking:** 33.33333 MHz clock source for PS<BR>
**Configuration and Debug:** Xilinx Platform Cable JTAG connector<BR>
**General Purpose I/O:** 1 user LED, 1 push button (SW1). The [Getting Started Guide](http://www.zedboard.org/sites/default/files/documentations/5164-MicroZed-GettingStarted-V1.pdf) Chapter 9 contains extesive examples of IO via file read and write to /sys/class/gpio "folder". e.g. to turn on the red LED on the main board, which is connected to GPIO pin 47, from the command prompt after logging into Dexter:
````
   echo 47 > /sys/class/gpio/export
   echo out > /sys/class/gpio/gpio47/direction
   echo 1 > /sys/class/gpio/gpio47/value
````
SW1 (or other GPIO lines) is available for input:
````
   echo 51 > /sys/class/gpio/export
   echo in > /sys/class/gpio/gpio51/direction
   cat /sys/class/gpio/gpio51/value
````
Since all of these are just reading and writing files, and DexRun has [read_from_robot](read-from-robot) and [write_to_robot](write-to-robot), we can access any available GPIO pin on the [MicroZed](MicroZed) board. For example, the following job in [DDE](DDE) will read the position of SW1:
````
new Job({name: "read_sw1",
	do_list: [
         Dexter.write_to_robot("51", "/sys/class/gpio/export"),
         Dexter.write_to_robot("in", "/sys/class/gpio/gpio51/direction"),
         Dexter.read_from_robot("/sys/class/gpio/gpio51/value", "sw1"),
         function(){out("sw1 on the zedboard is:" + this.user_data.sw1)}
         ]})
````
[Schematic Rev F](http://microzed.org/sites/default/files/documentations/MicroZed_Rev_F_Schematic_141212.pdf)

