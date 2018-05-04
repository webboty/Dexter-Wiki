The new [Tool Interface](End-Effectors) standard includes a [Tinyscreen+](End_Effector_Screen) ARM based Arduino compatible processor with a small OLED screen, 4 buttons, and lots of IO. 

## IDE Setup
To program the Tinyscreen+, install the Arduino IDE and then, under Files / Preferences click the boxes to the right of the "Additional Boards Manager URLs" and add `https://tiny-circuits.com/Downloads/ArduinoBoards/package_tinycircuits_index.json` on a separate line.

Select Tools / Boards / Boards Manager... (scroll to the top) and search for SAMD. Select "Arduino SAMD Boards (32-bits ARM Cortex-M0+), and click "Install". Once that is finished search for Tinyscreen and install the "Tinycircuits SAMD Boards". You may receive a warning that Windows is unable to verify the publisher during the install. 

Under Sketch / Include Library / Manage Libraries... search for Tinyscreen and install. This provides easy access to the screen and other functions.

Connect the Tinyscreen+ to the PC, hold down the button on the edge closest to the USB cable (marked "<BOOT" on the PCB) and turn on the device with the slide switch. The screen should show that it is in bootloader mode. 

On windows PCs, in Control Panel / Devices, Device Manager, look for "Teensy USB Serial" under Ports. If "Unknown Device" appears, install the drivers for Windows 7 / 8<BR>
https://tinycircuits.com/blogs/learn/171313479-tinyscreen-windows-7-8-driver-installation
<BR>to get the port to show up. 

## Programming
Select the port and upload your sketch. If it doesn't work, power on the Tinyscreen+ with the <BOOT button (closest to the USB cable) held down to get into bootloader mode. The port may be different, so check that and upload again.

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