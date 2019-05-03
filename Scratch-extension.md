The scratch programming language is a popular option to teach programming with a visual, block, based user interface.<BR>
https://scratch.mit.edu/

A Scratch extension has been developed to allow Dexter to be controlled with simple blocks. <BR>
https://github.com/Kenny2github/scratch-dexter
<BR>
https://www.facebook.com/steamaker/videos/577470826025034/

To setup communications between Dexter and your browser, a Node.JS proxy between websockets and [Dexter's raw socket interface](Dexter-Networking) is required. [Node.js and a proxy](nodejs-webserver#a-node-js-websocket-server) is available on the current image, but since it is running from Dexters IP address, the browser will see it as a cross site scripting security vulnerability and refuse to work. Running a Node.JS proxy on localhost, however, is allowed. To set that up:
- `nodejs` installed on your local machine<br>
https://nodejs.org/en/download/
- `npm` installed on your local machine. It should be installed with node.js but if you `sudo apt install nodejs`, you may need to install it separately. e.g. `sudo apt install npm`
- Make a folder, and at the command prompt, npm init it and install ws. You can just take the defaults for most question during the init. You can change the entry point to 'httpd.js'. 
````
mkdir dexproxy
cd dexproxy
npm init
npm install ws
````
- Save the file from:<br>
https://raw.githubusercontent.com/Kenny2github/scratch-dexter/master/httpd.js
<br>into the folder. 

Once you have all that setup, 
- <TT>node httpd.js <i>dexter_ip</i></TT> where dexter_ip  is the IP address of the dexter robot.
- Go to<br>
https://scratchx.org/?url=https://kenny2github.github.io/scratch-dexter/ext.js#scratch
<br>and accept the warning. Drag the black "move all joints" block over into the script area on the right. You can click on "events" above and drag a "when (flag) clicked" over on top of it, then click the green flag on the left or just double click the move all joints block. 

