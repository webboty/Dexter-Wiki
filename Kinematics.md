Movement of any robot depends on it's motors or other actuators. In Dexter, there are stepper motors on each axis. But we have a complex and powerful [FPGA](Gateware) based control system which integrates existing position, commanded position, forces, and time in order to provide fantastic accuracy and response times. 

In general, all you need to do is tell Dexter where to move each [joint](Joints) and they will get to exactly that position or die trying. But sometimes you want to change the goal position during a movement, or you want to bring the end effector to a Cartesian position and not worry about the joint angles. _Note: Cartesian positions have their own complexities; requiring [orientation](direction) and [configuration](configuration) to be specified, and [singularities](singularities ) to be avoided._

Here is a summary of the different movement methods, including related parameters, settings you can change in the environment that affect that method, and its pros and cons. 

| [Oplet](Command-oplet-instruction) | Parameters | Settings | Pros | Cons  |
| ----- | ---------- | ----------- | ---- | ----- |
| ['a' MOVEALL](Command-oplet-instruction#a) | 5-7 joint angles<br>(arcseconds) | [Acceleration](set-parameter-oplet#Acceleration)<BR>[MaxSpeed](set-parameter-oplet#MaxSpeed)<BR>[StartSpeed](set-parameter-oplet#StartSpeed) | Coordinated (joints finish together),<BR>Trapezoidal Ramping joints 1-5,<BR>Works w/o Cal (at lower precision)<br>Good for point to point motion | Can not change goal,<BR>No Trapezoidal Ramping on joint 6-7,<br>No separate ending speed |
| ['M' MOVETO](Command-oplet-instruction#M) | XYZ (integer microns)<br>XYZ direction (unit vector)<br>configuration (booleans).  | [Acceleration](set-parameter-oplet#Acceleration)<BR>[MaxSpeed](set-parameter-oplet#MaxSpeed)<BR>[StartSpeed](set-parameter-oplet#StartSpeed) | Onboard <a href="#inverse-kinematics">Kinematics</a><br>Trapezoidal Ramping joints 1-5,<BR>Works w/o Cal (at lower precision)<br>Good for point to point motion  | Can not change goal,<br>non-coordinated (not straight line),<BR>No Trapezoidal Ramping on joint 6-7,<br>No separate ending speed |
| ['T' MOVETOSTRAIGHT](Command-oplet-instruction#T) | XYZ (integer microns)<br>XYZ direction (unit vector)<br>configuration (booleans).  | [Acceleration](set-parameter-oplet#Acceleration)<BR>[MaxSpeed](set-parameter-oplet#MaxSpeed)<BR>[StartSpeed](set-parameter-oplet#StartSpeed) | Onboard <a href="#inverse-kinematics">Kinematics</a><br>Coordinated (straight line),<BR>Trapezoidal Ramping joints 1-5,<BR>Works w/o Cal (at lower precision)<br>Good for point to point motion  | Can not change goal,<BR>No Trapezoidal Ramping on joint 6-7,<br>No separate ending speed |
| ['P' PID_MOVE_ALL_JOINTS](Command-oplet-instruction#p) | 5-7 joint angles<br>(arcseconds) | [PID_P](set-parameter-oplet#JointPID),<br>PIDMaxSpeed via [`w, 36`](oplet-write) | Executes immediately,<BR>PID feedback loop based ramping,<BR>smooth deceleration.<BR>Good for smooth continuous motion through path and real-time control<BR>&nbsp; | Non-coordinated (joints finish independently),<BR>non-smooth acceleration,<br>requires sleeps between commands<br>Requires Cal |
| ['C' PID_MOVE_TO](Command-oplet-instruction#p) | XYZ (integer microns)<br>XYZ direction (unit vector)<br>configuration (booleans). | [PID_P](set-parameter-oplet#JointPID),<br>PIDMaxSpeed via [`w, 36`](oplet-write) | Since [2019.10.10](https://github.com/HaddingtonDynamics/Dexter/commit/4c506d60a42ed31d4acc35f74e6f6262ac6a78e5) <br>Onboard <a href="#inverse-kinematics">Kinematics</a><br>Executes immediately,<BR>PID feedback loop based ramping,<BR>smooth deceleration.<BR>Good for smooth continuous motion through path and real-time control<BR>&nbsp; | Non-coordinated (joints finish independently),<BR>non-smooth acceleration,<br>requires sleeps between commands<br>Requires Cal |

## Inverse Kinematics
To move the end effector to a desired Cartesian point, we must convert the points' X, Y, and Z values to [Joint](Joints) Angles for J1, J2, J3, J4, and J5. This is called "Inverse Kinematics". _Note: Dexter follows the common convention is that Z is up / down, X is right / left, and Y is out / back. (see picture below)._ Doing this computation correctly also requires that we know the correct lengths between each "link" on the robot. For example, L1 is from the mounting point to the axis of Joint 2 (J2). L2 is from J2 to J3, L3 from J3 to J4, L4 from J4 to J5, and L5 is from the axis of J5 to the tip of the end effector. This IK computation can be done by the control software, such as DDE, or onboard by Dexter. To specify the desired position and orientation, we use:

**`Position, Direction, Configuration`**

- <a href="#position">`Position`</a> [X, Y, and Z] indicate the destination point, but this isn't enough information;
- <a href="#direction">`Direction`</a> or "orientation" is an array of unit vectors [X, Y, Z] which specify the direction to point the end effector. e.g. `[0, 0, -1]` is straight down. See below for more information.
- <a href="#configuration">`Configuration`</a> is an array of boolean options [right_arm, elbow_up, wrist_out] for how we should reach that point. See below for more.

### Position
aka "**[X, Y, Z]**" this is the position that the tip of the end effector (assuming a correct LinkLength for L5) will be positioned at. It is not the position of the differential, or the end of the 5 axis, unless L5 is set to a very small value, e.g. 1 micron. Note that not every XYZ position can be reached, and that moving from one position to another can pass into unreachable areas, or cause the rotational speed of a joint to exceed maximums. A good starting point is: [0, 0.5, 0.075] and short movements around that point should be possible.

### Direction 
This controls what direction the last part of the arm is pointing. It will be at X, Y, Z, but should it be pointing straight down? [0, 0, -1] does that. Or should it be pointed out? [0, 1, 0] does that. Or maybe you want it pointed in? [0, -1, 0]. 

You can send a direction vector that has a larger than 1 magnitude.
The code will convert this to a unit vector for you, this is called 'normalizing'.
If you pass in 
[3, 3, 3]
it will convert it to 
[3, 3, 3] / sqrt(3^2+3^2+3^2)
or 
[0.57735, 0.57735, 0.57735]

#### Computing Direction
The following JavaScript code from DDE's [Kin.js](https://github.com/cfry/dde/blob/master/math/Kin.js) illustrates how to compute the direction vector from a "pitch" and "roll" angle:

````
Kin.angles_to_dir_xyz = function(x_angle = 0, y_angle = 0){
    if(x_angle.length == 2){ //were we passed an array in the first parameter?
        y_angle = x_angle[1] //if so, get the two angles out of the array.
        x_angle = x_angle[0] //and overwrite the array with the X angle.
    }
    let ZX_plane = [0, cosd(y_angle), sind(y_angle)]
    let ZY_plane = [cosd(x_angle), 0, sind(x_angle)]
    if(Vector.is_equal(ZX_plane, ZY_plane) || Vector.is_equal(Vector.multiply(-1, ZX_plane), ZY_plane)){
        dde_error("Direction (" + x_angle +", " + y_angle + ") causes a singularity")
    }
    return Vector.round(Vector.normalize(Vector.cross(ZX_plane, ZY_plane)), 15)
}
````
So, in DDE only, you can also use an array of 2 values for "direction", which will be taken as pitch and roll angles, but be careful not to use 90 as _both_ of the angles because it will cause a "singularity"

#### Singularity
"A point at which a function takes an infinite value" In many cases, there are more than one way to get to the target. If you specify [90,90] for wrist pitch and roll, there are an infinite number of ways to get to that direction. Configuration also helps us to select one set of joint angles when there are many which might reach the same point:

<img src="https://github.com/JamesNewton/AdvancedRoboticsWithJavascript/blob/master/docs/Configurations.png?raw=true" width=317 height=327 align="right">

A true gimbal lock (also called a singularity) will break the math and cause an error. Also positions that are near a singularity can cause strange behavior like large changes in joint angles. An example of a gimbal lock singularity is:
<br>`[0, 0, 4,  0, 0, -1,  1, 1, 1]`

This position is mechanically possible to reach, however because X and Y are both zero, and the tool interface is pointed straight down, the tool tip is perfectly aligned with the axis of J1 and so this position can be achieved with any value for J1. If there are any infinite number of solutions which one should it pick?

Here is another example, which is slightly more complex:
<br>`[L5, 0.3, 4,  0, 1, 0,  1, 1, 1]`

Where L5 is whatever value link 5 is set to. In this position Link5's axis is perpendicular to the plane that L2, L3, and L4 move in; let's call it "Plane 1". In this position the end effector can be rotated about L5's axis to any angle and still achieve the specified X,Y,Z position and direction. In this case three joints J2, J3, and J4 will have infinite solutions and will have large motions when approaching this position.

### Configuration
For most points that Dexter can reach, there are multiple ways in which Dexter can get there. For example, This picture shows all the ways Dexter might get to the same point (on the sloped face of the grey cube). There is also a [(really big) version that shows them all separated](https://raw.githubusercontent.com/cfry/dde/master/doc/coor_images/Configurations.png). `config` helps you specify your preference as to how Dexter configures its joints to get to the indicated x, y, z. There are 3 independent boolean values to determining this configuration. They are:
- **base-rotation** `right_arm`: LEFT or 0 means J1, the base joint has a _negative_ value, ie it is to the right if you are facing Dexters front. Looking down from the top at Dexter this would mean its rotated counter clockwise. The opposite direction is RIGHT, or 1 which is clockwise, to the left facing the robot.
- **J3-position** `elbow_up`: Joint 3 can be thought of as Dexter's elbow. The elbow can be either UP i.e. away from the table or DOWN, closer to the table.
- **J4 Position** `wrist_out`: Joint 4 / 5 can be thought of as Dexter's wrist. It can be pointing either IN, towards the base, or OUT, away from the base.

### DDE
In [DDE](DDE), we can `move_to(X, Y, Z, direction, config)` where 
- X, Y, and Z are the destination _in meters_, 
- `direction` is an array of unit vectors [X, Y, Z]. e.g. `[0, 0, -1]` is straight down,
- `config` is an array of boolean options [right_arm, elbow_up, wrist_out]. See below for more.
and DDE will use it's understanding of the Link Lengths and Dexter's shape to calculate the joint angles to reach this point. Those values should be checked for accuracy to improve the accuracy of the resulting position. Current versions of DDE will read the `S LinkLength` line from Defaults.make_ins on the robot to ensure it is using the robots actual values, rather than it's own default values. 

### Onboard 
Current firmware also directly supports the ['M'](Command-oplet-instruction#M) and ['T'](Command-oplet-instruction#T) [oplets](Command-oplet-instruction) for onboard kinematics, which take the same parameters, but the X, Y, Z units are integer microns, aka "micrometers", or a millionths of a meter (1/1,000,000 meters). The link lengths for this calculation are set via the [Link Lengths](set-parameter-oplet#LinkLengths) Set Parameter command and can be stored in the Defaults.make_ins file for automatic setting on robot startup. Note that Length 5 (the length of the tool interface and end effector) is set /first/ in that command, and it will update as many lengths as you give it, so you can easily change the end effector length on the fly by specifying only that ONE length. 

## Forward Kinematics
In those cases where you want to convert joint angles back into Cartesian XYZ coordinates, we need Forward Kinematics. 

The current Cartesian position and orientation can be retrieved from the robot via the ['r' Read from robot oplet, and the #POM keyword](https://github.com/HaddingtonDynamics/Dexter/wiki/read-from-robot#keywords). 

See also:
- [Dynamics](Dynamics)
- [Spatial Transformation Matrix at MassMind](http://techref.massmind.org/Techref/method/math/spatial-transformations.htm)

<img src="https://raw.githubusercontent.com/cfry/dde/master/doc/coor_images/Direction_Vector.PNG">

![](https://user-images.githubusercontent.com/419392/94041118-a9851800-fd7e-11ea-8a59-1c177d40a7a9.png)

![Link Lengths](https://user-images.githubusercontent.com/26582517/91360228-e0c9ce80-e7aa-11ea-9465-29e0f4eb875d.PNG)

