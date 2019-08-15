In DDE, robot.js, the [Dexter.write_to_robot](https://github.com/cfry/dde/search?utf8=%E2%9C%93&q=Dexter.write_to_robot+filename%3Arobot.js&type=) function writes file data to the Dexter filesystem via the capital 'W' [oplet](Command-oplet-instruction). 
Subcommands:
* f: File name. e.g. <BR>`make_ins("W", "f", 0, file_name)`
* <strike>s: Start file data. <BR>`make_ins("W", "s", max_content_chars, a_string.substring(next_start_index, max_content_chars))`</strike> _not used_
* m: Middle file data. <BR>`make_ins("W", "m", max_content_chars, a_string.substring(next_start_index, next_start_index + max_content_chars))`
* e: End File data. <BR>`make_ins("W", "e", chars_left, a_string.substring(next_start_index, a_string.length))`

Binary data is supported by standard URL escaping. E.g. a binary value of 0 is written in the data as %00. This decreases the payload size by a factor of three, but is worth doing for binary file support. In addition, any text data with a ';' (semicolon, ASCII 59 0x3B) must be escaped to avoid confusion with the ending character in a command, and the escape letter '%' (percent, ASCII 37 0x25) must be escaped as well. Other than null, ; and %, other binary data actually shouldn't need to be escaped. 

### History
The code as it was first released starts at:
https://github.com/cfry/dde/blob/99e37f9f466b3c374953581aefde0e27e1582fcd/robot.js#L1585
At that time, there was no function in the DexRun.c [Firmware](Firmware) to process these commands on the Dexter side. That was added in this release:
https://github.com/HaddingtonDynamics/Dexter/commit/19d08c26abacf4c81fc2c5427b03adc24d37b51b

Because certain characters need to be escaped, the DDE code had to be changed and the MAX_CONTENT_CHARS had to be reduced by a factor of 3. The following, placed in dde_init.js in the dde_apps document folder allowed binary transfers to Dexters file system given good connectivity. There are concerns about packet order, but it generally seems to work reliably. All of this has since been implemented in DDE via the Dexter.write_to_robot and write_file commands.

````
Dexter.socket_encode = function(c) {
	let hexchars = "0123456789ABCDEF"
    let high_nibble = (c / 16) & 0x0F
    let low_nibble = c & 0x0F
    return "%" + hexchars[high_nibble] + hexchars[low_nibble]
}

Dexter.write_to_robot = function(a_string="", file_name=null){
    let max_content_chars = 62 //244 //252 //ie 256 - 4 for (instruction_id, oplet, suboplet, length
    //Limiting to 62 bytes makes it easy to avoid overrunning the packet when all bytes are escaped.
    //About 3 times faster processing can be achieved with more intelligent escaping / breaking
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


