In most cases, you will control Dexter and other robots, or peripheral including serial devices from [DDE](dde) on a PC. That allows you to bring the power of the PC and the DDE IDE to bear on the overall control problem. However, in some cases you might want to connect something directly to Dexter and run a job via the [Job Engine, which is DDE running on Dexter](DDE#job-engine-on-dexter) vs DDE running on the PC. 

Serial devices, such as TTL serial adapters, ModBus adapters, or other devices with a serial interface like the OpenMV camera can be connected to Dexter's USB A connector, found just under the CAT5 connector on the MicroZed board. 

To verify the connection, you will need to SSH in or connect via the USB console cable.

Once you are at the command prompt on Dexter, with the device _disconnected_ type the following commands (bolded in the sample session below):

<br><tt><B>ls /dev/co*</B></tt>
<br><tt>/dev/console</tt>
<br><tt><B>ls /dev/ttyUSB*</B></tt>

Now, plug in the device and wait a few moments, then enter those same commands again. In this case, I've plugged in a simple USB - TTL adapter based on the CP2102 chip. Look for the difference:

<br><tt><B>ls /dev/co*</B></tt>
<br><tt>/dev/console</tt>
<br><tt><B>ls /dev/ttyUSB*</B></tt>
<br><tt>/dev/ttyUSB0</tt>

As you can see, the new device is there at `/dev/ttyUSB0`. 

Arduino Unos may show up as `/dev/ttyACM0`.

To test it's operation, you can even use the command line to send and receive data. e.g. Here is a sample session for an Arduino device programmed to respond with some binary data and a few analog port readings in response to a "?". You can find the code for that here: 
https://gist.github.com/JamesNewton/8b994528ff3ce69e60bbb67c40954cd2

````
stty -echo -F /dev/ttyUSB0 ospeed 57600 ispeed 57600 raw
echo -ne '?' > /dev/ttyUSB0
cat -v < /dev/ttyUSB0
````
In that session, the following showed up at the console:
`<10000000000000,470,419,423,389,362,343>^M`
which is exactly what the device was programmed to return.

You can have a two way conversation via typing in data and seeing the result via:

````
stty -F /dev/ttyUSB0 ospeed 57600 ispeed 57600 raw
cat /dev/ttyUSB0 & cat > /dev/ttyUSB0
````
Press Ctrl+Z to exit.

Next, we want to use the "serial" functions in the ["job engine"; DDE on Dexter)](DDE#job-engine-on-dexter) but there have been issues which require a little hacking depending on the version you have:<br>
https://github.com/HaddingtonDynamics/Dexter/issues/60#issuecomment-605376411