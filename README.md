
# LSL-Mouse
LSL-Mouse displays a circular path and streams the mouse coordinates to LSL.  
https://labstreaminglayer.readthedocs.io
Data is also saved in `data.csv` and `marker.csv` (same directory).  

Requirements :
- Java 8 (or newer): https://www.java.com/fr/download/  
- LSL-Mouse.jar: download the last release (see in [RELEASE](/RELEASE/)) and expand it
- Optional, but necessary for LSL streaming :
	- download the liblsl the `liblsl*.*` corresponding to your OS (liblsl32.dll should work on any windows OS)  
	https://github.com/sccn/liblsl/releases
	- **Make sure you have `LSL-Mouse.jar` and `liblslxxx.zzz` in the same directory**

Launch :
- double click `LSL-Mouse.jar`, this should do it...

Console use :   
- `cd ` to the directory where LSL-Mouse.jar is located    
- `java -jar LSL-Mouse.jar`   
- Command line options :  
	- `java -jar LSL-Mouse.jar -h`


User input: press the following key
- space : switch to next phase
- q : quit
- c : window message showing configuration

-----  

More detailed information: see in [DOC](/DOC/).  

-----  
Developed by Pierre JEAN (IMT Mines Alès) and Denis MOTTET (Université Montpellier).
