<table align="left" style="float:left;"><tr>
<td>
The new [Tool Interface](End-Effectors) standard includes a Tinyscreen+ ARM based Arduino compatible processor with a small OLED screen, 4 buttons, and lots of IO. _Note: this is NOT the Tinyscreen. The + sign makes a huge difference._
</td>
<td>
On this page:
<br><a href="#mounting">Mounting</a>
<br><a href="#ide-setup">IDE Setup</a>
<br><a href="#programming">Programming</a>
<br><a href="#communications">Communications</a>
<br><a href="#see-also">See also</a>
</td></tr></table>
Available here:<BR>
https://tinycircuits.com/collections/processors/products/tinyscreenplus

## Mounting

_Wide meaning left to right as viewing the screen, long meaning top to bottom, high meaning surface to distant._

1.025" wide, 0.991" long, ~0.5" high (with space for expansion PCB and wires)

|To, From:|left| top| surface|
|---|---------|----|--------|
|Button 1 center| -0.026| 0.212| -0.146|
|Button 2 center| -0.026| 0.487| -0.146|
|Button 3 center| 1.039| 0.212| -0.146 |
|Button 4 center| 1.039| 0.487| -0.146|
|Switch 1 center| -0.026| 0.773| -0.146|
|Mounting hole 1 center| 0.175| 0.920| 0|
|Mounting hole 1 center| 0.847| 0.920| 0|
|USB micro B| 0.278| 0| -0.165 |
|Viewable area| 0.750| 0.051| 0| 
|to |0.965| 0.651| 0|

Mounting hole diameter: 0.056

Button face: 0.068 long, 0.050 high. They depress about 0.01" and are very difficult to operate. An "L" lever to allow easy operation by pressing down will be necessary... or a larger piston type button to press in from the side. 

Switch face: 0.264 long, 0.050 high, this is the power switch and may be necessary to programming. Access with a toothpick or tweezers is probably enough.

USB port (micro B) is even with top edge, 0.30 wide, 0.111 high but it's best to leave more space as connectors can have extra plastic around them and won't be able to plug in otherwise. Access to this port is required to update firmware. 

