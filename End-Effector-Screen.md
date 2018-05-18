The new [Tool Interface](End-Effectors) standard includes a [Tinyscreen+](End_Effector_Screen) ARM based Arduino compatible processor with a small OLED screen, 4 buttons, and lots of IO. 

## IDE Setup
To program the Tinyscreen+, install the Arduino IDE and then, under Files / Preferences click the boxes to the right of the "Additional Boards Manager URLs" and add 
<BR>`https://tiny-circuits.com/Downloads/ArduinoBoards/package_tinycircuits_index.json` on a separate line.

Select Tools / Boards / Boards Manager... (scroll to the top) and search for SAMD. Select "Arduino SAMD Boards (32-bits ARM Cortex-M0+), and click Install. Once that is finished search for Tinyscreen and install the "Tinycircuits SAMD Boards". You may receive a warning that Windows is unable to verify the publisher during the install. Once the board files are installed, under Tools / Boards, (scroll down) select Tinyscreen+.

Under Sketch / Include Library / Manage Libraries... search for Tinyscreen and install. This provides easy access to the screen and other functions.

Connect the Tinyscreen+ to the PC, hold down the button on the edge closest to the USB cable (marked "<BOOT" on the PCB) and turn on the device with the slide switch. The screen should show that it is in bootloader mode. 

On windows PCs, in Control Panel / Devices, Device Manager, look for "Teensy USB Serial" under Ports. If "Unknown Device" appears, install the drivers for Windows 7 / 8<BR>
https://tinycircuits.com/blogs/learn/171313479-tinyscreen-windows-7-8-driver-installation
<BR>to get the port to show up. 

See also:<BR>
https://tinycircuits.com/blogs/learn/158833543-tinyscreen-setup

## Programming
Set Tools / Board to the Tinyscreen+, select the port and upload your sketch. If it doesn't work, power on the Tinyscreen+ with the <BOOT button (closest to the USB cable) held down to get into bootloader mode. The port may be different, so check that and upload again.

Your sketch will need to start with
````
#include <SPI.h>
#include <TinyScreen.h>

TinyScreen display = TinyScreen(TinyScreenPlus);

void setup(void) {
  display.begin();
  //display.on(); //on by default, but needed after display.off() which saves power.
  display.setBrightness(10); //sets main current level, valid levels are 0-15
  SerialUSB.Begin(9600); //USB to PC communications is via SerialUSB, NOT Serial
  Serial1.Begin(1000000); //Dexter coms on Serial1, pins 0 and 1 at 1MBPS
  }

/* BASIC DISPLAY FUNCTIONS */
//display.clearScreen(); //clears entire display- the same as clearWindow(0,0,96,64)
//display.clearWindow(x start, y start, width, height); 
//  clears specified OLED controller memory
//display.setFlip(true); //flips display upside down
//display.setMirror(true); //mirrors display left/right

/* BASIC GRAPHICS */
//display.drawRect(x start, y start, width, height, fill, 8bitcolor);
//  sets specified OLED controller memory to an 8 bit color, 
//  fill is a boolean- TSRectangleFilled or TSRectangleNoFill
//display.drawRect(x start, y start, width, height, fill, red, green, blue);
//  like above, but w/ 6 bit color values. Red and blue ignore the LSB.
//display.drawLine(x1, y1, x2, y2, 8bitcolor);
//  draw a line from (x1,y1) to (x2,y2) with an 8 bit color
//display.drawLine(x1, y1, x2, y2, red, green, blue);
//  like above, but uses 6 bit color values. Red and blue ignore the LSB.
//display.drawPixel(x,y,color);//set pixel (x,y) to specified color. This is slow.

// Color definitions
// 8b: BBBG GGRR
// #define bgr8(blue, green, red) ( (blue & 3) | (green & 7)<<2 | (red & 7)<<5 )

/* BITMAP GRAPHICS */
//display.setX(x start, x end);//set pixel x start, wrap around at x end
//display.setY(y start, y end);//set pixel y start, wrap around at y end
//display.goTo(x,y); //Set pixel x and y, as above, wrap at x and y max.
//display.startData();
//unsigned char buffer = { TS_8b_colors, ... };
//display.writeBuffer(buffer,count); //optimized write of a large buffer of 8 bit data.
//display.endTransfer();

