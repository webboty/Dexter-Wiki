Errors that occur on Dexter are communicated in the next returned packet.
- Most oplets will return a [status](status-data) with a standard error code (see list below)
- [Read From Robot](read-from-robot) and [Write To Robot](write-to-robot) deal with file IO and so return the [standard Linux error numbers](https://github.com/torvalds/linux/blob/master/include/uapi/asm-generic/errno-base.h) 
- The Monitor can override any of the above, replacing the status error with a number in the 600-699 range. In that case, it is possible that a prior error may be overwritten. E.g. If a monitor error occurs during a Read From Robot or Write To Robot which also generated an error, that error would be lost. Any such operation should be restarted once the monitor error is resolved. 

 No | Description
--- | -------------------
 0  | **No error**
 1  | **Unknown** Most often returned when the requested oplet or sub command is not known. This is most likely to occur because the [firmware](Firmware) on the robot is out of date, and it should be [updated](../../Firmware#readme).
 2  | **Bad Request** The request was incorrectly formatted. In practice, this means there was no ';' at the end of the last socket data. Any other malformation is unlikely to be detected. 
 3? | **Firmware - Gateware Mismatch** The DexRun.c [firmware](Firmware) and xillydemo.bit [gateware](Gateware) files do not match. See [Issue 68](https://github.com/HaddingtonDynamics/Dexter/issues/68) for more information. _Note: This error code is not yet implemented_
665 | **Boundary error** A joint was commanded to move past the boundary set for it, [Read](read-from-robot) the errors.log file for details. There are high and low boundary [settings](set-parameter-oplet) for each joint: J#Boundry(High|Low). These are specified in the Defaults.make_ins file, or can be overridden via [SetParmeter](set-parameter-oplet) or the "b" [Oplet](Command-oplet-instruction)
666 | **Monitor error** Raw encoder position or velocity exceeded, [Read](read-from-robot) the errors.log file for details. If the error was velocity related, the robot will be switched into open loop mode from any closed loop mode under the assumption that the cause is a bad [encoder](Encoder-Calibration).