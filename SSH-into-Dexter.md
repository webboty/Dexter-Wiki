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
1. Type in `root@192.168.1.142`   and hit enter.
1. When prompted, type in password: `klg`
### Commonly Used Shell Commands
* pwd  (print working directory)
* cd   _dirname_in_working_directory_ (change directory to the given sub directory)
* cd .. (change directory to the parent of the working directory)
* ls (show contents of the working directory)
* ls -l (show working directory contents including permissions, owner, size, modified date.)
* cat _filename_ (show contents of the given file)

