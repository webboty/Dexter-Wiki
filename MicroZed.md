Dexter's "brain" is a [ZedBoard.org](http://zedboard.org) [MicroZed](http://zedboard.org/product/microzed) board. The [7020 commercial](https://www.avnet.com/shop/us/products/avnet-engineering-services/aes-z7mb-7z020-som-g-rev-g-3074457345635208188/) version, NOT the 7010. [Schematic Rev F](http://microzed.org/sites/default/files/documentations/MicroZed_Rev_F_Schematic_141212.pdf), [Hardware Guide](http://zedboard.org/sites/default/files/documentations/5276-MicroZed-HW-UG-v1-7-V1.pdf)

<img src="http://zedboard.org/sites/default/files/product_spec_images/front_view_overlay.png" width=729 height=404>

Note: The [SD Card](SD-Card-Image) slot is directly under the Micro USB connector, on the opposite side of the board. To boot from the SD card, the jumpers must be set as follows:  JP1: 1-2, JP2: 2-3, JP3: 2-3. _Note: An internal diagnostic boot from the onboard QSPI is available by moving JP2: 1-2. This is only useful when connected via the [micro USB port](Dexter-USB-Connection)._

## Specs
**Processor:** [Zynq™-7000](https://www.xilinx.com/products/silicon-devices/soc/zynq-7000.html) AP [SoC XC7Z020-CLG400-1](https://www.xilinx.com/support/documentation/data_sheets/ds190-Zynq-7000-Overview.pdf), with two ARM 7 cores and an [FPGA](Gateware)<BR>
**Memory:** - 1 GB DDR3 - 128 Mb Quad-SPI Flash - [microSD card](SD-Card-Image) (just under the contains the ["Xillinux" Ubuntu operating system](http://xillybus.com/xillinux), DexRun [firmware](Firmware), and the .bit files to load the [FPGA](Gateware) 
<BR>
**I/O:**
 - [10/100/1000 Ethernet](Dexter-Networking)
 - USB 2.0 A Host port (under Ethernet) for things like a [WebCam](WebCam) or [other IO](End-Effectors)
 - USB-UART Micro B port for [console connection](Dexter-USB-Connection)
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
         function(){out("sw1 on the zedboard is:" + this.user_data.sw1)},
         Dexter.write_to_robot("51", "/sys/class/gpio/unexport")
         ]})
````
GPIO pins 9-15 are routed to the PMOD connector J5. If not used for a PMOD device, those IO pins could be accessed there.

