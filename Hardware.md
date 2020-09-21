# [Dexter Hardware](https://github.com/HaddingtonDynamics/Dexter/blob/master/Hardware)

Hardware includes:
* 3D printed parts provide scaffolding and are not structural. PLA on Dexter 1, then Onyx via MarkForged on HD and up.
* Carbon fiber strakes and square tubing provide structural support between the joints.
* Steel shafts and bearings make up each [Joint](Joints)
* [Strain Wave drives](https://www.youtube.com/watch?v=_jigqm6b8qc) originally from Harmonic [hanzh.com](http://www.hanzh.com/product-2.html) Model 14 component gear set 52:1 (unlisted, special order), but now from [Cone in the USA](https://conedrive.com/products/harmonic-2/), provide extensive gearing for Joints 1, 2 and 3, and a series of pulleys provide gearing for Joints 4 and 5. HD and prior versions rapidly oscillated back and forth over the motors 0.9' / 16 micro-step positions to increase resolution on J4/5 but the new HGI uses a set of pulley reductions to avoid that. Any error in belt stretch (which is minor due to the use of Kevlar) are corrected out quickly as the encoder sits at the joint and the FPGA updates rapidly.
* NEMA 17 0.9'/step Stepper Motors provide motive power.
* [Motor driver / interface PCB](Motor-Control-PCB)
* A2D [joint](Joints) [encoder](Encoders) give ~ONE MILLION CPR at each joint.
* Xilinx based [MicroZed](MicroZed) processor board provides stunning response speeds to any measured error in joint position.
* [micro SD card](SD-Card-Image) holds FPGA programming and Ubuntu 16.04 OS with [Firmware](Firmware), [node.js server](nodejs-webserver), and DDE [Job engine](DDE#job-engine-on-dexter).
* Wiring harness
* [End effector tool interface](End-Effectors). Many different end effectors will be developed for Dexter, and they will be documented in the [End effector list](End-Effectors).

[Parts list (BOM)](https://docs.google.com/spreadsheets/d/1uk89q76vcK4OT9NTM6qxsPpkON_QM3-OrlhfjPigGuE/edit?usp=sharing). May be out of date.

For problems related to hardware, be sure to search [Hardware Issues](https://github.com/HaddingtonDynamics/Dexter/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3AHardware+)