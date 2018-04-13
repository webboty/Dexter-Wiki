# Dexter <=> DDE communications: 

Uses sockets, 

The DDE code is in socket.js. Data is sent via send, and recieved via on_recieve which is setup as the socket response handler. 

The DexRun code is implemented in several different ways which are selected by the second command line argument. 
* DexRun # 1 # uses StartServerSocket which is a generic server interface. 
* DexRun # 2 # uses StartClientSocket which is a generic client interface. 
* DexRun # 3 # uses StartServerSocketDDE which is a special server interface for DDE. By default, Dexter starts DexRun in this mode.

StartServerSocketDDE uses a larger 256 byte buffer (the others are 64 bytes). When a new packet is received, it is processed by ProcessServerReceiveDataDDE which:
* Removes all spaces (spaces in your data must be escaped)
* Expects to see at least 4 spaces at the start (will not start passing on data until 4 spaces are seen)
* Expects to see a 0x3B "delimiter" at some point in the data, which it zeros. (any ';' in the data must be escaped)

The ParseInput routine then tokenizes the input via the standard C [strtok](http://www.massmind.org/techref/language/ccpp/cref/FUNCTIONS/strtok.html) function using " ," (space and comma) as the delimiter. The first token is expected to be the the [command / oplet / instruction](Command oplets). This is converted from a letter to an enumerated value by HashInputCMD. Then a switch continues the parsing of the command according to that value. 


