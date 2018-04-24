

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