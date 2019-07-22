
- Most oplets will return a [status](status data) with a standard error code (see list below)
- [Read From Robot](read from robot) and [Write To Robot](write to robot) deal with file IO and so return the [standard Linux error numbers](https://github.com/torvalds/linux/blob/master/include/uapi/asm-generic/errno-base.h) 
- The Monitor can override any of the above, replacing the status error with a number in the 600-699 range. 

 No | Description
--- | -------------------
 0  | **No error**
 1  | **Unknown** Most often returned when the requested oplet or sub command is not known. This is most likely to occur because the [firmware](Firmware) on the robot is out of date, and it should be [updated](../../Firmware#readme).
665 | **Boundary error** A joint was commanded to move past the boundary set for it, [Read](read from robot) the errors.log file for details. There are high and low boundary [settings](set-parameter-oplet) for each joint: J#Boundry(High|Low). These are specified in the Defaults.make_ins file, or can be overridden via [SetParmeter](set-parameter-oplet) or the "b" [Oplet](Command-oplet-instruction)
666 | **Monitor error** Raw encoder position or velocity exceeded, [Read](read from robot) the errors.log file for details. If the error was velocity related, the robot will be switched into open loop mode from any closed loop mode.