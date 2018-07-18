In DDE, robot.js, the [Dexter.write_to_robot](https://github.com/cfry/dde/search?utf8=%E2%9C%93&q=Dexter.write_to_robot+filename%3Arobot.js&type=) function writes file data to the Dexter filesystem via the capital 'W' [oplet](Command-oplet-instruction). The code as it was first released starts at:
https://github.com/cfry/dde/blob/99e37f9f466b3c374953581aefde0e27e1582fcd/robot.js#L1585

Subcommands:
* f: File name. e.g. <BR>`make_ins("W", "f", 0, file_name)`
* <strike>s: Start file data. <BR>`make_ins("W", "s", max_content_chars, a_string.substring(next_start_index, max_content_chars))`</strike> _not used_
* m: Middle file data. <BR>`make_ins("W", "m", max_content_chars, a_string.substring(next_start_index, next_start_index + max_content_chars))`
* e: End File data. <BR>`make_ins("W", "e", chars_left, a_string.substring(next_start_index, a_string.length))`

In the original version, there is no function in the DexRun.c [Firmware](Firmware) to process these commands on the Dexter side. That has been added in this release:
https://github.com/HaddingtonDynamics/Dexter/commit/19d08c26abacf4c81fc2c5427b03adc24d37b51b

Because certain characters need to be escaped, the DDE code must be changed. The following, placed in dde_init.js in the dde_apps document folder will allow binary transfers to Dexters file system given good connectivity. There are concerns about packet order, but so far it seems to work reliably.

````
Dexter.socket_encode = function(c) {
	let hexchars = "0123456789ABCDEF"
    let high_nibble = (c / 16) & 0x0F
    let low_nibble = c & 0x0F
    return "%" + hexchars[high_nibble] + hexchars[low_nibble]
}

Dexter.write_to_robot = function(a_string="", file_name=null){
    let max_content_chars = 64 //244 //252 //ie 256 - 4 for (instruction_id, oplet, suboplet, length
    let i = 0
    let code = 0
    let payload = ""
    let instrs = []
    debugger
    if (file_name){
        instrs.push(make_ins("W", "f", 0, file_name))
    	}
	while (i < a_string.length) {
    	code = a_string[i].charCodeAt(0) //numerical ASCII value of current character. 
        if ((0x00 == code || 0x3B == code || 0x25 == code)) {
	        payload += Dexter.socket_encode(code)
            }
        else {
        	payload += a_string[i]
            }
        if (payload.length >= max_content_chars) {
	        instrs.push(make_ins("W", "m", payload.length, payload))
            payload = ""
            }
        i++
    	}
    instrs.push(make_ins("W", "e", payload.length, payload)) //close the file
    return instrs
	}
````

In the future, subcommands to retrieve file data will also be needed. These might be added under the 'r' (not 'R') oplet. Suggestions:
* f: File name. As above to set the file name or directory path to be read
* r: Read data. Reads the next block of data. Dexter will return an error when an attempt is made to read past end of file.

At this time, DexRun.c only returns the robot status data to DDE, and DDE only expects status data, so both will have to be extended to handle returned file data.
