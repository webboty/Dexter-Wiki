Movement of any robot depends on it's motors or other actuators. In Dexter, there are stepper motors on each axis. But we have a complex and powerful [FPGA](Gateware) based control system which integrates existing position, commanded position, forces, and time in order to provide fantastic accuracy and response times. 

In general, all you need to do is tell Dexter where to move each [joint](Joints) and they will get to exactly that position or die trying. But sometimes you want to change the goal position during a movement, or you want to bring the end effector to a Cartesian position and not worry about the joint angles. 

Here is a summary of the different movement methods, including related parameters, settings you can change in the environment that affect that method, and its pros and cons. 

| [Oplet](Command-oplet-instruction) | Parameters | Settings | Pros | Cons  |
| ----- | ---------- | ----------- | ---- | ----- |
| ['a' MOVEALL](Command-oplet-instruction#a) | 5-7 joint angles<br>(arcseconds) | [Acceleration](set-parameter-oplet#Acceleration)<BR>[MaxSpeed](set-parameter-oplet#MaxSpeed)<BR>[StartSpeed](set-parameter-oplet#StartSpeed) | Coordinated (joints finish together),<BR>Trapezoidal Ramping joints 1-5,<BR>Works w/o Cal (at lower precision)<br>Good for point to point motion | Can not change goal,<BR>No Trapezoidal Ramping on joint 6-7,<br>No separate ending speed |
| ['M' MOVETO](Command-oplet-instruction#M) | XYZ (integer microns)<br>XYZ direction (unit vector)<br>configuration (booleans).  | [Acceleration](set-parameter-oplet#Acceleration)<BR>[MaxSpeed](set-parameter-oplet#MaxSpeed)<BR>[StartSpeed](set-parameter-oplet#StartSpeed) | Onboard Kinematics<br>Coordinated (straight line),<BR>Trapezoidal Ramping joints 1-5,<BR>Works w/o Cal (at lower precision)<br>Good for point to point motion  | Can not change goal,<BR>No Trapezoidal Ramping on joint 6-7,<br>No separate ending speed |
| ['P' PID_MOVE_ALL_JOINTS](Command-oplet-instruction#p) | 5-7 joint angles<br>(arcseconds) | [PID_P](set-parameter-oplet#JointPID),<br>PIDMaxSpeed via [`w, 36`](oplet-write) | Executes immediately,<BR>PID feedback loop based ramping,<BR>smooth deceleration.<BR>Good for smooth continuous motion through path and real-time control<BR>&nbsp; | Non-coordinated (joints finish independently),<BR>non-smooth acceleration,<br>requires sleeps between commands<br>Requires Cal |
| ['C' PID_MOVE_TO](Command-oplet-instruction#p) | XYZ (integer microns)<br>XYZ direction (unit vector)<br>configuration (booleans). | [PID_P](set-parameter-oplet#JointPID),<br>PIDMaxSpeed via [`w, 36`](oplet-write) | Since [2019.10.10](https://github.com/HaddingtonDynamics/Dexter/commit/4c506d60a42ed31d4acc35f74e6f6262ac6a78e5) Executes immediately,<BR>PID feedback loop based ramping,<BR>smooth deceleration.<BR>Good for smooth continuous motion through path and real-time control<BR>&nbsp; | Non-coordinated (joints finish independently),<BR>non-smooth acceleration,<br>requires sleeps between commands<br>Requires Cal |

## Kinematics
Convert XYZ to [Joint](Joints) Angles for J1, J2, J3, J4, and J5. Dexter follows the common convention is that Z is up / down, X is right / left, and Y is out / back. (see picture below)

**In [DDE](DDE):** We can `move_to(X, Y, Z, direction, config)` where 
- X, Y, and Z are the destination in meters, 
- `direction` is an array of unit vectors [X, Y, Z]. e.g. `[0, 0, -1]` is straight down,
- `config` is an array of boolean options [right_arm, elbow_up, wrist_out]. See below for more.
and DDE will use it's understanding of the Link Lengths and Dexters shape to calculate the joint angles to reach this point. Those values should be checked for accuracy to improve the accuracy of the resulting position.

**Onboard:** Current firmware also directly supports the 'M' and 'T' [oplets](command-oplets) for onboard kinematics, which take the same parameters, but the X, Y, Z units are integer microns, aka "micrometers", or a millionths of a meter (1/1,000,000 meters). The link lengths for this calculation are set via the [Link Lengths](set-parameter-oplet#LinkLengths) Set Parameter command and can be stored in the Defaults.make_ins file for automatic setting on robot startup. Note that Length 5 (the length of the tool interface and end effector) is set /first/ in that command, and it will update as many lengths as you give it, so you can easily change the end effector length on the fly by specifying only it's length. 

The current Cartesian position and orientation can be retrieved from the robot via the ['r' Read from robot oplet, and the #POM keyword](https://github.com/HaddingtonDynamics/Dexter/wiki/read-from-robot#keywords). 

<img src="https://github.com/JamesNewton/AdvancedRoboticsWithJavascript/blob/master/docs/Configurations.png?raw=true" width=317 height=327 align="right">

**config** For most points within the reachability of Dexter, there are multiple ways in which Dexter can get there. For example, This picture shows all the ways Dexter might get to the same point (on the sloped face of the grey cube). There is also a [(really big) version that shows them all separated](https://raw.githubusercontent.com/cfry/dde/master/doc/coor_images/Configurations.png). `config` helps you specify your preference as to how Dexter configures its joints to get to the indicated x, y, z. There are 3 independent boolean values to determining this configuration. They are:
- **base-rotation** `right_arm`: LEFT or 0 means J1, the base joint has a _negative_ value, ie it is to the right if you are facing Dexters front. Looking down from the top at Dexter this would mean its rotated counter clockwise. The opposite direction is RIGHT, or 1 which is clockwise, to the left facing the robot.
- **J3-position** `elbow_up`: Joint 3 can be thought of as Dexter's elbow. The elbow can be either UP i.e. away from the table or DOWN, closer to the table.
- **J4 Position** `wrist_out`: Joint 4 / 5 can be thought of as Dexter's wrist. It can be pointing either IN, towards the base, or OUT, away from the base.

**Direction** controls what direction the last part of the arm is pointing. It will be at X, Y, Z, but should it be pointing straight down? [0, 0, -1] does that. Or should it be pointed out? [0, 1, 0] does that. Or maybe you want it pointed in? [0, -1, 0]. You can also use an array of 2 values in DDE, which will be used as pitch and roll angles, but be careful not to use 90 as _both_ of the angles because it will cause a "singularity"

**Singularity**  "A point at which a function takes an infinite value" In many cases, there are more than one way to get to the target. If you specify [90,90] for wrist pitch and roll, there are an infinite number of ways to get to that direction. A good starting point for xyz is: [0, 0.5, 0.075].

<img src="https://raw.githubusercontent.com/cfry/dde/master/doc/coor_images/Direction_Vector.PNG">

See also: [Dynamics](Dynamics)
