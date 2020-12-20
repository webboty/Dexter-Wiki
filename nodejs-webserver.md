All of the below is in the current image already. Files which may be more up to date are available in the [Firmware www folder](https://github.com/HaddingtonDynamics/Dexter/tree/Stable_Conedrive/Firmware/www) and a [Batch Update](#batch-update) can make it easy to install new versions.

Contents:
- <a href="#installing-node-js-on-dexter">Installing Node.js on Dexter</a>
- <a href="#a-node-js-websocket-server">A Node.js Websocket server</a>
- <a href="#browser-interface">Browser interface</a>
- <a href="#scratch">Scratch</a>, <a href="#web-editor">Web Editor</a>, <a href="#modbus-client">ModBus client</a>

Node.js allows you to use Javascript as a scripting language on the host instead of just on the client in the browser. It's very commonly used to build things like web servers, apps that use sockets to communicate, and generally things that work between a computer and the internet.<BR>
https://nodejs.org/en/ <BR>
https://en.wikipedia.org/wiki/Node.js
<BR>It's an ideal way to develop a web server that runs on Dexter, serves a web page that interacts with the robot and allows you to directly control it without anything but a browser.

## Installing Node js on Dexter
Because we need to download files, your Dexter must be connected to your router and the internet. Check [Dexter Networking](Dexter-Networking) for more information on that.

[SSH into Dexter](SSH-into-Dexter).

To ensure you have enough space check that `df -h ` shows more than 4.0G available.

To install Node.js, you should first install NVM (Node Version Manager) you can can use the method outlined at:<BR>
https://www.liquidweb.com/kb/how-to-install-node-version-manager-on-ubuntu/<BR>
Or, if you have an older version of Dexter, with Ubuntu 12.04 (use `lsb_release -a` to check the OS version) you can use these instructions:<BR>
https://www.liquidweb.com/kb/how-to-install-nvm-node-version-manager-for-node-js-on-ubuntu-12-04-lts/
<BR>Everything is up to date, and we already have a c compiler and build platform, so you should be able to skip the:<BR> 
`apt-get install build-essential libssl-dev`
<BR>but if you run into problems below, try that before giving up. It is 29 Megs, so we should have space for it. You can approve it. That takes quite a while. 

To actually install NVM, you need curl, so you have to install that:<BR>
`sudo apt-get install curl`

The current version of NVM is 0.33.11 so I did<BR>
`curl https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash`
<BR>but you can probably use whatever the latest version is as listed here:<BR>
https://github.com/nvm-sh/nvm#installing-and-updating <BR>
It should complete quickly, and advise you to close and re-open your terminal, so log off and back on. And now we should have nvm. You can check with `nvm --help`

Now you can use NVM to install different versions of Node.js. The latest LTR (Long Term Release) version which is compatible with Ubuntu 16.04 is 11.6 (for Ubuntu 12 is version 4.9). So install that with:<BR>
`nvm install 11.6`

Once that finishes, you should be able to run node:<BR>
`node --help`

Notes:<BR>
- If you install more than one version of node, you must select the default each time. To avoid this, use `nvm alias default 4.9.1` to set the default to e.g. 4.9.1 (use whatever version you have working)
- If, when you type `node --help` (or any other node command) you get `node: command not found` try `nvm ls` to verify that you have 4.9.1 listed, then try `nvm use 4.9.1` to set that as the current version.
- https://nodejs.org/docs/latest-v4.x/api/index.html API docs for node 4.x
- https://nodejs.org/docs/latest-v11.x/api/index.html API docs for 11.x

## A Node js Websocket server

To setup a simple web server / Socket interface to the Dexter [Firmware](Firmware) first create a sub folder under the share:<BR>
````
cd /srv/samba/share
mkdir www
cd www
````
Now initialize the folder (just accept all the default answers to the questions), and install the ws websocket, and formidable form post modules
````
npm init
npm install ws
npm install formidable
````
This folder will hold all the files your server will serve when a user connects. 

Next, make a file called httpd.js using nano:<BR>
`nano httpd.js`<BR>
and then copy in the following raw text of the httpd.js file from<BR>
https://github.com/HaddingtonDynamics/Dexter/tree/StepAngles/Firmware/www
<BR>(to paste into PuTTY just right click the window)

That last line in that file would test the socket interface by sending a status update request to Dexter and then writing out the returned value to the console, but the program needs to NOT connect to dexter at the start, and only connect when asked to by the browser. Why? Because DexRun.c only supports a single socket connection at a time, and if this server "hogs" it, DDE and other applications cant connect at all. 

Run the script by entering<BR>
`node httpd.js`

To stop the server, press Ctrl+C

To run your web server in the background while you continue to work, use <BR>
`node httpd.js &`
<BR>The & or and sign at the end places the server in the background. You can check on it with `job` or take it back to the foreground with `fg` and then press Ctrl+C to stop it. 

But as long as it's running in the background, you can edit files for it to serve in the /srv/samba/share/www folder. For example, if you place a file called "index.html" in that folder, with something like:
```javascript
<html>
<body>
<h1>HELLO WORLD!</h1>
</body>
</html>
````
and then go to http://_dexers-ip-address_/index.html you should see "HELLO WORLD!"

## Browser interface
Now you can make a file with some Javascript in it that makes a websocket connection to the Node.JS server on port 3000, which will then make a _raw_ socket connection to Dexter on port 50000, and pass on your request, and log the result. That is the chat.html file at:<BR>
https://github.com/HaddingtonDynamics/Dexter/tree/StepAngles/Firmware/www
<BR>Which will get a status report and display it on the browser document as decimal integers (the node console display is hex bytes). 

The raw data returned would be something like:
`1,0,1531789558,73782,103,0,0,0,0,0,0,0,0,0,3757,3003,0,0,0,2147483647,0,0,0,0,286,56,0,0,0,2147483647,0,0,0,0,786,3015,0,0,0,2147483647,0,0,0,0,1688,3687,0,0,0,2147483647,0,0,0,0,2004,216,0,0,0,2147483647
`

You can see the returned little endian integer values (4 bytes each) which are 1, 1, then the two times, and the 103 (67 hex) is the 'g'. The rest of the data includes all the [status values](status-data). See [Dexter - DDE communications](DexRun-DDE-communications) for more on how to talk to Dexter via the socket interface.

There are other capabilities which can be added to the node server as well:

### Scratch
With the Node Proxy, you can also add extensions to the [Scratch](https://scratch.mit.edu/) language to control Dexter, as demonstrated in [this video](https://www.facebook.com/steamaker/videos/577470826025034/). For more, see [Scratch extension](Scratch-extension)

### Web Editor
Editing files on Dexter via a browser based web editor. This makes it _really_ easy to edit the Job Engine jobs which run on startup, as it opens the autoexec.jobs file by default. To disable a job, just put a # in front of it's file name. To enable, remove the leading #. To add a job, put it in the dde_apps folder, and then list it in the autoexect.jobs file (no path required). You can also traverse all the files on Dexter and edit any text files. For more, see:<BR>
https://github.com/HaddingtonDynamics/Dexter/issues/85

### ModBus client
Acting as a ModBus server to set and read register values from a file or start and return values from Job Engine jobs:<BR>
https://github.com/HaddingtonDynamics/Dexter/issues/84

## Batch Update
After installing node on Dexter (or if it is already installed) it can be easy to update all the files related to the node server by simply downloading the current image of the www folder as a zip file and extracting it, then copying all those files into the /srv/samba/share/www folder. Step by step:
1. You will need a Dexter robot, and a [local network connection to it from a PC](Dexter-Networking#connection). 
2. On the PC, you will need a way to quickly transfer a large number of files to [Dexter via the samba share or sftp](Dexter-Networking#file-sharing). The key is to be able to see the files on the robot in your file manager on the PC and move files back and forth.
3. Download the [www.zip](https://drive.google.com/file/d/1opXPhTBxCwHbTXl6UTgPAOxYyx3PuR4r/view?usp=sharing) file and extract on your PC. Inside you will find a folder called 'www' and in that folder there are a bunch of files like "httpd.js" and others as well as sub folders like "node_modules" and "edit". You may see some of the same files and folders in the www folder under the share on Dexter. The goal is to /replace/ the files on Dexter with these new files. 
4. Copy the www folder from the zip file over the top of the www folder on Dexter, telling it to replace any existing files. 
5. Restart the robot once that is finished. Then when it has settled down after bootup, hit the robots IP address with your web browser and explore the options. 

On a Mac or Linux PC, after downloading the zip file and extracting it to a www subfolder in your Downloads folder, you can use scp in the terminal program:
````
cd  /users/your_name/Downloads
scp -r www root@192.168.1.142:/srv/samba/share/
````
Where "your_name" is your user name on the PC and 192.168.1.142 is whatever your Dexters IP address actually is. 