All the wiring comes out the lower face away from the surface (the back side) and is centered over the hole in the "Gripper" part over the tool interface mate to the differential so it would be best to leave as much space under the unit as possible.
![](https://github.com/HaddingtonDynamics/Dexter/blob/master/Hardware/GripperAssembly.PNG)

A PCB with a power regulator to provide the +5 the Tinyscreen+ needs from the 8.75 volt servo power, provide access to IO pins and power to end effectors via an array of pogo pins, and connect to the tinyscreen via a ribbon cable connector is being designed. [Schematic and PCB](https://workspace.circuitmaker.com/Projects/Details/Caleb-Ho/Dexter-Tool-Interface-v2-Tinyscreen-Dynamixel-Copy). It attaches to the back of the center gripper on the side towards the roll bearing. The wires will solder directly into it, and it will provide connectors for the servos broken out from those wires.

There are no mounting holes at the top of the Tinyscreen+. so a lip that holds the top down would be wise. This will probably conflict with the USB port so the lip can really only be at the center to the right side. 

The back surface at the top can come in about 0.265 from the top at a height of about -0.272 for the expansion PCB to rest on and about 0.171 from the top at a height of about -0.240 for the main PCB to rest on.

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
Serial (not Serial1) is the hardware serial port connected to IO pin 0 (RX in) and 1 (TX out), which is free to use to connect to external serial devices. See the [Tool Interface](End-Effectors) page for more on that wiring.

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

void setup(void) {
  Wire.begin();//initialize I2C before we can initialize TinyScreen- not needed for TinyScreen+
  display.begin();
  display.setBrightness(10);//sets main current level, valid levels are 0-15
  display.clearScreen();
  display.setFont(thinPixel7_10ptFontInfo);
  display.setCursor(0,20);
  display.fontColor(rgb8(5,3,7),TS_8b_Black);
  display.print("Ready");
  SerialPort.begin(BAUD_RATE);
  //SerialPort.print("Ready\n");
  }

byte buf[100]; //max packet length?
byte c;
int sstate = 0;
int slen = 0;
int i = 0;
unsigned short scrc = 0;
unsigned short crc_table[256] = {
  0x0000, 0x8005, 0x800F, 0x000A, 0x801B, 0x001E, 0x0014, 0x8011,
  0x8033, 0x0036, 0x003C, 0x8039, 0x0028, 0x802D, 0x8027, 0x0022,
  0x8063, 0x0066, 0x006C, 0x8069, 0x0078, 0x807D, 0x8077, 0x0072,
  0x0050, 0x8055, 0x805F, 0x005A, 0x804B, 0x004E, 0x0044, 0x8041,
  0x80C3, 0x00C6, 0x00CC, 0x80C9, 0x00D8, 0x80DD, 0x80D7, 0x00D2,
  0x00F0, 0x80F5, 0x80FF, 0x00FA, 0x80EB, 0x00EE, 0x00E4, 0x80E1,
  0x00A0, 0x80A5, 0x80AF, 0x00AA, 0x80BB, 0x00BE, 0x00B4, 0x80B1,
  0x8093, 0x0096, 0x009C, 0x8099, 0x0088, 0x808D, 0x8087, 0x0082,
  0x8183, 0x0186, 0x018C, 0x8189, 0x0198, 0x819D, 0x8197, 0x0192,
  0x01B0, 0x81B5, 0x81BF, 0x01BA, 0x81AB, 0x01AE, 0x01A4, 0x81A1,
  0x01E0, 0x81E5, 0x81EF, 0x01EA, 0x81FB, 0x01FE, 0x01F4, 0x81F1,
  0x81D3, 0x01D6, 0x01DC, 0x81D9, 0x01C8, 0x81CD, 0x81C7, 0x01C2,
  0x0140, 0x8145, 0x814F, 0x014A, 0x815B, 0x015E, 0x0154, 0x8151,
  0x8173, 0x0176, 0x017C, 0x8179, 0x0168, 0x816D, 0x8167, 0x0162,
  0x8123, 0x0126, 0x012C, 0x8129, 0x0138, 0x813D, 0x8137, 0x0132,
  0x0110, 0x8115, 0x811F, 0x011A, 0x810B, 0x010E, 0x0104, 0x8101,
  0x8303, 0x0306, 0x030C, 0x8309, 0x0318, 0x831D, 0x8317, 0x0312,
  0x0330, 0x8335, 0x833F, 0x033A, 0x832B, 0x032E, 0x0324, 0x8321,
  0x0360, 0x8365, 0x836F, 0x036A, 0x837B, 0x037E, 0x0374, 0x8371,
  0x8353, 0x0356, 0x035C, 0x8359, 0x0348, 0x834D, 0x8347, 0x0342,
  0x03C0, 0x83C5, 0x83CF, 0x03CA, 0x83DB, 0x03DE, 0x03D4, 0x83D1,
  0x83F3, 0x03F6, 0x03FC, 0x83F9, 0x03E8, 0x83ED, 0x83E7, 0x03E2,
  0x83A3, 0x03A6, 0x03AC, 0x83A9, 0x03B8, 0x83BD, 0x83B7, 0x03B2,
  0x0390, 0x8395, 0x839F, 0x039A, 0x838B, 0x038E, 0x0384, 0x8381,
  0x0280, 0x8285, 0x828F, 0x028A, 0x829B, 0x029E, 0x0294, 0x8291,
  0x82B3, 0x02B6, 0x02BC, 0x82B9, 0x02A8, 0x82AD, 0x82A7, 0x02A2,
  0x82E3, 0x02E6, 0x02EC, 0x82E9, 0x02F8, 0x82FD, 0x82F7, 0x02F2,
  0x02D0, 0x82D5, 0x82DF, 0x02DA, 0x82CB, 0x02CE, 0x02C4, 0x82C1,
  0x8243, 0x0246, 0x024C, 0x8249, 0x0258, 0x825D, 0x8257, 0x0252,
  0x0270, 0x8275, 0x827F, 0x027A, 0x826B, 0x026E, 0x0264, 0x8261,
  0x0220, 0x8225, 0x822F, 0x022A, 0x823B, 0x023E, 0x0234, 0x8231,
  0x8213, 0x0216, 0x021C, 0x8219, 0x0208, 0x820D, 0x8207, 0x0202
  };
    
void crc() {
  scrc = (scrc << 8) ^ crc_table[((unsigned short)(scrc >> 8) ^ c) & 0xFF];
  }
  
 void loop() {
  while (-1==SerialPort.peek());
  c = SerialPort.read();
  //display.setCursor(0,20);
  //display.print(String(c,HEX));
  switch (sstate) {
    case  0: scrc=0; //start crc from zero
             sstate=(0xff == c? 1: 0); crc(); break; //header 1
    case  1: sstate=(0xff == c? 2: 0); crc(); break; //header 2
    case  2: sstate=(0xfd == c? 3: 0); crc(); break; //header 3
    case  3: sstate=(0x00 == c? 4: 0); crc(); break; //reserved
    case  4: sstate=(SERVO_ID == c? 5: 0); crc(); break; //only our addr
    case  5: sstate=6; slen = c; i = 0; crc(); break; //low byte of length
    case  6: sstate=7; slen += c<<8; slen -= 4; crc(); break; //without inst/crc
    case  7: sstate=(SERVO_WRITE_INST == c? 8: 0); crc(); break; //only writes
    case  8: sstate=(!slen--? 9: 8); buf[i++] = c; crc(); break; //data
    case  9: sstate=(c == (scrc & 0xFF)? 10: 0); buf[i]=0; break; //crclow 
    case 10: sstate=(c == ((scrc>>8) & 0xFF)? 11: 0); break; //crchigh 
    default: sstate = 0; i=0; crc(); break; //recycle
    }
  if (sizeof(buf) < i) { //check for more data than fits in buf
    i = sizeof(buf);
    display.setCursor(30,10);
    display.fontColor(rgb8(7,0,0),TS_8b_Black);
    display.print("OVERRUN");
  }
#ifdef DEBUG
  if (!sstate) shake = (shake? 0:1); //shake so we know new stuff came in
  display.setCursor(sstate*7,0+shake);
  display.print(sstate,HEX);
  if (sstate<7) {
  display.setCursor(sstate*12,10+shake);
  } else {    
  display.setCursor((sstate-6)*12,20+shake);
  }
  display.print(c,HEX);
  display.print(' ');
  if (10 == sstate || 11 == sstate) { //show the CRC
    display.setCursor(40,40+shake);
    display.print(scrc,HEX); //the calculated word
    display.setCursor((11-sstate)*14,40+shake); //aligned
    display.print(c,HEX); //this is the recieved byte
  }
  if (11 == sstate) { //got a packet (9 for no crc, 11 for crc)
    display.setCursor(0,30+shake);
    display.print(i);
    display.print(' ');
    for (int j=0;buf[j]&&sizeof(buf)>j;j++) {
      display.print(buf[j],HEX); //assumes the data is a string
      display.print(':');
    }
    if ('-' == s) s='|'; else s='-'; //should change each time we send a command
    display.print(s);
    SerialPort.print("AU"); //A is 01000001 and U is 01010101
    sstate = 0;
    }
#else //try for a simple memory map of the display
  if (11 == sstate) { //got a packet
    sstate = 0; //DON'T FORGET THIS!
    display.setCursor(buf[0],buf[1]);
    display.fontColor(buf[3],TS_8b_Black);
    display.print(char(buf[2]));
    //Next 2 a hack. Single chars won't display. Bug?
    display.setCursor(0,0); //so do a string off the screen. 
    display.print(" "); //if you don't do this, it won't print anything.
    }
#endif
  }

````

In [DDE](DDE), the `make_ins("S","ServoSet2X")` command can be used to send data to the Tinyscreen+ using the sketch above if you have Dynamixel support in your [FPGA](Gateware) [image](SD-Card-Image) (from ~April 2018) and in the DexRun firmware (TDInt branch). In the future, support for sending longer strings will be added, see Issue #32:<BR>
https://github.com/HaddingtonDynamics/Dexter/issues/32

````
function send_screen_data(screen_addr, data) {
	let payload = ""
    let instrs = []
	for(let char of data) {
        payload += char
        if (payload.length >= 4) {
            instrs.push( //out(
                make_ins("S", "ServoSet2X", screen_addr 
                , (payload.charCodeAt(0) || 0)
                +((payload.charCodeAt(1) || 0) << 8)
                , (payload.charCodeAt(2) || 0)
                +((payload.charCodeAt(3) || 0) << 8)
                  )) //)
            payload = ""
            }
    	}
    if (payload.length > 0) {
		instrs.push( //out(
			make_ins("S", "ServoSet2X", screen_addr 
    	   	, (payload.charCodeAt(0) || 0)
    	    +((payload.charCodeAt(1) || 0) << 8)
    	    , (payload.charCodeAt(2) || 0)
    	    +((payload.charCodeAt(3) || 0) << 8)
    	      )) //)
		}
	return instrs
	}

function screen_color(red, green, blue) {
  return ( (red>>1 & 3) | (green & 7)<<2 | (blue & 7)<<5 )
  }

function send_screen_char(screen_addr, x, y, c, red, green, blue) {
	let instrs = []
    instrs.push( //out(
      make_ins("S", "ServoSet2X", screen_addr 
               , (x || 0)
               +((y || 0) << 8)
               , (c.charCodeAt(0) || 0)
               +((screen_color(red, green, blue) || 0) << 8)
              )) //)
    return instrs
    }
function send_screen_string(screen_addr, x, y, a_string, red, green, blue) {
    let instrs = []
    let color = screen_color(red, green, blue)
	for(let char of a_string) {
        instrs.push( //out(
          make_ins("S", "ServoSet2X", screen_addr 
            , (x || 0)
            +((y || 0) << 8)
            , (char.charCodeAt(0) || 0)
            +((color || 0) << 8)
            )) //)
        x+=6
        }
    return instrs
    }

new Job({name: "my_job",
        robot: Robot.dexter241,
        do_list: [
            //Dexter.move_all_joints(10, 0, 0, 0, 0), //not needed
            //make_ins("S", "RebootServo", 1), //only needed for the XL-320's
            //make_ins("S", "RebootServo", 3),
            //Robot.wait_until(1), //only needed after RebootServo
            Dexter.move_all_joints(0, 0, 0, 0, 0), //REQUIRED! Servo system won't start without
            Robot.label("LoopPoint"),
            make_ins("g"),
            //DEBUG defined in Tinyscreen+ code.
            //make_ins("S", "ServoSet2X", 2, char1 + char2<<8, char3+char4<<8), // 
            //make_ins("S", "ServoSet2X", 2, 34 + (72 << 8), 105 + (34 << 8)), // "Hi"
            //make_ins("S", "ServoSet2X", 2, 25940, 29811), // Test
            //send_screen_data(2, "Test"),
            //DEBUG not defined in Tinyscreen+ code.
            //make_ins("S", "ServoSet2X", 2, x + (y << 8), char + (color << 8)),
            //make_ins("S", "ServoSet2X", 2, 30 + (30 << 8), 66 + (250 << 8)), // white "A" at 30,30
            //send_screen_char(2, 10, 10, 'A', 7,7,7),
            send_screen_string(2, 0, 20, "Hello world!", 7, 7, 0),
            //Robot.go_to("LoopPoint"),

            ]
        }
    )

````

## See also
- https://github.com/TinyCircuits/TinyCircuits-TinyScreenPlus-ASM2022 <BR>Includes: [Reference manual](https://github.com/TinyCircuits/TinyCircuits-TinyScreen_Lib/blob/master/TinyScreenReferenceManual.pdf) _Not searchable_ and [Examples](https://github.com/TinyCircuits/TinyCircuits-TinyScreenPlus-ASM2022/tree/master/examples)
- https://www.robotshop.com/media/files/pdf/tinyscreen-plus-kit-schematic.pdf <BR>Schematic
- https://github.com/TinyCircuits/TinyCircuits-TinyScreen_Lib <BR>General Libraries
- https://github.com/arduino/ArduinoCore-samd <BR>The Tinyscreen+ is based on the Atmel SAMD21 processor. 
- http://ww1.microchip.com/downloads/en/DeviceDoc/40001882A.pdf <BR>SAMD21 datasheet.
- https://tinycircuits.com/search?type=article&q=Tinyscreen%2B <BR>Additional articles about the Tinyscreen+
- https://cdn.shopify.com/s/files/1/1125/2198/files/ASD2021_Rev1.pdf?3908401193410811528 <BR>Ribbon cable expansion schematic.
- https://datasheet.octopart.com/FH19SC-32S-0.5SH%2899%29-Hirose-datasheet-506304.pdf <BR>Connector for the ribbon cable.
- https://www.digikey.com/product-detail/en/molex-llc/0152660337/WM11482-ND/3161072 <BR>Althernate ribbon cable, shorter, has conductor pads on the same side on both ends.
- https://www.mouser.com/datasheet/2/185/DF12_catalog-938983.pdf <BR>The expansion connector
- https://codebender.cc/user/TinyCircuits <BR>A few examples are for the Tinyscreen+, most Tinyscreen demos are also useful
- https://www.mouser.com/ProductDetail/855-P70-2300045R <BR>The pogo pins used in the end effector interface.
