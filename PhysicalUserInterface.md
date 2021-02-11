# Physical User Interface

The physical robot can be a user interface by sensing and then resisting or complying with touches from the user. For example, the robot could be programmed to start recording a new sequence of positions in "follow mode" when it feels a downward tap on the end effector. A more interesting example is when the robot resists side to side motion somewhat, but when pushed hard enough will "pop" or "cog" to a new location, where it again resists motion. This presents a feeling of turning a rotary switch through multiple positions. Combined with an up or down motion, this can be used to select from several options. 

Dexter HDI now ship with a Physical User Interface script called "PhUI" (pronounced "phooey") installed as the default startup script for the onboard [DDE job engine](DDE#job-engine-on-dexter). After boot up, Dexter HDI will find it's home position, then enter PhUI and wait for mechanical commands. During this time, Dexter HDI will NOT respond to commands from [DDE](DDE) or other control software.

## Dexter HDI PhUI Commands

- Exit: Grab the tool interface and "cog" up twice without rotating to the side to exit PUI mode. (On older versions, you first move 7 places to the right and then up twice. That is "right" while facing the robot, meaning CCW when looking down on the robot from the top.) After exiting, Dexter [firmware](Firmware) will respond to commands from a PC or other connected device.

- Record / Play: To program or play recordings, grab the tool interface and turn it left or right to one of the other slots and either replay (by tilting the end effector up twice) or record a new program (by tilting down). 

  Record: To record an action, rotate left or right from center to select a slot, then tilt the head down two cogs. Dexter will go into follow mode and allow you to record by moving the arm through a series of motions. It does take a number of seconds to be ready so be patient. To end a recording push the robot away from you so that joint 2 is tilted back and it will stop recording and return to home position. 

  Play: To replay a recording, rotate left or right from center to select a slot, tilt the end effector up two cogs. It should then reposition itself and begin to replay after 30 seconds or so. If nothing was recorded in the slot then it will do a small rotation and stop. 

  To stop it from playing just pop the end effector in a upward position with your hand. This is not a slow nudge; it's more like a smack with your flat hand in an upward position.

https://photos.app.goo.gl/trWqVvSvmc9UVz9g6

https://www.youtube.com/watch?v=wVUsNT75h5I

