# Dexter Gateware

"Gateware" is what we call the FPGA programming that makes Dexter stunningly responsive. It allows feedback from encoder to motor in as little as 200 nanoseconds. 

Dexters gateware configures the FPGA on the zedboard. The DexRun.c firmware communicates with the gateware by setting and reading memory mapped registers. 