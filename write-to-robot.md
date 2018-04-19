In DDE, robot.js, the [Dexter.write_to_robot](https://github.com/cfry/dde/search?utf8=%E2%9C%93&q=Dexter.write_to_robot+filename%3Arobot.js&type=) function writes file data to the Dexter filesystem via the capital 'W' [oplet](Command-oplet-instruction). The code currently starts at:
https://github.com/cfry/dde/blob/master/robot.js#L1479

Subcommands:
* f: File name. e.g. <BR>`make_ins("W", "f", 0, file_name)`
* s: Start file data. <BR>`make_ins("W", "s", max_content_chars, a_string.substring(next_start_index, max_content_chars))`
* m: Middle file data. <BR>`make_ins("W", "m", max_content_chars, a_string.substring(next_start_index, next_start_index + max_content_chars))`
* e: End File data. <BR>`make_ins("W", "e", chars_left, a_string.substring(next_start_index, a_string.length))`

In the original version, there is no function in the DexRun.c [Firmware](Firmware) to process these commands on the Dexter side. That has been added in this release:
https://github.com/HaddingtonDynamics/Dexter/commit/ef39cfe6effd36faeb9bd96b21a11a3dcd6b84ef

In the future, subcommands to retrieve file data will also be needed. Suggestions:
* f: File name. As above to set the file name or directory path to be read
* r: Read data. Reads the next block of data. Dexter will return an error when an attempt is made to read past end of file.

At this time, DexRun.c only returns the robot status data to DDE, and DDE only expects status data, so both will have to be extended to handle returned file data.
