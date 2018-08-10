The new [Tool Interface](End-Effectors) for end effectors incorporates 2 [Dynamixel XL-320 servos](http://support.robotis.com/en/product/actuator/dynamixel_x/xl_series/xl-320.htm) which use a [single wire, half duplex, serial interface](http://support.robotis.com/en/product/actuator/dynamixel_x/xl-series_main.htm) and a data format known as the [Dynamixel v.20 Protocol](http://support.robotis.com/en/product/actuator/dynamixel_pro/communication.htm)

The connector on the servo is a Molex Microlatch 3 pin. For a PCB, the mating part is the [Molex 53253-0370](https://www.mouser.com/ProductDetail/Molex/53253-0370?qs=sGAEpiMZZMs%252bGHln7q6pm%2fK1Y9dcUIE1bkILUHD%2fB%252bE%3d)

Pin | Signal 
--- | ---- 
 1 | GND 
 2 | VDD 
 3 | DATA 

Data for the servo bus comes from the main controller board. See the [Tool Interface](End-Effectors) page for more on that wiring.