Node.js allows you to use Javascript as a scripting language on the host instead of just on the client in the browser. It's very commonly used to build things like web servers, apps that use sockets to communicate, and generally things that work between a computer and the internet.<BR>
https://nodejs.org/en/ <BR>
https://en.wikipedia.org/wiki/Node.js
<BR>It's an ideal way to develop a web server that runs on Dexter, serves a web page that interacts with the robot and allows you to directly control it without anything but a browser.

## Installing Node.js on Dexter
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
- 

## A Node.js Web / socket server.

To setup a simple web server / Socket interface to the Dexter [Firmware](Firmware) first create a sub folder under the share:<BR>
`mkdir /srv/samba/share/www`
<BR>This folder will hold all the files your server will serve when a user connects. 

Next, make a file called httpd.js using nano:<BR>
`nano httpd.js`<BR>
and then copy in the following text (to paste into PuTTY just right click the window)
````
var http = require('http'); 
var url = require('url'); //url parsing
var fs = require('fs'); //file system
var net = require('net'); //network
const ws = require('ws'); //websocket
// https://github.com/websockets/ws 
//install with:
//npm install --save ws 
//on Dexter, if httpd.js is going to be in the /srv/samba/share/ folder, 
//install ws there but then run it from root. e.g. 
//cd /srv/samba/share/ 
//npm install --save ws 
//cd /
//node /srv/samba/share/httpd.js 

//standard web server on port 8080 to serve files
http.createServer(function (req, res) {
  var q = url.parse(req.url, true)
  if ("/"==q.pathname) 
    q.pathname="index.html"
  var filename = "/srv/samba/share/www/" + q.pathname
  console.log("serving"+q.pathname)
  fs.readFile(filename, function(err, data) {
    if (err) {
      res.writeHead(404, {'Content-Type': 'text/html'})
      return res.end("404 Not Found")
    }  
    res.writeHead(200, {'Content-Type': 'text/html'})
    res.write(data)
    return res.end()
  });
}).listen(80)

//socket server to accept websockets from the browser on port 3000
//and forward them out to DexRun as a raw socket
var browser = new ws.Server({ port:3000 })
var bs 
var dexter = new net.Socket()
//don't open the socket yet, because Dexter only allows 1 socket connection
dexter.connected = false //track socket status (doesn't ws do this?)

browser.on('connection', function connection(socket, req) {
    console.log(process.hrtime()[1], " browser connected ", req.connection.Server);
    bs = socket
    socket.on('message', function (data) {
        console.log(process.hrtime()[1], " browser says ", data.toString());
        //Now as a client, open a raw socket to DexRun on localhost
        if (!dexter.connected) { 
            dexter.connect(50000, "127.0.0.1") 
            console.log(process.hrtime()[1], "dexter connect")
            dexter.on("connect", function () { 
                dexter.connected = true 
                console.log(process.hrtime()[1], "dexter connected")
                } )
            dexter.on("data", function (data){
                console.log(process.hrtime()[1], " dexter says ", data)
                if (bs) {
                    bs.send(data,{ binary: true })
                    console.log(process.hrtime()[1], " browser responded to ")
                    }
                })
            dexter.on("close", function () { 
                dexter.connected = false 
                dexter.removeAllListeners() 
                //or multiple connect/data/close events next time
                console.log(process.hrtime()[1], "dexter disconnect")
                } )
            }
        dexter.write(data.toString());
        });
    socket.on('close', function (data) {
        console.log(process.hrtime()[1], " browser disconnected ");
        bs = null
        dexter.end()
        });
    });


//test to see if we can get a status update from DexRun
//dexter.write("1 1 1 undefined g ;")

````

That last line would test the socket interface by sending a status update request to Dexter and then writing out the returned value to the console, but the program needs to NOT connect to dexter at the start, and only connect when asked to by the browser. Why? Because DexRun.c only supports a single socket connection at a time, and if this server "hogs" it, DDE and other applications cant connect at all. 

Run the script by entering<BR>
`node httpd.js`

To stop the server, press Ctrl+C

To run your web server in the background while you continue to work, use <BR>
`node httpd.js &`
<BR>The & or and sign at the end places the server in the background. You can check on it with `job` or take it back to the foreground with `fg` and then press Ctrl+C to stop it. 

But as long as it's running in the background, you can edit files for it to serve in the /srv/samba/share/www folder. For example, if you place a file called "index.html" in that folder, with something like:
````
<html>
<body>
<h1>HELLO WORLD!</h1>
</body>
</html>
````
and then go to http://_dexers-ip-address_/index.html you should see "HELLO WORLD!"

