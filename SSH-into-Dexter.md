SSH stands for "Secure SHell. It is an application running on a computer that gives you a command line interface to typing in 'shell' commands into another computer such as Dexter's Linux operating system. Here's how to use it to control your Dexter:
### All Operating Systems
1. Connect an Ethernet cable between your computer and the Dexter processor board.
2. Username: root
3. password: klg
### Windows OS
1. Install an [SSH client](https://en.wikipedia.org/wiki/Comparison_of_SSH_clients) (e.g. [PuTTY (https://www.chiark.greenend.org.uk/~sgtatham/putty/)). 
2. Configure the IP address in the client software to 192.168.1.142 (this is [Dexters default IP address (Dexter-Networking)), or to the IP address assigned via your router. 
3. set the port to 22.
4. select SSH. 
### MacOS
1. Double click on the file: `Applications/Utilities/Terminal.app`
1. Type in `root@192.168.1.142`   and hit `enter`.
1. When prompted, type in password: `klg` and hit `enter`.
### Commonly Used Shell Commands
* `pwd`  (print working directory)
* `cd`   _dirname_in_working_directory_ (change directory to the given sub directory)
* `cd ..` (change directory to the parent of the working directory)
* `ls` (show contents of the working directory)
* `ls -l` (show working directory contents including permissions, owner, size, modified date.)
* `cat` _filename_ (show contents of the given file)
* `nano` (start a new editor buffer. Help at the screen bottom tells you basic commands. 
          When performing Control-x to exit the buffer, you will be prompted to indicate
          if you want to save the file, and if so, a new file name.)
* `nano` _filename_ (Edit an existing file. Use regular commands like the arrow keys, delete/backspace, etc... 
ctrl+o Outputs a file... this is SAVE not open.
ctrl+x to eXit. 
To select text, move the cursor to the start of the text you want to select, press the Alt-A key combination to mark the start, then move the cursor to the end of the section you want to select. press Ctrl+K to cut your selection and Ctrl+U to paste it.)

Detailed instructions are available for updating the [firmware](https://github.com/HaddingtonDynamics/Dexter/tree/master/Firmware) or [gateware](https://github.com/HaddingtonDynamics/Dexter/tree/master/Gateware)