# Dexter <=> DDE communications: 

Uses sockets, 

The DDE code is in socket.js. Data is sent via send, and recieved via on_recieve which is setup as the socket response handler. 

The DexRun code is implemented in several different ways which are selected by the second command line argument. 
* DexRun # 1 # uses StartServerSocket which is a generic server interface. 
* DexRun # 2 # uses StartClientSocket which is a generic client interface. 
* DexRun # 3 # uses StartServerSocketDDE which is a special server interface for DDE. By default, Dexter starts DexRun in this mode.

StartServerSocketDDE uses a larger 256 byte buffer (the others are 64 bytes). When a new packet is received, it is processed by ProcessServerReceiveDataDDE