Now you can make a file with some Javascript in it that makes a websocket connection to the Node.JS server on port 3000, which will then make a _raw_ socket connection to Dexter on port 50000, and pass on your request, and log the result. Like this:
````
<html>
<body> 
	Dexter Status
	<div id="stat">hello</div>
<script type="text/javascript">
    var count = 0
    let port = 3000
    let ip_address = self.location.hostname //"192.168.0.137"
    //may not be an ip address... 
    let interval = 1000 //how often to update status
    let ws = new WebSocket('ws://'+ip_address+":"+port)
    ws.binaryType = "arraybuffer" //avoids the blob
    ws.onopen = function(){
        self.status = "open"
        getDexterStatus()
        }
    ws.onerror = function(error) {
        self.status = "error"+error
        }
    ws.onmessage = function(msg) {
        data = new Int32Array(msg.data)
        if ('g'.charCodeAt(0) == data[4]) {//status
            displayStatus(msg.ip_address,data)
//1,0,1531787828,349602,103,0,0,0,0,0,0,0,0,0,3703,2967,0,0,0,2147483647,0,0,0,0,293,56,0,0,0,2147483647,0,0,0,0,809,3063,0,0,0,2147483647,0,0,0,0,1682,3675,0,0,0,2147483647,0,0,0,0,1990,218,0,0,0,2147483647
            }
        //document.write("message:"+data.length+"  "+data)
        //ws.close()
        }

    function getDexterStatus() {
	ws.send("1 " + (count++) + " 1 undefined g")
	setTimeout(function(){getDexterStatus()},interval)
	}
    function displayStatus(ip,data) {
	var stat = document.getElementById('stat')
	stat.innerHTML = ip_address + " Job: " + data[0]+" No: " + data[1] + " Op: " + String.fromCharCode(data[4])
	//+data.toString()
	data = data.slice(10,60)
//https://github.com/cfry/dde/blob/99e37f9f466b3c374953581aefde0e27e1582fcd/robot.js#L2247
//https://github.com/cfry/dde/blob/master/robot.js#L1907
	let label = new Array("ANGLE","DELTA","PID_DELTA","FORCE_CALC_ANGLE","A2D_SIN","A2D_COS","PLAYBACK","SENT","SLOPE","MEASURED_ANGLE")
	let tdstyle="border:1px solid black; "
	var num=0
        tbl  = document.createElement('table');
        tbl.style = 'font-family: Arial, Helvetica, sans-serif; border-collapse: collapse; border: 1px solid black; width:100px;';
        var tr = tbl.insertRow(); //top header
        var td = tr.insertCell();
        td.appendChild(document.createTextNode('Cell'));
        td.style = tdstyle + " background-color:#808080; color:#ffffff;";
        td.innerText=" "
        for(var j = 1; j < 6; j++) { //top labels
            var td = tr.insertCell();
            td.appendChild(document.createTextNode('Cell'));
            td.style = tdstyle + " background-color:#808080; color:#ffffff;";
            td.innerText="Joint:" + j
            }
        for(var i = 0; i < 10; i++) {
            var tr = tbl.insertRow(); //data lines
            var td = tr.insertCell(); //left header label
            td.appendChild(document.createTextNode('Cell'));
            td.style = tdstyle + " text-align: right; background-color:#808080; color:#ffffff;";
            td.innerText=label[i]
            for(var j = 0; j < 5; j++) {
                var td = tr.insertCell();
                td.appendChild(document.createTextNode('Cell'));
                td.style = tdstyle;
                num=data[j * 10 + i]
                if (9 == i && 2147483647 == num) num="UNKNOWN"
                td.innerText=num
                }
            }
	stat.appendChild(tbl)
	}

</script>
</body>
</html>
````

Which will get a status report and display it on the browser document as decimal integers (the node console display is hex bytes). 

The raw data returned would be something like:
`1,0,1531789558,73782,103,0,0,0,0,0,0,0,0,0,3757,3003,0,0,0,2147483647,0,0,0,0,286,56,0,0,0,2147483647,0,0,0,0,786,3015,0,0,0,2147483647,0,0,0,0,1688,3687,0,0,0,2147483647,0,0,0,0,2004,216,0,0,0,2147483647
`

You can see the returned little endian integer values (4 bytes each) which are 1, 1, then the two times, and the 103 (67 hex) is the 'g'. The rest of the data includes all the [status values](status-data). See [Dexter - DDE communications](DexRun-DDE-communications) for more on how to talk to Dexter via the socket interface.