/* TEXT GRAPHICS */
//display.setFont(thinPixel7_10ptFontInfo);
//display.fontColor(text color, background color); //sets text and background color
//display.setCursor(x,y); //set text cursor position to (x,y)
//display.print("Example Text!");
//display.println(" Print, move to next line");
//int width=display.getPrintWidth("Example Text!"); //total width in pixels
//int height=display.getFontHeight(); //always the same for a given font

//getButtons(TSButtonUpperLeft|TSButtonUpperRight) true if any listed button is pressed


````

## Communications 
Serial (not Serial1) is the hardware serial port connected to IO pin 0 (RX in) and 1 (TX out), which is free to use to connect to external serial devices.

The Tinyscreen+ will listen on the same data bus used by the [servos](End-Effector-Servos) and may return data there or via the dedicated return data line. To decode the [Dynamixel V2.0 Protocol](http://support.robotis.com/en/product/actuator/dynamixel_pro/communication.htm), a small serial filter routine can be added to standard Arduino code. 

````
#include <Wire.h>
#include <SPI.h>
#include <TinyScreen.h>

//current Dexter to Dynamixel XL-320 serial rate is 115200
#define BAUD_RATE 115200

//#define SerialPort SerialUSB //NO that's the USB serial
//#define SerialPort Serial1 //NO that's something else
#define SerialPort Serial
//IO pin 0, RX (in) and IO pin 1, TX (out)
#define SERVO_ID 0x02
#define SERVO_WRITE_INST 0x03
//Library must be passed the board type: TinyScreenPlus for TinyScreen+
TinyScreen display = TinyScreen(TinyScreenPlus);
#define rgb8(red, green, blue) ( (red>>1 & 3) | (green & 7)<<2 | (blue & 7)<<5 )

byte buf[100]; //max packet length?
byte c;
int servostate = 0;
int servolen = 0;
int i = 0;

void setup(void) {
  Wire.begin();
//initialize I2C before we can initialize TinyScreen- not needed for TinyScreen+
  display.begin();
  //setBrightness(brightness);//sets main current level, valid levels are 0-15
  display.setBrightness(10);
  display.clearScreen();
  display.setFont(thinPixel7_10ptFontInfo);
  display.setCursor(0,20);
  display.fontColor(rgb8(5,3,7),TS_8b_Black);
  display.print("Ready");
  SerialPort.begin(BAUD_RATE);
  }

void loop() {
  c = SerialPort.read();
  //display.setCursor(0,20);
  //display.print(String(c,HEX));
  switch (servostate) {
    case 0: servostate = (0xff == c? 1: 0); break; //header 1 and 2
    case 1: servostate = (0xfd == c? 2: 0); break; //header 3
    case 2: servostate = (0x00 == c? 3: 0); break; //reserved
    case 3: servostate = (SERVO_ID == c? 4: 0); break; //only our addr
    case 4: servostate = 5; servolen = c; break; //low byte of length
    case 5: servostate = 6; servolen += c<<8; servolen -= 3; break; //less inst/crc
    case 6: servostate = (SERVO_WRITE_INST == c? 7: 0); break; //only writes
    case 7: servostate = (!servolen--? 8: 7); buf[i++] = c; break;
    case 8: servostate = 9; buf[i]=0; break; //end TODO: check CRC
    default: servostate = 0; i=0; break; //recycle
    }

  if (sizeof(buf) < i) {
    i = sizeof(buf);
    display.setCursor(30,10);
    display.fontColor(rgb8(7,0,0),TS_8b_Black);
    display.print("OVERRUN");
    }
  }
````

See also:<BR>
- https://github.com/TinyCircuits/TinyCircuits-TinyScreen_Lib/blob/master/TinyScreenReferenceManual.pdf Not searchable.
- https://tinycircuits.com/search?type=article&q=Tinyscreen%2B
- https://github.com/arduino/ArduinoCore-samd The Tinyscreen+ is based on the Atmel SAMD21 processor. 
- http://ww1.microchip.com/downloads/en/DeviceDoc/40001882A.pdf SAMD21 datasheet.
- https://www.robotshop.com/media/files/pdf/tinyscreen-plus-kit-schematic.pdf Schematic
- https://www.mouser.com/datasheet/2/185/DF12_catalog-938983.pdf The expansion connector
- https://github.com/TinyCircuits/TinyCircuits-TinyScreen_Lib
- https://codebender.cc/user/TinyCircuits A few examples are for the Tinyscreen+, most Tinyscreen demos are also useful
