# Dexter <=> DDE communications: 

Uses sockets, over an [Ethernet link](Dexter-Networking).

The DDE code is in socket.js. Data is sent via send, and recieved via on_recieve which is setup as the socket response handler. 

### Data Format
Fields are separated by space (but could be comma) and the request is terminated by a ';' (semicolon).

**Header**

- ​<i>job_id</i>: Mapped to an actual job object. There can be many jobs and this number indexes the job the data came from.
- _instruction id_: Index of the instruction in the jobs do_list array. e.g. In a Job with 4 instructions, the id will be 4 for the last instruction. 
- _start_: Time (in milliseconds since Jan 1, 1970) when the instruction was started.
- _end_: Time the instruction ended. Since the instruction hasn't ended, its end time is "undefined."
- [_instruction_](Command-oplet-instruction): The command / oplet / instruction to be executed.

for example, `1 1 1528438131 undefined g ;` will return a status update

**Data**

All of the header data is included in the same format in respose packets sent back to DDE from Dexter

The ID's and Oplet allow DDE to know what that response is to. 

The times are useful tools for debugging. We can know the timing of an instruction to compare it with other instructions in the job as well as instructions from other jobs. In a post-motem analysis of two cooperating jobs, you can see which instructions (from both jobs) happened first.​

The DexRun code is implemented in several different ways which are selected by the second command line argument. 
* ./DexRun # 1 # uses [StartServerSocket](../search?utf8=%E2%9C%93&q=StartServerSocket+filename%3ADexRun.c&type=) which is a generic server interface. 
* ./DexRun # 2 # uses [StartClientSocket](../search?utf8=%E2%9C%93&q=StartClientSocket+filename%3ADexRun.c&type=) which is a generic client interface. 
* ./DexRun # 3 # uses [StartServerSocketDDE](../search?utf8=%E2%9C%93&q=StartServerSocketDDE+filename%3ADexRun.c&type=) which is a special server interface for DDE. By default, Dexter starts DexRun in this mode.

[StartServerSocketDDE](../search?utf8=%E2%9C%93&q=StartServerSocketDDE+filename%3ADexRun.c&type=) uses a larger 256 byte buffer (the others are 64 bytes) but only 128 bytes is sent by DDE. When a new packet is received, it is processed by [ProcessServerReceiveDataDDE](../search?utf8=%E2%9C%93&q=ProcessServerReceiveDataDDE+filename%3ADexRun.c&type=)  which:
* Expects to see at least 4 spaces at the start (will not start passing on data until 4 spaces are seen and removed). This is done to strip off the header data and return just the actual message payload.
* Expects to see a 0x3B "delimiter" or flag at some point in the data. 0X3B is a ';' (semicolon). This is replace with a NULL and is the terminator for the payload. The code, does NOT however, stop when it sees a semicolon. ALL semicolons will be replaced with NULL. Any semicolon in the data must be escaped (e.g. as added for the [write_to_robot implementation](https://github.com/HaddingtonDynamics/Dexter/commit/d54dba59f23a629d92783c3f018deb41a0415770)). 

The ParseInput routine then tokenizes the payload input via the standard C [strtok](http://www.massmind.org/techref/language/ccpp/cref/FUNCTIONS/strtok.html) function using " ," (space _or_ comma) as the delimiter. The first token is expected to be the the [command / oplet / instruction](Command-oplet-instruction). This is converted from a letter to an enumerated value by [HashInputCMD](../search?utf8=%E2%9C%93&q=HashInputCMD+filename%3ADexRun.c&type=). Then a switch continues the parsing of the command according to that value. 

Every command sent by DDE is then replied to via [ProcessServerSendDataDDE](../search?utf8=%E2%9C%93&q=ProcessServerSendDataDDE+filename%3ADexRun.c&type=) which again strtok's the (cleaned up) command from the recieved data and then simply returns it as an integer in an array along with some time information, an error code, and the current robot [status](status-data).

