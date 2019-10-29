The new [Tool Interface](End-Effectors) for end effectors incorporates 2 [Dynamixel XL-320 servos](http://emanual.robotis.com/docs/en/dxl/x/xl320/) which use a [single wire, half duplex, serial interface](http://emanual.robotis.com/docs/en/dxl/x/xl320/#connector-information) and a data format known as the [Dynamixel v.20 Protocol](http://emanual.robotis.com/docs/en/dxl/protocol2/). These become the 6th and 7th axis for the robot. Data for the servo bus comes from the main controller board.

Joint | Dynamixel Address | Description
----- | ----------------- | --------------
 6    | 3                 | Roll
 7    | 1                 | Span (open close gripper)

The connector on the servo is a Molex Microlatch 3 pin. For a PCB, the mating part is the [Molex 53253-0370](https://www.mouser.com/ProductDetail/Molex/53253-0370?qs=sGAEpiMZZMs%252bGHln7q6pm%2fK1Y9dcUIE1bkILUHD%2fB%252bE%3d)

Pin | Signal @Servo | Signal @Motor Board
--- | ---- | ---
 1 | GND (nearest the feedhorn) | J25 top pin
 2 | VDD | J25 bottom pin*
 3 | DATA | J20 bottom pin "AUX2"

*See the [Tool Interface](End-Effectors) page, <a href="https://github.com/HaddingtonDynamics/Dexter/wiki/End-Effectors#version-2">Version 2 section</a> for more on that wiring / power supply.

The firmware was [changed May 29th on the TDInt branch](../commit/42df0e01285ef8b67764ed53f3cc697df44d4d93) to accept servo commands via the [Set Parameter](set-parameter-oplet) command and to include measured angle and force data via the returned [status data](status-data)

Communications are at 115,200 baud. Joint 4 is Servo ID 3, and Joint 5 is Servo ID 1 on the Dynamixel Protocol 2.0 bus. 

### Servo Errors

The servos monitor operation and blink red when they encounter an [error](status-errors). They also return a bit code[^](http://emanual.robotis.com/docs/en/dxl/x/xl320/#shutdown18):

Bit | Value | Description 
--- | ----- | -------------
Bit 3~7	| Unused
Bit 2 |	ERROR_INPUT_VOLTAGE	| Voltage is out of operational voltage range
Bit 1 |	ERROR_OVER_HEATING	| Temperature is out of operational temperature range
Bit 0 |	ERROR_OVERLOAD  	| Motor cannot output max load due to load being applied continuously

These are binary additive. E.g. If the input voltage dips too low and the motor is also overloaded, the returned value will be 5 = 1*2^0 + 0*2^1 + 1*2^2. Or to decode the value:
````
if (error>=4) { error -= 4; //voltage error }
if (error>=2) { error -= 2; //overheated }
if (error>=1) { error -= 1; //overloaded }
````

If the error has been resolved, you can clear it with an `S RebootServo #` where # is the number of the servo. E.g. it's good to have the following code at the start of each job when you will be using the end effector servos:

````
do_list [
    set_parameter("RebootServo", 1), //reset J7 SPAN
    sleep(1), //give the servo time to reset.
    set_parameter("RebootServo", 3), //reset J6 ROLL
    sleep(1), //give the servo time to reset.
    ...
````

### Servo Setup

The following Arduino program will setup the servos for use in Dexter.


````C++
// ========================================
// Dynamixel XL-320 Setup for Dexter
// ========================================
// Talking standard serial, so connect servo data line to Arduino Digital TX 1
// Servo VDD needs to be +5, and of course, GND is GND.
#include "XL320.h"
// You will need to install the XL320 library from:
// https://github.com/hackerspace-adelaide/XL320

// Name your robot!
XL320 robot;
int servoID = 3; //desired servo ID number (1 "span" or 3 "roll" for Dexter)

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