This allows data to be read from the Dexter file system without having to use a SAMBA share (which was blocked by Windows 10). Support was added in [DexRun.c TDint branch](https://github.com/HaddingtonDynamics/Dexter/commit/243ac0fa3c995effd9c75731d3a9c7ecb70cc73e)* as the 'r' [oplet](Command-oplet-instruction) (see below for more) and in [DDE version 2.3.16, June 4, 2018](https://github.com/cfry/dde/releases/tag/untagged-5d86b61c13b61d266905) as Dexter.read_from_robot(pathfile, user_variable) where 
- pathfile is the path and file name from root e.g. "/srv/samba/share/err.txt" or a "fake" file name starting with "#" which returns data without actually creating a file. E.g. "#XYZ" returns a cartesian coordinate matrix of position and orientation which was added in [DexRun.c TDint branch](https://github.com/HaddingtonDynamics/Dexter/blob/e6db50da946176123e191e9af6660a318f240489/Firmware/DexRun.c#L2117)*
- user_variable is the name of the variable to add to the user_data object. e.g. "foo" would put the returned text of the file in user_data.foo

For example:

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
In DexRun.c, this required that we implement separate return data for the 'r' oplet. Previously, all commands always return the 'g' status data. This is the first command to return something different. In this case, it returns the contents of a file from the robots file system, in blocks of MAX_CONTENT_CHARS bytes. The parameters for the 'r' command are the block number to read, and the file name, both as null terminated strings. The block number is in human readable ASCII format and should start with 0 then increase until fewer than MAX_CONTENT_CHARS bytes are returned. e.g. to read the AdcCenters.txt file from the share, [DDE](DDE) will send:
````
1 1 1543452381142 undefined r 0 /srv/samba/share/AdcCenters.txt
1 2 1543452381197 undefined r 1 /srv/samba/share/AdcCenters.txt
````

- The first value in the string is an arbitrary integer job number. Any number is fine.
- The second number is an arbitrary instruction number in that job. Normally this is incremented and is returned by DexRun so that you can tell which packet the response is for. Actually, this can be ignored and set to the same value every time.
- The third value is the start time of the instruction in seconds since January 1, 1970. This is totally optional; a simple number 1 works fine.
- The forth is the end time of the instruction, which is not known, and therefore the word "undefined" is sent. This can be replace with 1 or any other character.
- Next is the oplet 'r'.
- Next is the block number. This should start at 0 and increment by 1 as long as the returned packets have a PAYLOAD_LENGTH of MAX_CONTENT_CHARS.
- Finally, the path and file name of the desired file is sent. 

Because start and end times are not required, the requests above could have been sent as:
````
1 1 1 1 r 0 /srv/samba/share/AdcCenters.txt
1 2 1 1 r 1 /srv/samba/share/AdcCenters.txt
````

## 'r' returned data

The returned data includes the number of bytes actually read as a binary integer and then the actual bytes of data from the file starting from the block number times MAX_CONTENT_CHARS for a length of MAX_CONTENT_CHARS or less if the end of the file is reached. The format of the returned data is the same as the [status data](status-data), except that after the oplet and error code, the 6th integer is the number of characters returned, and after that, the data is actually bytes. 

For example, assuming the example commands shown above were sent, and assuming the AdcCenters.txt file contained "0x5960000\r\n0x6540000\r\n0x47e0000\r\n0x46a0000\r\n0x3f20000\r\n0x3e80000\r\n0x4920000\r\n0x73a0000\r\n0x5000000\r\n0x51e0000\r\n" (where the \r is a 0x0D or carriage return, and the \n is a 0x0A or line feed), then the following packets would be recieved:

Int | Address | Description | Sample
--- | --- | --- | ---
  0 |  00 | Job number | 1
  1 |  04 | Instruction number | 1
  2 |  08 | Start time | 1543452381142 
  3 |  16 | End time | 1543452391148 
  4 |  24 | Oplet ('r') | 114
  5 |  32 | ERROR | 0
  6 |  40 | PAYLOAD_LENGTH | 62
  7-|  48-| PAYLOAD_DATA | "0x5960000\r\n0x6540000\r\n0x47e0000\r\n0x46a0000\r\n0x3f20000\r\n0x3e800"

Note that from address 48 on to 110, we are using the BYTE form of the data, NOT the Integer form. Also, note that the PAYLOAD_LENGTH in integer 6 is 62 which is the MAX_CONTENT_CHARS value. This indicates that the entire file has NOT been read, and triggers another request, with an increased block number after the 'r' oplet.

Int | Address | Description | Sample
--- | --- | --- | ---
  0 |  00 | Job number | 1
  1 |  04 | Instruction number | 2
  2 |  08 | Start time | 1543452381197 
  3 |  16 | End time | 1543452382199 
  4 |  24 | Oplet ('r') | 114
  5 |  32 | ERROR | 0
  6 |  40 | PAYLOAD_LENGTH | 48
  7-|  48-| PAYLOAD_DATA | "00\r\n0x4920000\r\n0x73a0000\r\n0x5000000\r\n0x51e0000\r\n"

Note that the PAYLOAD_LENGTH of 48 is less than MAX_CONTENT_CHARS and so we have completed reading the file. Concatenating the second returned PAYLOAD_DATA string to the first results in the complete original file content.

The incoming socket handler should not assume that each packet of data recieved is an 'r' as ['g' status](status-data) responses will also be recieved. Check the integer 4 Oplet value and process each type of data accordingly. 

*Note that this release of DexRun.c requires an updated [FPGA image](SD-Card-Image). Contact us for that file before updating the code.