The new [Tool Interface](End-Effectors) standard includes a [Tinyscreen+](End_Effector_Screen) ARM based Arduino compatible processor with a small OLED screen, 4 buttons, and lots of IO. The Tinyscreen+ will listen on the servo bus and may return data there or via the dedicated return data line. To decode the [Dynamixel V2.0 Protocol](http://support.robotis.com/en/product/actuator/dynamixel_pro/communication.htm), a small serial filter routine can be added to standard Arduino code. 

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