# [Dexter Hardware](https://github.com/HaddingtonDynamics/Dexter/blob/master/Hardware)

Hardware includes:
* 3D printed parts (see below) provide scaffolding and are not structural. PLA on Dexter 1, then Onyx via MarkForged on HD and up.
* Carbon fiber strakes and square tubing (see parts list) provide structural support between the joints.
* Steel shafts and bearings make up each [Joint](Joints)
* [Strain Wave drives](https://www.youtube.com/watch?v=_jigqm6b8qc) originally from Harmonic [hanzh.com](http://www.hanzh.com/product-2.html) Model 14 component gear set 52:1 (unlisted, special order), but now from [Cone in the USA](https://conedrive.com/products/harmonic-2/), provide extensive gearing for Joints 1, 2 and 3, and a series of pulleys provide gearing for Joints 4 and 5. HD and prior versions rapidly oscillated back and forth over the motors 0.9' / 16 micro-step positions to increase resolution on J4/5 but the new HGI uses a set of pulley reductions to avoid that. Any error in belt stretch (which is minor due to the use of Kevlar) are corrected out quickly as the encoder sits at the joint and the FPGA updates rapidly.
* NEMA 17 0.9'/step Stepper Motors provide motive power.
* [Motor driver / interface PCB](Motor-Control-PCB)
* A2D [joint](Joints) [encoder](Encoders) give ~ONE MILLION CPR at each joint.
* Xilinx based [MicroZed](MicroZed) processor board provides stunning response speeds to any measured error in joint position.
* [micro SD card](SD-Card-Image) holds FPGA programming and Ubuntu 16.04 OS with [Firmware](Firmware), [node.js server](nodejs-webserver), and DDE [Job engine](DDE#job-engine-on-dexter).
* Wiring harness
* [End effector tool interface](End-Effectors). Many different end effectors will be developed for Dexter, and they will be documented in the [End effector list](End-Effectors).

For diagrams, overall dimensions, etc... see the [Kinematics](Kinematics) page.

[Parts list (BOM)](https://docs.google.com/spreadsheets/d/1uk89q76vcK4OT9NTM6qxsPpkON_QM3-OrlhfjPigGuE/edit?usp=sharing). May be out of date.

For problems related to hardware, be sure to search [Hardware Issues](https://github.com/HaddingtonDynamics/Dexter/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3AHardware+)

Motor board:
- https://github.com/kgallspark/Dexter/tree/master/MotorControl (see updated [schematic PDF](09011-00135-A.PDF))

Opto interruptors:
- https://github.com/kgallspark/Dexter/tree/master/OPTO (see updated schematic and BOM in the Opto folder here)
- LEDs: https://www.digikey.com/product-detail/en/lite-on-inc/LTE-4206/160-1028-ND/121704
- Phototransistors: https://www.digikey.com/product-detail/en/lite-on-inc/LTR-4206E/160-1030-ND/121710

Harmonic Drives: www.hanzh.com/ but you need the number 14 component set. This is not listed in their site but they do reply quickly. The problem here is that the lead time is 9-12 weeks. We do not sell them directly. The gear ratio is 52:1.

STL files:
- https://www.thingiverse.com/thing:2108244 Dexter version 1
- https://www.thingiverse.com/thing:3206154 Dexter HD
- https://www.thingiverse.com/thing:3781990 .. HD update
- https://www.thingiverse.com/thing:2842496 covers

OnShape CAD:
- https://cad.onshape.com/documents/2af8ed0e61a34ebf69284c68/w/72caf65e51bde98e456925d2/e/6843c182cbf9181dbb307455

Kinematic skins:
- https://myhub.autodesk360.com/ue2d0419e/g/shares/SH919a0QTf3c32634dcf753762a288dd9424