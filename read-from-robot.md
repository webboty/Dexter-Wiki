Read From Robot is a protocol which allows data to be read from the Dexter file system without having to use a SAMBA share (which was blocked by Windows 10, see [Issue 50](https://github.com/HaddingtonDynamics/Dexter/issues/58)). The protocol is implemented on the Dexter [Firmware](firmware) side via the 'r' [oplet](Command-oplet-instruction) in [DexRun.c on the TDint branch on 2018/07/26](https://github.com/HaddingtonDynamics/Dexter/commit/243ac0fa3c995effd9c75731d3a9c7ecb70cc73e)*  (see below for more) and on the PC side in [DDE version 2.3.16, on 2018/06/04](https://github.com/cfry/dde/releases/tag/untagged-5d86b61c13b61d266905) as Dexter.read_from_robot(pathfile, user_variable) where:
- pathfile is one of the following:
  - a file name from the /srv/samba/share folder. e.g. "AdcCenters.txt"
  - the path and file name from root e.g. "/srv/samba/share/err.txt" 
  - a <a href="#keywords">#Keyword</a> which returns data without actually creating a file. E.g. "#POM" (was #XYZ) returns a Cartesian coordinate matrix of position and orientation which was added [2018/08/04](https://github.com/HaddingtonDynamics/Dexter/blob/e6db50da946176123e191e9af6660a318f240489/Firmware/DexRun.c#L2117)* 
  - a <a href="#bash-shell-commands">BASH Shell command</a> starting with "`" or backtick (ASCII 96 0x60) character
- user_variable is the name of the variable to add to the user_data object. e.g. "foo" would put the returned text of the file in user_data.foo

For example in DDE:

````
new Job({name: "my_job",
         do_list: [
         	Dexter.read_from_robot("/srv/samba/share/AdcCenters.txt", "centers_string"), 
            //returned data in user_data.centers_string
            function foo() {out(this.user_data.centers_string)}

            ]
         })
````

## 'r' [Oplet](Command-oplet-instruction)
In Dexter's [Firmware](Firmware), Read From Robot is implemented via the 'r' oplet. Most oplets return the standard [status data ](status-data), but the 'r' oplet instead returns the contents of a file from the robots file system, in blocks of MAX_CONTENT_CHARS bytes. The parameters for the 'r' command are the block number to read, and the file name, both as null terminated strings. The block number is in human readable ASCII format and should start with 0 then increase until fewer than MAX_CONTENT_CHARS bytes are returned. e.g. to read the AdcCenters.txt file from the share, [DDE](DDE) will send:
````
1 1 1543452381142 undefined r 0 /srv/samba/share/AdcCenters.txt
1 2 1543452381197 undefined r 1 /srv/samba/share/AdcCenters.txt
````

- The first value in the string is an arbitrary integer job number. Any number is fine.
- The second number is an arbitrary instruction number in that job. Normally this is incremented and is returned by DexRun so that you can tell which packet the response is for. Actually, this can be ignored and set to the same value every time.
- The third value is the start time of the instruction in seconds since January 1, 1970. This is totally optional; a simple number 1 works fine.
- The forth is the end time of the instruction, which is not known, and therefore the word "undefined" is sent. This can be replace with 0 or any other character. Note: In the future, this may be used to describe the desired format of return data, so for standard status packets, use "0".
- Next is the oplet 'r'.
- Next is the block number. This should start at 0 and increment by 1 as long as the returned packets have a PAYLOAD_LENGTH of MAX_CONTENT_CHARS.
- Finally, the path and file name of the desired file is sent. 

Because start and end times are not required, the requests above could have been sent as:
````
1 1 1 0 r 0 /srv/samba/share/AdcCenters.txt
1 2 1 0 r 1 /srv/samba/share/AdcCenters.txt
````

Please note the incrementing number after the 'r' oplet. This "block number" is required. 

## 'r' returned data

The returned data includes the number of bytes actually read as a binary integer and then the actual bytes of data from the file starting from the block number times MAX_CONTENT_CHARS for a length of MAX_CONTENT_CHARS or less if the end of the file is reached. The format of the returned data is the same as the [status data](status-data), except that after the oplet and error code, the 6th integer is the number of characters returned, and after that, the data is actually bytes. 

For example, assuming the example commands shown above were sent, and assuming the AdcCenters.txt file contained "0x5960000\r\n0x6540000\r\n0x47e0000\r\n0x46a0000\r\n0x3f20000\r\n0x3e80000\r\n0x4920000\r\n0x73a0000\r\n0x5000000\r\n0x51e0000\r\n" (where the \r is a 0x0D or carriage return, and the \n is a 0x0A or line feed), then the following packets would be recieved:

Packet #1:

Int | Addr | Description | Sample
--- | --- | --- | ---
  0 |  00 | Job number | 1
  1 |  04 | Instruction number | 1
  2 |  08 | Start time | 1543452381142 
  3 |  12 | End time | 1543452391148 
  4 |  16 | Oplet ('r') | 114
  5 |  20 | ERROR | 0
  6 |  24 | PAYLOAD_LENGTH | 62
  7-|  28-| PAYLOAD_DATA | "0x5960000\r\n0x6540000\r\n0x47e0000\r\n0x46a0000\r\n0x3f20000\r\n0x3e800"

_Note that from address 28 on to 86, we are using the BYTE form of the data, NOT the Integer form. Also, note that the PAYLOAD_LENGTH in integer 6 is 62 which is the MAX_CONTENT_CHARS value. This indicates that the entire file has NOT been read, and triggers another request, with an increased block number after the 'r' oplet._

Packet #2

Int | Addr | Description | Sample
--- | --- | --- | ---
  0 |  00 | Job number | 1
  1 |  04 | Instruction number | 2
  2 |  08 | Start time | 1543452381197 
  3 |  12 | End time | 1543452382199 
  4 |  16 | Oplet ('r') | 114
  5 |  20 | ERROR | 0
  6 |  24 | PAYLOAD_LENGTH | 48
  7-|  28-| PAYLOAD_DATA | "00\r\n0x4920000\r\n0x73a0000\r\n0x5000000\r\n0x51e0000\r\n"

_Note that the PAYLOAD_LENGTH of 48 is less than MAX_CONTENT_CHARS and so we have completed reading the file. Concatenating the second returned PAYLOAD_DATA string to the first results in the complete original file content._

### Errors
If there is an error, the [standard Linux error number](https://github.com/torvalds/linux/blob/master/include/uapi/asm-generic/errno-base.h) will be returned instead of the standard [status errors](status errors). The most common error is 2: "No such file or directory"

The incoming socket handler should not assume that each packet of data received is an 'r' as ['g' status](status-data) responses may also be received. Check the integer 4 Oplet value and process each type of data accordingly. 

*Note that this release of DexRun.c requires an updated [FPGA image](SD-Card-Image). Contact us for that file before updating the code.

## Keywords

There are special "files" which are generated inside the firmware and not actually saved as files, with names which start with the "#" symbol (and therefore can not be actual names in the file system) which return data which might not fit in a single socket payload. The read_from_robot functionality allows this data to be returned as if it were from a file, by transferring multiple blocks. 

Keyword | Datatype | Description | Sample
--- | --- | --- | ---
  #POM |  ASCII JSON, 4x4 matrix of floats | **P**osition and **O**rientation **M**atrix. (Was #XYZ aka Pose, Transformation Matrix, Transform, or Coordinate system matrix) First three columns are the orientation unit vectors for the x, y, and z axes aka DCM: [Direction Cosine](https://en.wikipedia.org/wiki/Direction_cosine) Matrix. The last column is XYZ position in microns. The 4th row is always [0, 0, 0, 1] to maintain transformation matrix format. A direction vector can be made from the 3rd column, z, by multiplying by -1. | `"[[1,0,0,0],[0,1,0,0],[0,0,1,0],[0,0,0,1]]"`
  #EyeNumbers | ASCII, 5 space delimited integers | Each encoder can measure where it is in the current eye but has to keep track of when it transitions between eyes. These 5 values are the indices for which eye number each encoder is on. The range is 0 to 512, center is 255. Note, they will not move all the way to 0 or 512.| `"286, 255, 255, 255, 324"`
  #RawEncoders | ASCII JSON, undocumented | RawEncoders is the uncorrected data about each joints position, prior to calibration. It does not take into account the slight changes in the "eye" from one slot to the next, or any mis-positioning of the eye center. Calibration is NOT required. The data format is complex and not yet documented. | `"[14319360, 596611840, 2108160, -1794560, -172800]"`
 #measured_angles | ASCII JSON, 5 integers | The measured angles. Calibration IS required. | `"[0, 0, 0, 0, 0]"`
 #Steps | ASCII JSON, 5 integers | The position the motor has been commanded to move to in steps. Added [20190816](https://github.com/HaddingtonDynamics/Dexter/commit/ce61cf652dc591dab8ba1096834206f7c551ce72). Calibration is not required.| `"[0, 0, 0, 0, 0]"` 
#StepAngles | ascii JSON, 5 integer arcseconds | The position the motor has been commanded to move to in arcseconds. Added [20190816](https://github.com/HaddingtonDynamics/Dexter/commit/ce61cf652dc591dab8ba1096834206f7c551ce72).  Calibration is not required.| `"[0, 0, 0, 0, 0]"`
  #Servo | id, address, length | <a href="https://github.com/HaddingtonDynamics/Dexter/commit/f87b7c898d891604474573a44642c551488a667f">2020/08/10</a> to close #<a href="https://github.com/HaddingtonDynamics/Dexter/issues/94">94</a> Read back data from Servo. This is mostly useful for debug, or for custom applications with the dynamixel servoes. | `"FF FF FD 03"...`

**Example:** To read the current Cartesian "Position and Orientation Matrix" send:
<br>`1 1 1543452381142 undefined r 0 #POW;` which will return 6 binary integers and then the first MAX_CONTENT_CHARS characters of "file" data. So, for example, you might see (expressed as 4 byte integers for the first 28 bytes, 0...27) 1, 1, 1543452381142, 349602, 114, 62, and then (expressed as single bytes starting from byte 28) 91, 91, 49, 46, 48, 48, 48, 48, 48, 48, 44, 32, 48, 46, 48, 48, 48, 48, 48, 48, 44, 32, 48, 46, 48, 48, 48, 48, 48, 48, 44, 32, 48, 46, 48, 48, 48, 48, 48, 48, 93, 44, 91, 45, 48, 46, 48, 48, 48, 48, 48, 48, 44, 32, 49, 46, 48, 48, 48, 48, 48, 48. "#POM" returns ASCII JSON data which you will see starting at the 28th byte in the returned packet. In this example the byte data (expressed as ASCII) is "[[1.000000, 0.000000, 0.000000, 0.000000],[-0.000000, 1.000000" The last binary integer, starting at 6 * 4 = 24 for 4 bytes, is the "length" of the data, and will almost certainly be MAX_CONTENT_CHARS. So you don't have all the data and then need to read the _next_ block:
<br>`1 2 1543452381147 undefined r 1 #POW;` which will return, again, 6 integers and then more ASCII data. Append that data to the prior data and look at the last int. If it's less than MAX_CONTENT_CHARS, stop, otherwise keep going with increasing block number:
<br>`1 3 1543452381152 undefined r 2 #POW;` Notice that the second value, the 1, 2, 3 does not need to be incremented.  It is just an "instruction number" to help you keep track of which status return you are looking at. Nor is the third value (it's just the time). Nor is the forth value; the "undefined" (it could just be "0"). But the 6th value, the "block number", after the 'r' oplet, which goes 0, 1, 2 IS required and MUST start from 0 and be increased without changing the "file name" in order to get all of the file data. 

## BASH Shell Commands

Starting [20190816](https://github.com/HaddingtonDynamics/Dexter/commit/ce61cf652dc591dab8ba1096834206f7c551ce72), if the "r 0" is followed by a \` or "back-tick" (ASCII 96 0x60) character, the rest of the command will be sent to a child processes started with the BASH shell. 
e.g. "r 0 \`ls" will return a directory listing; use "r 1 \` " (or "r" with any number >0 and a back-tick) repeatedly to get the rest of the data.

NOTE: It is critical when using this feature to read all the way to the end until an error 10 is returned. Do not stop sending "r 1 \`" commands just because you get back less than MAX_CONTENT_CHARS data. The reason for this is that the BASH command may require time to complete it's output. It might send back a few bytes at a time and not fill the return buffer completely with every read. When the child process has completed, doing a read will return an "end of stream" error which clearly indicates the process has finished. Until then, the process is running and may continue to generate output. New 'r' shell commands will fail because the file handle is still in use. 

CAUTION: If this function is not used correctly, it can really scramble the system. E.g. you can format the SD card with this. Or you can lose connectivity because the system is waiting on the child process to stop. 

## Read from FPGA memory

This has an internal debug function which reads and prints to the local console values from the [FPGA](Gateware) memory mapped IO. In that case, the first parameter is the address and the second the length. For example, `r 0 10` will display, on the stdout console (not returned via the socket), the first 10 memory values which list the types of the I/O data passed in the higher addresses. Each value encodes the width and the type of each interface. The number of interfaces is the second highest byte in the first memory address plus 1, but it will be obvious by the number of non-zero values returned. 

Note, Starting [20190816](https://github.com/HaddingtonDynamics/Dexter/commit/ce61cf652dc591dab8ba1096834206f7c551ce72), the interface count in the .bit file is checked, and if it does not match the INPUT_OFFSET in DexRun.c, a "fake" RAM array will be created and referenced instead of the FPGA's mapped memory to keep any commands from writing to the wrong places and causing damage. The firmware will continue to run and can be used to update the files via [write-to-robot](write-to-robot).

### Notes

The reason why we need a block number is because we have a very small returned packet. We limit the packet size because it gives us the fastest possible status return. If you increase the size of the return data (It's said you can do 65K bytes technically, but and MTU of 1600 is usually the limit, and 480 is sometimes the limit) but... when you go over 240, the data gets broken up into multiple packets. And then you loose speed. We wanted the absolute fastest possible response speed for tiny fast movements.
