All of the below is in the current image already. Files which may be more up to date are available in the [Firmware www folder](https://github.com/HaddingtonDynamics/Dexter/tree/StepAngles/Firmware/www)

Contents:
- <a href="#installing-node-js-on-dexter">Installing Node.js on Dexter</a>
- <a href="#a-node-js-websocket-server">A Node.js Websocket server</a>
- <a href="#browser-interface">Browser interface</a>
- <a href="#scratch">Scratch</a>

Node.js allows you to use Javascript as a scripting language on the host instead of just on the client in the browser. It's very commonly used to build things like web servers, apps that use sockets to communicate, and generally things that work between a computer and the internet.<BR>
https://nodejs.org/en/ <BR>
https://en.wikipedia.org/wiki/Node.js
<BR>It's an ideal way to develop a web server that runs on Dexter, serves a web page that interacts with the robot and allows you to directly control it without anything but a browser.

## Installing Node js on Dexter
Because we need to download files, your Dexter must be connected to your router and the internet. Check [Dexter Networking](Dexter-Networking) for more information on that.

[SSH into Dexter](SSH-into-Dexter).

To ensure you have enough space check that `df -h ` shows more than 4.0G available.

To install Node.js, you can use the method outlined at:<BR>
https://www.liquidweb.com/kb/how-to-install-node-js-via-nvm-node-version-manager-on-ubuntu-12-04-lts/
<BR>which uses NVM (Node Version Manager). And the procedure for installing that is described at:<BR>
https://www.liquidweb.com/kb/how-to-install-nvm-node-version-manager-for-node-js-on-ubuntu-12-04-lts/
<BR>Everything is up to date, so you can start at:<BR> 
`apt-get install build-essential libssl-dev`
<BR>that is 29 Megs, so we should have space for it. You can approve it. That takes quite a while. 

To actually install NVM, you need curl, so you have to install that:<BR>
`sudo apt-get install curl`

The current version of NVM is 0.33.11 so I did<BR>
`curl https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash`
<BR>but you can probably use whatever the latest version is. It should complete quickly, and advise you to close and re-open your terminal, so log off and back on. And now we should have nvm. You can check with `nvm --help`

Now you can use NVM to install different versions of Node.js. The latest LTR (Long Term Release) version which is compatible with Ubuntu 12 is version 4.9. So install that with:<BR>
`nvm install 4.9`

Once that finishes, you should be able to run node:<BR>
`node --help`

Notes:<BR>
- If you install more than one version of node, you must select the default each time. To avoid this, use `nvm alias default 4.9.1` to set the default to e.g. 4.9.1 (use whatever version you have working)
- If, when you type `node --help` (or any other node command) you get `node: command not found` try `nvm ls` to verify that you have 4.9.1 listed, then try `nvm use 4.9.1` to set that as the current version.
- https://nodejs.org/docs/latest-v4.x/api/index.html API docs for node 4.x

## A Node js Websocket server

To setup a simple web server / Socket interface to the Dexter [Firmware](Firmware) first create a sub folder under the share:<BR>
````
cd /srv/samba/share
mkdir www
cd www
````
Now initialize the folder (just accept all the default answers to the questions), and install the ws websocket module
````
npm init
npm install ws
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

## Scratch
With the Node Proxy, you can also add extensions to the [Scratch](https://scratch.mit.edu/) language to control Dexter, as demonstrated in [this video](https://www.facebook.com/steamaker/videos/577470826025034/). For more, see [Scratch extension](Scratch-extension)
