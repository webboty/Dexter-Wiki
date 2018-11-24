## Kinematics
Convert XYZ to [Joint](Joints) Angles for J1, J2, J3, J4, and J5. Dexter follows the common convention is that Z is up / down, X is right / left, and Y is out / back. (see picture below)

In [DDE](DDE) we can `move_to(X, Y, Z, direction, config)` where 
- X, Y, and Z are the destination in meters, 
- `direction` is an array of unit vectors [X, Y, Z]. e.g. `[0, 0, -1]` is straight down,
- `config` is an array of boolean options [right_arm, elbow_up, wrist_out]. See below for more.

Dexter also directly supports the 'M' and 'T' [oplets](command-oplets) which take the same parameters, but the X, Y, Z units are integer microns, aka "micrometers", or a millionths of a meter (1/1,000,000 meters).

<img src="https://github.com/JamesNewton/AdvancedRoboticsWithJavascript/blob/master/docs/Configurations.png?raw=true" width=317 height=327 align="right">

**config** For most points within the reachability of Dexter, there are multiple ways in which Dexter can get there. For example, This picture shows all the ways Dexter might get to the same point (on the sloped face of the grey cube). There is also a [(really big) version that shows them all seperated](https://raw.githubusercontent.com/cfry/dde/master/doc/coor_images/Configurations.png). `config` helps you specify your preference as to how Dexter configures its joints to get to the indicated x, y, z. There are 3 independent boolean values to determining this configuration. They are:
- **base-rotation** `right_arm`: LEFT or 0 means J1, the base joint has a _negative_ value, ie it is to the right if you are facing Dexters front. Looking down from the top at Dexter this would mean its rotated counter clockwise. The opposite direction is RIGHT, or 1 which is clockwise, to the left facing the robot.
- **J3-position** `elbow_up`: Joint 3 can be thought of as Dexter's elbow. The elbow can be either UP i.e. away from the table or DOWN, closer to the table.
- **J4 Position** `wrist_out`: Joint 4 / 5 can be thought of as Dexter's wrist. It can be pointing either IN, towards the base, or OUT, away from the base.

**Direction** controls what direction the last part of the arm is pointing. It will be at X, Y, Z, but should it be pointing straight down? [0, 0, -1] does that. Or should it be pointed out? [0, 1, 0] does that. Or maybe you want it pointed in? [0, -1, 0]. You can also use an array of 2 values in DDE, which will be used as pitch and roll angles, but be careful not to use 90 as _both_ of the angles because it will cause a "singularity"

**Singularity**  "A point at which a function takes an infinite value" In many cases, there are more than one way to get to the target. If you specify [90,90] for wrist pitch and roll, there are an infinite number of ways to get to that direction.

<img src="https://raw.githubusercontent.com/cfry/dde/master/doc/coor_images/Direction_Vector.PNG">
