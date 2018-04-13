In DDE, robot.js, the [Dexter.write_to_robot](https://github.com/cfry/dde/search?utf8=%E2%9C%93&q=Dexter.write_to_robot+filename%3Arobot.js&type=) function writes file data to the Dexter filesystem via the capital 'W' [oplet](Command-oplet-instruction). The code currently starts at:
https://github.com/cfry/dde/blob/master/robot.js#L1479

Subcommands:
* f: File name. e.g. <BR>`make_ins("W", "f", 0, file_name)`
* s: Start file data. <BR>`make_ins("W", "s", max_content_chars, a_string.substring(next_start_index, max_content_chars))`
* m: Middle file data. <BR>`make_ins("W", "m", max_content_chars, a_string.substring(next_start_index, next_start_index + max_content_chars))`
* e: End File data. <BR>`make_ins("W", "e", chars_left, a_string.substring(next_start_index, a_string.length))`