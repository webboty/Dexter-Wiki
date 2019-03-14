# [Dexter Development Environment](https://github.com/HaddingtonDynamics/Dexter/blob/master/DDE/README.md)

DDE allows users to develop and run software on multiple platforms to control the Dexter 5 axis robot arm. In addition to providing standard robot control functions like [kinematics](Kinematics), it is fundamentally a JavaScript development environment with lots of extensions. See [DexRun-DDE-communications](DexRun-DDE-communications) for more detail about the communications between DDE and Dexter.

## Install DDE
[Links to current LTS and other versions](https://github.com/HaddingtonDynamics/Dexter/blob/master/DDE/README.md)

### DDE on Windows
Download and run the .exe

### DDE on Ubuntu
1. In the Documents folder, create a subfolder called "dde_apps".
2. Download the AppImage file, change the execution permissions, and start it.
<img src="https://raw.githubusercontent.com/HaddingtonDynamics/Dexter/master/DDE/doc/installDDEUbuntu.png">

### DDE on Dexter
In this case, we don't need to make a distributable package, we just want to run the source. And having the source directly run means we can develop on Dexter and also use parts of it in other ways (see Job Engine below). So instead of installing the Electron package, we just install the source and use npm to pull in the dependencies. Note: This requires the 16.04 version of the operating system on Dexter.
````
$ git clone https://github.com/cfry/dde
$ cd dde
$ npm i
$ npm run start
````
Of course, the GUI part of the app will only be visible with an X-Server running and since Dexter does not have a video adapter, this must be a remote the [X-Windows Desktop](Dexter-Networking#x-windows). On the current images, an icon is provided to launch DDE from the desktop when logged in via X-Windows. The program takes a while to load (need faster SD Card and interface?) but operation isn't horribly slow.

A "dde_apps" folder is created under the "/root" folder (alongside Documents, not in it) for the DDE application. Setting the dexter0 ip address to `localhost` in the `/root/dde_apps/dde_init.js` file allows local connection of DDE to DexRun. 

### Job Engine on Dexter

To run DDE jobs without the full DDE GUI interface, e.g. via [SSH](Dexter-Networking#shell-access-via-ssh), you can start them from `~/Documents/dde` with the command:<br>
`node  core  define_and_start_job  job_file.dde`

**Job Engine Initialization:** When run for the first time, the job engine creates a `dde_init.js` file in the `/root/Documents/dde_apps` folder. (note this is different than for the GUI DDE on Dexter which is in `/root/dde_init.js`). The job engine defaults to simulate, so the jobs don't actually make the robot move until the dde_init file is edited to add `,simulate: false` after the IP address in the definition of dexter0. The IP address is set to `localhost` so it will work no matter what IP address Dexter is actually assigned.

`/root/Documents/dde_apps/dde_init.js`:
````Javascript
persistent_set("ROS_URL", "localhost:9090") //required property, but you can edit the value.
persistent_set("default_dexter_ip_address", "localhost") //required property but you can edit the value.
persistent_set("default_dexter_port", "50000") //required property, but you can edit the value.
new Dexter({name: "dexter0", simulate: false}) //dexter0 must be defined.
````

When you 'run a job' as defined above, it sets window.platform to "node". If you are in dde, `window.platform == "dde"` will evaluate to true. That means you can customize any code written based on this "platform" i.e.
````Javascript
if(window.platform == "node")      { /* hey I'm in node. */ }
else if (window.platform == "dde") { /* we're in dde! */ }
````

The system software takes advantage of this. One important case is that the "out" function is defined as:
````Javascript
function out(val="", color="black", temp=false, code=null){
    if(window.platform == "node") { console.log(val) }
    else { /* do formatting and print to DDE's Output pane */ }
}
````
Thus when running on node, 'out' only pays attention to its first arg, and it sends the first arg directly to the console.

## Programming notes:

**Units:** Note that Dexter uses the metric system of measurement. So all units of distance are in meters. A meter is about 3 feet which is about Dexters full reach at the size Dexter has been made at so far. A tenth of a meter (e.g. 0.1) is about 4 inches which is about the length of your finger. A hundredth of a meter (e.g. 0.01) called a "centimeter"  is a about a half inch (actually closer to 0.4 inches) or about the width of your little finger. And a thousandth of a meter (e.g. 0.001) called a "millimeter" is tiny, about a half of a tenth of an inch or about the thickness of a fingernail.

So if you change the first digit to the right of the decimal point, you are moving about a finger length. The next digit, 2nd to the right, moves you about a finger width, and the 3rd moves you about the thickness of a fingernail.

**Debugging:** _Note that all of this is documented in the  help system inside DDE. Download and install the program to access that in it's formatted form_.
- the `out` function can be wrapped around _anything_ to dump a copy of the result to the output panel when it is evaluated _without_ any change in program behavior. It returns whatever it prints, so there is no side effect to it being included. E.g. `let variable = out("Hello" + "World").toUpper())` will still put "HELLOWORLD" in variable, but it will also let you see "HelloWorld" on the screen.
- `inspect` provides a very nice interactive inspector in the output panel. It does not return values like `out` as of this time.
- To debug DDE code in the Chrome debugger, launch that by right clicking anywhere in the Editor pane and select Inspect Element to open the debugger UI. Then add `debugger;` anywhere in the code and "Eval" it. You can explore variables, step, etc... everything you can do in a web page.
- To update / debug any part of DDE, simply overwrite the objects property in the Editor script. To retain that between restarts, add the code to the dde_init.js file in the dde_apps folder. e.g. To get a report to the output panel of the result of any call to Dexter.sleep in the job, just redefine the original function:<BR>
`Dexter.sleep           = function(seconds){ return make_ins("z", seconds) }`<BR>
as a new function including a call to `out`. e.g. <BR>
`Dexter.sleep           = function(seconds){ return out(make_ins("z", seconds)) }`<br>
any job evaluated after that line will show the instruction array generated by a Dexter.sleep in the do_list. 
<br><br>In general, you can override any function in a class with: <BR>
<tt>_Class_._function-name_ = function(_parameters_) { _code_ } </tt>
