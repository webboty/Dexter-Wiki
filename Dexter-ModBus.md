There are two ways to connect to a ModBus device: <a href="#modbus-serial">Serial</a> and <a href="#modbus-tcp">TCP</a>. In either case, the protocol needs to be processed via an <a href="#module">NPM Module</a> which can be <a href="#job-engine-support">integrated into the DDE Job Engine</a> and which has been added to DDE for the PC as of version 3.5.7 (Note: This is a BETA version). 

## Module
If your [job engine](DDE#dde-on-dexter) doesn't already have it, you will need to install support for ModBus protocol into Dexter. We have tested the [modbus-serial](https://www.npmjs.com/package/modbus-serial) NPM package. Despite the name, modbus-serial supports both serial and TCP connections.

You can do this directly if [Dexter is connected to the internet](Dexter-Networking#internet-access) by [SSHing into Dexter](Dexter-Networking#shell-access-via-ssh) and then:
````
cd /root/Documents/dde
npm install modbus-serial
````
Note: The install will take a while. 

If your Dexter is NOT directly connected to your PC and does not have ModBus installed, you can try to install it locally, and then copy the files over into the `node_modules` folder and trigger the make in the node_modules/modbus-serial/node_modules/@serialport/bindings/build folder. This is not well documented. Hopefully you can get an updated image with the ModBus support pre-installed, or [connect your Dexter to the internet](Dexter-Networking#internet-access) for this process.

Once the module is installed, you can use it via node.js. Examples are found here:<br>
https://github.com/yaacov/node-modbus-serial/tree/master/examples

## Job Engine Support

To access the new module from DDE jobs, until support is built in, we need to update the core files [SSHing into Dexter](Dexter-Networking#shell-access-via-ssh) and then:
````
cd /root/Documents/dde
nano core/index.js
````
Scroll down to the `global.` section near the end and add the line:<br>
`var ModbusRTU = require("modbus-serial")`
after the last var line above the global section, then below the other globals, add:<br>
`global.ModbusRTU = ModbusRTU`

After this change, any of the [methods allowed by the library](https://github.com/JamesNewton/node-modbus-serial/tree/patch-1#these-classes-are-implemented) should be available in a DDE job via the ModbusRTU object.


## ModBus TCP

To access network modbus devices (other than you own PC), your Dexter will need to be [connected to the network](Dexter-Networking#internet-access). 
The following classes are supported, when working as a client:

| Class | Client Function |
|-------|----------|
| FC1 "Read Coil Status" | `readCoils(coil, len)` |
| FC2 "Read Input Status" | `readDiscreteInputs(addr, arg)` |
| FC3 "Read Holding Registers" | `readHoldingRegisters(addr, len) ` |
| FC4 "Read Input Registers" | `readInputRegisters(addr, len) ` |
| FC5 "Force Single Coil" | `writeCoil(coil, binary) //NOT setCoil` |
| FC6 "Preset Single Register"
| FC15 "Force Multiple Coil" | `writeRegister(addr, value)` |
| FC16 "Preset Multiple Registers" | `writeRegisters(addr, valueAry)` |
| FC43/14 "Read Device Identification" (supported ports: TCP, RTU) | `readDeviceIdentification(id, obj)` |

For operation as a server, accepting requests from other modbus devices, see [Issue 34](https://github.com/HaddingtonDynamics/Dexter/issues/84) for progress on integrating a modbus server into the "always on" node.js web server on Dexter, and see this [more extensive example](https://github.com/HaddingtonDynamics/Dexter/blob/StepAngles/Firmware/dde_apps/modbus_test.dde) from [DCISIV](www.dcisiv.com.au) for two way modbus communications in a job engine job. 

````js
var ModbusRTU = require("modbus-serial") //for node.js, not needed in DDE
var client = new ModbusRTU();
var plc = "192.168.0.177"; //change to the address of the remote device.

// open connection to a tcp line
client.connectTCP(plc, { port: 8502 });
client.setID(1);
client.setTimeout(3000);
out(client)

client.callback = function(err, data) {
	if (err) {
    	Control.error(err);
        }
    else {
        console.log(data.data);
        }
    }

new Job({ user_data: {state: 1},
    name: "my_job",
    do_list: [
    	function() { client.writeCoil(5, this.user_data.state, client.callback)},
        function() { this.user_data.state = 0 },
        Control.wait_until(1),
    	function() { client.writeCoil(5, this.user_data.state, client.callback)},
    ]})

````

[A more extensive example](https://github.com/HaddingtonDynamics/Dexter/blob/StepAngles/Firmware/dde_apps/modbus_test.dde) from [DCISIV](www.dcisiv.com.au) is available.

A good tool for this is [QModMaster](https://sourceforge.net/projects/qmodmaster/) which can simulate a ModBus master and allow you to send test messages. 

When working with ModBusTCP, it would be best for Dexter to be listening for a message all the time, and to start a job if a new message is recieved. This ability will be integrated into the onboard [node web server](nodejs-webserver) in time. See [Issue 34](https://github.com/HaddingtonDynamics/Dexter/issues/84) for progress.

## ModBus Serial

Of course, accessing a serial modbus device requires a serial adapter, either one with built in RS485 support <sup>[1](https://www.amazon.com/dp/B0721BB8PQ)</sup>, or standard serial unit <sup>[1](https://www.tindie.com/products/Power_Modules/miniature-micro-usb-serial-uart-ttl-33v-5v-out/)</sup>, <sup>[2](http://massmind.ecomorder.com/techref/ecomprice.asp?p=416063)</sup>, <sup>[3](https://www.tindie.com/products/CircuitGizmos3/multiple-usb-serial-interface/)</sup> with a TTL to RS485 adapter <sup>[1](https://www.digitspace.com/ttl-to-rs485module-rs485-to-ttl-uart)</sup>. USB adapters based on the CP2102 or CP2104 are known to work with Dexter. 

See [Dexter-Serial-Peripherals](Dexter-Serial-Peripherals) for installation, testing and troubleshooting of serial adapters in general. 

This example job connects to a serial adapter at `/dev/ttyUSB0` and once the connection is established, it turns on the relay coil, reads the switch input and then turns off the relay. It uses a device address of 0 which seems to work with the standard cheap chinese modbus devices<a href="https://www.aliexpress.com/item/4000014522532.html">^</a>.

````js
const serial_path = "/dev/ttyUSB0"
const client_id = 0;
const coil_no = 0;

// create an empty modbus client
var ModbusRTU = require("modbus-serial");
var client = new ModbusRTU();

// open connection to a serial port, then callback write
client.connectRTUBuffered(serial_path, { baudRate: 9600 }, write);

//connection open, set client address and write
function write() {
    client.setID(client_id);

    // write the value to the coil
    client.writeCoil(coil_no, 1)
        .then(read) //promise to read
    console.log("write done");
    return
    }

function read() {
    // read the register
    client.readHoldingRegisters(0,1)
        .then(function(data){
                console.log(data)
                client.writeCoil(coil_no, 0)
                    .then(finish)
                });
    }

function finish() {
        client.close()
        console.log("finished")
        }


````