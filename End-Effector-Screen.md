The new [Tool Interface](End-Effectors) standard includes a [Tinyscreen+](End_Effector_Screen) ARM based Arduino compatible processor with a small OLED screen, 4 buttons, and lots of IO. 

## IDE Setup
To program the Tinyscreen+, install the Arduino IDE and then, under Files / Preferences click the boxes to the right of the "Additional Boards Manager URLs" and add `https://tiny-circuits.com/Downloads/ArduinoBoards/package_tinycircuits_index.json` on a separate line.

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

/* BITMAP GRAPHICS */
//display.setX(x start, x end);//set OLED RAM to x start, wrap around at x end
//display.setY(y start, y end);//set OLED RAM to y start, wrap around at y end
//display.startData();
//unsigned char buffer = { TS_8b_colors, ... };
//display.writeBuffer(buffer,count); //optimized write of a large buffer of 8 bit data.
//display.endTransfer();

/* TEXT GRAPHICS */
//display.setFont(thinPixel7_10ptFontInfo);
//display.fontColor(text color, background color); //sets text and background color
//display.setCursor(x,y); //set text cursor position to (x,y)- in this example, the example string is centered
//display.print("Example Text!");
//display.println(" Print, move to next line");
//int width=display.getPrintWidth("Example Text!"); //total width in pixels
//int height=display.getFontHeight(); //always the same for a given font

//getButtons(TSButtonUpperLeft|TSButtonUpperRight) true if any listed button is pressed


````

## Communications 
The Tinyscreen+ will listen on the same data bus used by the [servos](End-Effector-Servos) and may return data there or via the dedicated return data line. To decode the [Dynamixel V2.0 Protocol](http://support.robotis.com/en/product/actuator/dynamixel_pro/communication.htm), a small serial filter routine can be added to standard Arduino code. 

````
#define ID "\xF0"
//fake header for testing
//#define HEADER "AAB0"
#define  HEADER "\xFF\xFF\xFD\x00"
//fake inst for testing
//#define WRITE_INST '3'
#define WRITE_INST '\x03'

char buf[100]; //max packet length?

void setup(){
    Serial.begin(9600);
    Serial.print("Hello World!");
	}

void loop() {
  if (Serial.find( HEADER ) && Serial.find(ID)) {
    Serial.readBytesUntil('\xFF',buf,min(3,sizeof(buf)));
    int l = buf[0]+buf[1]*256; //get the length
    buf[0]=0; //incase it's not a write
    if ( WRITE_INST == buf[2]) 
	Serial.readBytesUntil('\xFF',buf,min(l,sizeof(buf)));
  	}
  Serial.print(":" );
  Serial.println(buf );
  buf[0]=0;
	//exit(0);
  }
````

See also:<BR>
- https://github.com/TinyCircuits/TinyCircuits-TinyScreen_Lib/blob/master/TinyScreenReferenceManual.pdf
- https://tinycircuits.com/search?type=article&q=Tinyscreen%2B
- https://github.com/TinyCircuits/TinyCircuits-TinyScreen_Lib
