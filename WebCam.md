The USB connector is under the RJ-45 network jack at the bottom of the control board. With a USB connector plugged in, there is very little space for a fan or cooling for the stepper drivers. A small fan can be offset to the side of the cables even if it doesn't fit completely. 

In Linux, webcams show up as /dev/video# (where # is 0, 1, 2, etc...). If the camera has audio, it should appear as /dev/audio#. Since Dexter doesn't have any built in audio or video devices, /dev/video0 and /dev/audio0 (if it has audio).

To install a simple tool for capturing images or video:<BR>
`sudo apt-get install fswebcam`

Then to test it:<BR>
`fswebcam test.jpg`
<BR> and you should see a new image file appear in the folder. 

To use the webcam with a [NodeJS webserver](nodejs-webserver), install:<BR>
https://github.com/chuckfairy/node-webcam
by downloading the repo as a zip or tz and extracting it into a temp folder on Dexters SAMBA share at `\\<dexters-ip>\share`. (Note: This is needed because NPM crashes on Ubuntu 12 trying to install this directly) On the command prompt in Dexter, `mv` the temp folder contents in the `node_modules` folder. (Note: This is needed to avoid changing permissions on the node_modules folder to let Windows write to it directly)

There is a sample of using the web cam with a ws socket on a nodejs service at:<BR>
https://github.com/chuckfairy/node-webcam/tree/master/examples/websocket