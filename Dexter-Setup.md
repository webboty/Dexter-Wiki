## Unpack
Caution: _When following the directions below to remove a Dexter from it's shipping / storage case, please be very careful to rotate each joint in the correct direction to unfold the arm and stand it up straight. This direction should be obvious for the base (joint1) and joints 2 and 3, but joints 4 and 5 can be rotated in the wrong direction and end up 360 degrees from the correct place. Pictures placed in the case when a new Dexter is sent out should indicate the correct directions, but you can also figure them out by looking at what the tool interface will run into as it's rotated. Just make sure the wires don't get wrapped up._

Normally, all that is required is rotating Joint 3, "the elbow" up to a straight vertical position.

## Mount
![](https://user-images.githubusercontent.com/419392/59459216-1c0ff580-8dd1-11e9-8ece-7ac85a8863e8.png)<br>
The top layer of the case will contain the base, remove it and place it with the feet down, and the center mounting pole up. The standard Dexter base is NOT intended to be perfectly stable. It must be mounted to a stable surface by gluing, screwing, or clamping the legs down. 
- If the surface has a lip, you can clamp 2 or 3 of the feet down.<br><img src="https://user-images.githubusercontent.com/419392/58844223-183edf00-862b-11e9-8197-37fc97029a21.png">
- For wooden, metal, or other surfaces which can have holes, screw or bolt the feet down.<br><img src="https://user-images.githubusercontent.com/419392/58844593-c5febd80-862c-11e9-9076-bd438fcaf95d.png">
- For a flat, inviolate surface like steel, marble, cement, etc... use large amounts of hot glue injected up into the bottom of each "foot"<br><img src="https://user-images.githubusercontent.com/419392/58844667-183fde80-862d-11e9-8bcc-aeaed5865306.png"> <img src="https://user-images.githubusercontent.com/419392/58922776-4dae0000-86f1-11e9-9b93-85de3b98551f.png">

- Other options are available for customization, please contact us.

## Assemble
Now remove the Dexter robot from the lower level of the case. <br>
![](https://user-images.githubusercontent.com/419392/59470338-36a39800-8dec-11e9-8b33-f0f3ae1827b8.png)<br>
Typically, you should grip it by the handle on Joint 3 aka "the elbow" (avoid gripping the side of joint 3 which has the belts) and by the base mounting column. Align the strakes with holes in the base mount and slide it in. Tighten the base clamp. It requires a Phillips head screwdriver. 

## Position
Before powering on Dexter, position each joint at the home position. This is less important for the Dexter HDI models as it will find it's own home position when powered up, however, it's faster if it's already closer to home. Prior models will need to be placed as close to home position as is possible using the knobs on the motors. The default position is with all the joints vertical, and the tool interface (the last segment) pointed to the right when facing the joints, as shown here:<br> <img src="https://raw.githubusercontent.com/cfry/dde/master/doc/coor_images/Positive_Joint_Directions_J234.PNG">

On the Dexter HDI, there is an "X" on the base that indicates the home position.

Normally, when unpacking Dexter, all you should need to do is rotate Joint 3 in the direction shown by the arrow (clockwise, when facing the motors) and make minor adjustments to the other joints. Each motor drive shaft has a small dial / handle which can be used to rotate it to a consistent position on startup. The exception is Joint 1, which can be aligned by simply rotating it. Wherever the robot is located when powered on is accepted as the zero or home position. For more on this, see [Encoder Calibration](Encoder-Calibration). DDE will walk you through initial calibration. **Note: The Dexter HDI should NEVER be calibrated outside the factory as it is not necessary and is a complex process.**

## Power
Plug in the power cord to fire up your Dexter robot. Wait for the "boot dance" to complete (if it's enabled). 

If you don't see the boot dance, or the board doesn't light up, see [Troubleshooting](Troubleshooting)
Continue to [connect to Dexter](Dexter-Networking).

# Bootup

On power up, Dexter will load the OS, then move slightly to let you know it's working. That's it for the Dexter 1 and HD models. On the new HDI, the robot will start looking for home position via the [Find_Index_Pulses_HDI](https://github.com/HaddingtonDynamics/Dexter/blob/StepAngles/Firmware/dde_apps/Find_Index_Pulses_HDI.dde) [Job Engine](DDE#job-engine-on-dexter) script. This may take a minute or two. If so configured, it may then go into [PHUI mode](PhysicalUserInterface) where it is waiting for you to move the end effector and record or play back recordings. While in any of these startup modes, you will NOT be able to communicate with the robot via [DDE](DDE). You can use [SSH](Dexter-Networking#shell-access-via-ssh) or the [console cable](Dexter-USB-Connection). Once those startup modes end, you will be able to communicate with the robot via DDE. 

To exit PHUI mode: Grab the tool interface and "cog" 7 places to the right and then up twice to exit PUI mode. That is "right" while facing the robot, meaning CCW when looking down on the robot from the top. After exiting, Dexter firmware will respond to commands from a PC or other connected device.