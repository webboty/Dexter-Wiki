Installing the CP2104 USB adapter driver<BR>
<a href="https://raw.githubusercontent.com/HaddingtonDynamics/Dexter/master/Hardware/DexterCP2104Installing.png"><img src="https://raw.githubusercontent.com/HaddingtonDynamics/Dexter/master/Hardware/DexterCP2104Installing.png" height=366 width=496></a>

CP2104 USB Port Installed<BR>
<a href="https://raw.githubusercontent.com/HaddingtonDynamics/Dexter/master/Hardware/DexterCP2104Installed.png"><img src="https://raw.githubusercontent.com/HaddingtonDynamics/Dexter/master/Hardware/DexterCP2104Installed.png" height=366 width=496></a>

## In DDE:

Eval result of  `serial_devices()` _before_ installing: `INSPECTing  A Array of 0`

Eval result of  `serial_devices()` after installing:
```` 
 <    > Refresh INSPECTing  An Object of 7 properties
{comName: "COM63"
  locationId: "Port_#0001.Hub_#0006"
  manufacturer: "Silicon Labs"
  pnpId: "USB\\VID_10C4&PID_EA60\\01255890"
  productId: "EA60"
  serialNumber: "01255890"
  vendorId: "10C4"
}
````