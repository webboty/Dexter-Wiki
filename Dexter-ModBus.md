There are two ways to connect to a ModBus device: Serial and TCP. In either case, the protocol needs to be processed.

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

After this change, any of the methods allowed by the module should be available in a DDE job via the ModbusRTU object.


## ModBus TCP

To access network modbus devices (other than you own PC), your Dexter will need to be [connected to the network](Dexter-Networking#internet-access). 

````js
//var ModbusRTU = require("modbus-serial") //for node.js, not needed in DDE
var client = new ModbusRTU();
var plc = "192.168.1.24"; //change to the address of the remote device.

// open connection to remote device
client.connectTCP(plc, { port: 8502 })
    .then(setClient)
    .then(function() {
        console.log("Connected"); })
    .catch(function(e) {
        console.log(e.message); });

function setClient() {
    // set the client's unit id
    client.setID(1); //change to the correct unit id
    // set a timeout for requests default is null (no timeout)
    client.setTimeout(1000);
    // run program
    run();
}

function run() {
    // write to coil
    client.writeCoil(5, true)
        .then(function(d) {
            console.log("Write true to coil 5", d); })
        .catch(function(e) {
            console.log(e.message); })
        .then(writeRegisters);
}

function writeRegisters() {
    // write 3 registers statrting at register 101
    // negative values (< 0) have to add 65535 for Modbus registers
    client.writeRegisters(101, [10, 9, 8, -20 + 65535, -10 + 65535])
        .then(function(d) {
            console.log("Write 10, 9, 8, -20, -10 to registers 101 to 105", d); })
        .catch(function(e) {
            console.log(e.message); })
        .then(close);
}

function close() {
    client.close();
}
````

[A more extensive example](https://github.com/HaddingtonDynamics/Dexter/blob/StepAngles/Firmware/dde_apps/modbus_test.dde) is available.

When working with ModBusTCP, it would be best for Dexter to be listening for a message all the time, and to start a job if a new message is recieved. This ability will be integrated into the onboard [node web server](nodejs-webserver) in time. 

## ModBus Serial

Of course, accessing a serial modbus device requires a serial adapter, either one with built in RS485 support <sup>[1](https://www.amazon.com/dp/B0721BB8PQ)</sup>, or standard serial unit <sup>[1](https://www.tindie.com/products/Power_Modules/miniature-micro-usb-serial-uart-ttl-33v-5v-out/)</sup>, <sup>[2](http://massmind.ecomorder.com/techref/ecomprice.asp?p=416063)</sup> with a TTL to RS485 adapter <sup>[1](https://www.digitspace.com/ttl-to-rs485module-rs485-to-ttl-uart)</sup>. USB adapters based on the CP2102 or CP2104 are known to work with Dexter. 

See [Dexter-Serial-Peripherals](Dexter-Serial-Peripherals) for installation, testing and troubleshooting of serial adapters in general. 

This example job connects to a serial adapter at `/dev/ttyUSB0` and once the connection is established, it writes the values 0, 0xffff to registers starting at address 5 on device number 1, then reads the 2 registers starting at address 5.

````js
// create an empty modbus client
// var ModbusRTU = require("modbus-serial"); //For node.js, not needed in DDE
var client = new ModbusRTU();
 
// open connection to a serial port
client.connectRTUBuffered("/dev/ttyUSB0", { baudRate: 9600 }, write);
 
function write() {
    client.setID(1);
 
    // write the values 0, 0xffff to registers starting at address 5
    // on device number 1.
    client.writeRegisters(5, [0 , 0xffff])
        .then(read);
}
 
function read() {
    // read the 2 registers starting at address 5
    // on device number 1.
    client.readHoldingRegisters(5, 2)
        .then(console.log);
}
````