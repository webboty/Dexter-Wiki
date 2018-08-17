The new [Tool Interface](End-Effectors) for end effectors incorporates 2 [Dynamixel XL-320 servos](http://support.robotis.com/en/product/actuator/dynamixel_x/xl_series/xl-320.htm) which use a [single wire, half duplex, serial interface](http://support.robotis.com/en/product/actuator/dynamixel_x/xl-series_main.htm) and a data format known as the [Dynamixel v.20 Protocol](http://support.robotis.com/en/product/actuator/dynamixel_pro/communication.htm)

The connector on the servo is a Molex Microlatch 3 pin. For a PCB, the mating part is the [Molex 53253-0370](https://www.mouser.com/ProductDetail/Molex/53253-0370?qs=sGAEpiMZZMs%252bGHln7q6pm%2fK1Y9dcUIE1bkILUHD%2fB%252bE%3d)

Pin | Signal 
--- | ---- 
 1 | GND 
 2 | VDD 
 3 | DATA 

Data for the servo bus comes from the main controller board. See the [Tool Interface](End-Effectors) page for more on that wiring.

The firmware was [changed May 29th on the TDInt branch](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) to accept servo commands via the [Set Parameter](set-parameter-oplet) command and to include measured angle and force data via the returned [status data](status-data)