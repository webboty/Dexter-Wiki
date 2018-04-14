# Dexter <=> DDE communications: 

Uses sockets, 

The DDE code is in socket.js. Data is sent via send, and recieved via on_recieve which is setup as the socket response handler. 

The DexRun code is implemented in several different ways which are selected by the second command line argument. 
* ./DexRun # 1 # uses [StartServerSocket](../search?utf8=%E2%9C%93&q=StartServerSocket+filename%3ADexRun.c&type=) which is a generic server interface. 
* ./DexRun # 2 # uses [StartClientSocket](../search?utf8=%E2%9C%93&q=StartClientSocket+filename%3ADexRun.c&type=) which is a generic client interface. 
* ./DexRun # 3 # uses [StartServerSocketDDE](../search?utf8=%E2%9C%93&q=StartServerSocketDDE+filename%3ADexRun.c&type=) which is a special server interface for DDE. By default, Dexter starts DexRun in this mode.

[StartServerSocketDDE](../search?utf8=%E2%9C%93&q=StartServerSocketDDE+filename%3ADexRun.c&type=) uses a larger 256 byte buffer (the others are 64 bytes). When a new packet is received, it is processed by [ProcessServerReceiveDataDDE](../search?utf8=%E2%9C%93&q=ProcessServerReceiveDataDDE+filename%3ADexRun.c&type=)  which:
* Expects to see at least 4 spaces at the start (will not start passing on data until 4 spaces are seen and removed)
* Expects to see a 0x3B "delimiter" or flag at some point in the data. 0X3B is a ';' (semicolon)

The ParseInput routine then tokenizes the input via the standard C [strtok](http://www.massmind.org/techref/language/ccpp/cref/FUNCTIONS/strtok.html) function using " ," (space _then_ comma) as the delimiter. The first token is expected to be the the [command / oplet / instruction](Command-oplet-instruction). This is converted from a letter to an enumerated value by HashInputCMD(../search?utf8=%E2%9C%93&q=HashInputCMD+filename%3ADexRun.c&type=). Then a switch continues the parsing of the command according to that value. 

Every command sent by DDE is then replied to via [ProcessServerSendDataDDE](../search?utf8=%E2%9C%93&q=ProcessServerSendDataDDE+filename%3ADexRun.c&type=) which again strtok's the (cleaned up) command from the recieved data and then simply returns it as an integer in an array along with some time information, an error code, and the current robot [status](status-data).

