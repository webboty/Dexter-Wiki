Writes values to the [FPGA / Gateware](Gateware). The second parameter is written to the address of the FPGA specified by the first parameter. e.g. in [DDE](DDE) `make_ins("w", 42, 64)` writes the value 64 into the address 42 in the FPGA. If the address is ACCELERATION_MAXSPEED ( 5 ) then the maxSpeed and coupledAcceleration variables in DexRun will be updated. 


Addr | Function
---- | ---------
5 | ACCELERATION_MAXSPEED 
42 | COMMAND_REG<BR> Bits:<BR>1:CapCalibrateBase<BR>2:CapCalibrateEnd<BR>3:CapCalibratePivot<BR>4: GoMove<BR>5: SelectMoveChange<BR>6: EnableLoop<BR>7: AClrLoop <BR>8: CAL_RUN <BR>9: ResetMotorPosition <BR>10: ResetForce <BR> 13-14: DiffAxisEnable<BR>see <a href="http://JamesNewton.github.io/Dexter.html">cmd calc</a>
79| PID? Timebase Divisor
80| Physics J123? Timebase Divisor
81| Physics J45? Timebase Divisor