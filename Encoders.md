
Encoders provide closed loop feedback of the actual position of each [joint](Joints). Dexters joint encoders use low res, 3D printed disks with 200 or fewer slots. But instead of sensing only black / white as each slot passes the sensors, local 12 bit A2D converters sense 8,000 levels of grey (4096 up, 4096 down) as each slot exposes or occludes the light. That data is sent back to the [FPGA](Gateware), which processes it to find the fraction of an angle the disk has moved within that slot. The result is over 1 MILLION positions / joint rotation. 50 micron repeatability. Under 10 micron step precision. And, variations in the slots characterize the wheel, allowing absolute positioning.

<img src="https://raw.githubusercontent.com/HaddingtonDynamics/Dexter/master/Hardware/EncoderOverview.png">

Here you can see the slot passing under the mask. Notice the variation in light level inside the hole. This change in light level is what we are sensing; not just the passing of the slot, but the change in transmitted light as the slot passes.<BR>
![Animation of one encoder sensor as the slot passes the mask.](https://user-images.githubusercontent.com/419392/93278868-a6849900-f77a-11ea-94a1-3a8694d31c93.gif)

### Unit Circle
There are two light sources and sensors for each disk to provide quadrature directional data. These are placed such that the signals generated are 90 degrees out of phase with each other. In this animation, you can see the phase difference between the two sensors:<BR>
![Animation of the encoder showing the phase between the two sensors.](https://user-images.githubusercontent.com/419392/93278905-c025e080-f77a-11ea-936e-0685ca073cac.gif)

## DDE Calibration Dialog
This makes them sin and cosine. When they are plotted on a graph in DDE on the Jobs / Calibrate Dexter... window, as each slot passes the sensors, they form a circle: <BR>
![Good encoder, after replacing Sensor in hole of encoder block.](https://user-images.githubusercontent.com/419392/59716213-6d9df300-91ca-11e9-87d6-0b530f39fb61.png)<BR>An acceptable encoder "eye" in the DDE Calibration dialog. 

After selecting your Dexter in step one, you can press Start to begin a full range motion of each joint while the encoder values are sampled and displayed. The dots are color coded but they can be differentiated by position and order of appearance:
- Blue / previous: The blue dot near center indicates where the center is currently set in the robot; where it was previously set and saved. This dot will appear first when selecting a joint.
- Black / circling: The black dots are a sampling of ADC reading and will appear around the circle as the joint moves
- Green / estimated center: A green dot will appear and jump occasionally to show the programs best guess of the center.  
- Red / your click: When you click, you move the red dot to indicate the center of the eye. The is the value that will be written to the robot when you press "Save"

_Dexter HDI robots come pre-calibrated from the factory **Never Save an Eye Calibration on a Dexter HDI!**_

### ADC Centers
The light sources can be adjusted to change their intensity. This changes the maximum readings returned by Analog to Digital Conversion (ADC), thereby expanding the circle. Minimum readings, when the light is blocked, might be zero or slightly higher if any light is leading around the disk. The center of the circle must be marked, and those coordinate values are stored in the AdcCenters.txt file in the /srv/samba/share folder. The easiest way to set those center positions is to use the DDE Calibration dialog, shown above, in step 2, for each joint. This is a long and careful process which _must_ be completed with precision before the robot will calibrate and work correctly in closed loop modes. Follow the directions in DDE to view and adjust the circles, and then set the center point by pressing "Save". Always restart the robot after saving the new configuration. _Dexter HDI robots come pre-calibrated from the factory **Never Save an Eye Calibration on a Dexter HDI!**_

On startup those AdcCenters values are read in by the firmware and set into the FPGA. The robot _must_ be restarted (power cycled) after the file is updated.

_Note: Development of the FPGA code started in the middle and so the numbering of the [joints](Joints) is not consistent. Specifically, when reading (or writing) the AdcCenters.txt file from the [share](Dexter-Networking#file-sharing) folder, the 2nd and 4th, and 3rd and 5th, lines must be swapped_

### Calibration
During [movement calibration](Encoder-Calibration) (which is NOT the same as the eye calibration we are discussing here), the signals are recorded in RAM and transferred to the HiMem.dta and memText.txt files on the disk via the ["load_tables" oplet](Command-oplet-instruction) which actually stores the data from the tables. HiMem.dta is binary, the text version of this file is memText.txt. On startup, RAM is automatically [loaded from HiMem.dta](https://github.com/HaddingtonDynamics/Dexter/search?q=himem&unscoped_q=HiMem.dta). 

Each value represents a reading from the A2D at a given angle converted to arctangent (perpendicular from the tangent which is sin/cos) and combined with the slot number. Values are a 32 bits. The top 10(?) bits are the slot number and the bottom 20(?) bits are the interpolated data.

The lower bits are divided by 2^20th(?) to make a fraction. This the fractional part is offset by a tiny amount 1/2^20(?). This introduces a 1 bit error, but avoids the arctangent singularities at 90 and 180 degrees.

The axis data for all the joints are stored sequentially, separated by address. The top 12 bits of the 32 bit address represent the axis. 3FFF FFFF is the maximum address for DRAM in the FPGA. We use 3F00 0000 to 3FFF FFFF for this data. 3F00 0000 to 3F3F FFFF is the first axis, the next axis starts at 3F40... then 3F8, 3FA, 3FE.

### Sliding Window
Because there are over a million possible positions for each joint, the entire disk's values will not fit in the FPGA at once. Instead, a smaller "sliding window" table with 2k entries, divided into 4 parts, each 512 entries, is loaded with the part of the data that is currently being indexed.

### Table Lookup
A single real world value read from the joints A2D converter is matched against that table. And the address in the table is incremented if the value is smaller, decremented if it's larger. As a result it is constantly vibrating between the upper and lower bounds, but this is a very very slight movement and can't possibly be seen in the actual position of the robot.

The measured angle derived from this system, is fed into the motor control logic to produce an error signal, which is compensated for by motor commands.

See also:<br>
https://github.com/JamesNewton/HybridDiskEncoder

