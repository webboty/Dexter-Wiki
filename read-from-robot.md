This allows data to be read from the Dexter file system without having to use a SAMBA share (which was blocked by Windows 10). Support was added in [DexRun.c TDint branch](https://github.com/HaddingtonDynamics/Dexter/commit/243ac0fa3c995effd9c75731d3a9c7ecb70cc73e)* as the 'r' oplet (see below for more) and in [DDE version 2.3.16, June 4, 2018](https://github.com/cfry/dde/releases/tag/untagged-5d86b61c13b61d266905) as Dexter.read_from_robot(pathfile, user_variable) where 
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

In DexRun.c, this required that we implement separate return data for the 'r' oplet. Previously, all commands always return the 'g' status data. This is the first command to return something different. In this case, it returns the contents of a file from the robots file system, in blocks of MAX_CONTENT_CHARS bytes. The parameters for the 'r' command are the block number to read, and the file name, both as null terminated strings. The block number is in human readable ASCII format and should start with 0 then increase until fewer than MAX_CONTENT_CHARS bytes are returned. 

The returned data includes the number of bytes actually read as a binary integer and then the actual bytes of data from the file starting from the block number times MAX_CONTENT_CHARS for a length of MAX_CONTENT_CHARS or less if the end of the file is reached.

*Note that this release of DexRun.c requires an updated FPGA image. Contact us for that file before updating the code.