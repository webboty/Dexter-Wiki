
Encoders provide closed loop feedback of the actual position of each [joint](Joints). Dexters joint encoders use low res, 3D printed disks with 200 or fewer slots. But instead of sensing only black / white as each slot passes the sensors, local 12 bit A2D converters sense 8,000 levels of grey (4096 up, 4096 down) as each slot exposes or occludes the light. That data is sent back to the [FPGA](Gateware), which processes it to find the fraction of an angle the disk has moved within that slot. The result is over 1 MILLION positions / joint rotation. 50 micron repeatability. Under 10 micron step precision. And, variations in the slots characterize the wheel, allowing absolute positioning.

<img src="https://raw.githubusercontent.com/HaddingtonDynamics/Dexter/master/Hardware/EncoderOverview.png">

There are two light sources and sensors for each disk to provide quadrature directional data. These are placed such that the signals generated are 90 degrees out of phase with each other. This makes them sin and cosine. If they are plotted on a graph, as each slot passes the sensors, they form a circle. 

During calibration, the signals are recorded in RAM and transferred to the HiMem.dta and memText.txt files on the disk via the ["load_tables" oplet](Command-oplet-instruction) which actually stores the data from the tables. HiMem.dta is binary, the text version of this file is memText.txt. On startup, RAM is automatically [loaded from HiMem.dta](https://github.com/HaddingtonDynamics/Dexter/search?q=himem&unscoped_q=HiMem.dta). 

Each value represents a reading from the A2D at a given angle converted to arctangent (perpendicular from the tangent which is sin/cos) and combined with the slot number. Values are a 32 bits. The top 10(?) bits are the slot number and the bottom 20(?) bits are the interpolated data.

The lower bits are divided by 2^20th(?) to make a fraction. This the fractional part is offset by a tiny amount 1/2^20(?). This introduces a 1 bit error, but avoids the arctangent singularities at 90 and 180 degrees.

The axis data for all the joints are stored sequentially, separated by address. The top 12 bits of the 32 bit address represent the axis. 3FFF FFFF is the maximum address for DRAM in the FPGA. We use 3F00 0000 to 3FFF FFFF for this data. 3F00 0000 to 3F3F FFFF is the first axis, the next axis starts at 3F40... then 3F8, 3FA, 3FE.

Because there are over a million possible positions for each joint, the entire disk's values will not fit in the FPGA at once. Instead, a smaller "sliding window" table with 2k entries, divided into 4 parts, each 512 entries, is loaded with the part of the data that is currently being indexed.

A single real world value read from the joints A2D converter is matched against that table. And the address in the table is incremented if the value is smaller, decremented if it's larger. As a result it is constantly vibrating between the upper and lower bounds, but this is a very very slight movement and can't possibly be seen in the actual position of the robot.

The measured angle derived from this system, is fed into the motor control logic to produce an error signal, which is compensated for by motor commands.

See also:<br>
https://github.com/JamesNewton/HybridDiskEncoder

