
# LSL-Mouse
LSL-Mouse displays a circular path and streams the mouse coordinates to LSL.  
https://labstreaminglayer.readthedocs.io   
Data is also saved in `data.csv` and `marker.csv` (same directory).  

### Requirements :
- Java 8 (or newer): https://www.java.com/fr/download/  
- `LSL-Mouse.jar`: download the last release (see in [RELEASE](/RELEASE/)) and expand it
- Optional, but necessary for LSL streaming
	- go to https://github.com/sccn/liblsl/releases
	- Select the `liblsl-.....` archive corresponding to your needs. A reasonable choice is the latest stable version corresponding to your operating system.   
	- download the archive and expand it
	- find and copy the necessary  `liblslxxx.zzz` file next to `LSL-Mouse.jar`
		- Windows 7:  `liblsl32.dll`
		- Windows 10: `liblsl64.dll`
		- OSX: `liblsl64.dylib`
		- Unix: `liblsl64.so`
	- **Make sure you have `LSL-Mouse.jar` and `liblslxxx.zzz` in the same directory**

#### Windows executable
For 1-click usage on Windows, download the executable (see in [RELEASE](/RELEASE/)). 

### Launch
- double click `LSL-Mouse.jar`, this should do it...

### Console use
- `cd ` to the directory where LSL-Mouse.jar is located    
- `java -jar LSL-Mouse.jar`   
- Command line options :  
	- `java -jar LSL-Mouse.jar -h`


### User input
Press the following key :
- space : switch to next phase
- q : quit
- c : window message showing configuration

-----  

More detailed information: see in [DOC](/DOC/).  

-----  
Developed by Pierre JEAN (IMT Mines Alès) and Denis MOTTET (Université Montpellier).
