The new [Tool Interface](End-Effectors) for end effectors incorporates 2 [Dynamixel XL-320 servos](http://support.robotis.com/en/product/actuator/dynamixel_x/xl_series/xl-320.htm) which use a [single wire, half duplex, serial interface](http://support.robotis.com/en/product/actuator/dynamixel_x/xl-series_main.htm) and a data format known as the [Dynamixel v.20 Protocol](http://support.robotis.com/en/product/actuator/dynamixel_pro/communication.htm). These become the 6th and 7th axis for the robot.

The connector on the servo is a Molex Microlatch 3 pin. For a PCB, the mating part is the [Molex 53253-0370](https://www.mouser.com/ProductDetail/Molex/53253-0370?qs=sGAEpiMZZMs%252bGHln7q6pm%2fK1Y9dcUIE1bkILUHD%2fB%252bE%3d)

Pin | Signal 
--- | ---- 
 1 | GND (nearest the feedhorn)
 2 | VDD 
 3 | DATA 

Data for the servo bus comes from the main controller board. See the [Tool Interface](End-Effectors) page for more on that wiring.

The firmware was [changed May 29th on the TDInt branch](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) to accept servo commands via the [Set Parameter](set-parameter-oplet) command and to include measured angle and force data via the returned [status data](status-data)

Communications are at 115,200 baud. Joint 4 is Servo ID 3, and Joint 5 is Servo ID 1 on the Dynamixel Protocol 2.0 bus. The following Arduino program will setup the 

````
// ========================================
// Dynamixel XL-320 Setup for Dexter
// ========================================

#include "XL320.h"
// You will need to install the XL320 library from:
// https://github.com/hackerspace-adelaide/XL320

// Name your robot!
XL320 robot;
int servoID = 3; //desired servo ID number (1 or 3 for Dexter)

void setup() {

// Uncomment these one at a time, program the Arduino, then power cycle the servo
//IMPORTANT: POWER CYCLE EACH TIME YOU DO ONE OF THE SETUP STEPS

//Step 1 - Set the ID number
/*
  Serial.begin(1000000); //Default baud rate for XL-320 servos
  robot.begin(Serial); 
  delay(100);
  robot.sendPacket(254, XL_ID, servoID); //broadcast to all servos
*/

//Step 2 - Set the baud rate
/*
  Serial.begin(9600); // Goes back to 9600 after ID set for some reason
  robot.begin(Serial); 
  delay(100);
  // writePacket(1, XL_BAUD_RATE, x) sets the baud rate:
  // 0: 9600, 1:57600, 2:115200, 3:1Mbps
  robot.sendPacket(254, XL_BAUD_RATE, 2);
*/

//Step 3 - Test / Move to zero
/*
  Serial.begin(115200); // Now at our desired baud rate
  robot.begin(Serial);
  delay(100);
  robot.moveJoint(servoID, 0);  
*/
}

void loop() {
}
```` 