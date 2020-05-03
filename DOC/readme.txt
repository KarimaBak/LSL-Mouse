LSL-Mouse displays a circular path and streams the mouse coordinates to LSL. 
Data is also saved in 'data.csv' and 'marker.csv' (same directory)

Requirements : 
-- Java 8 (or newer): https://www.java.com/fr/download/
-- LSL-Mouse.jar 

Requirements for LSL streaming : 
-- the liblsl*.* corresponding to the OS (liblsl32.dll should work on any windows OS)
*** Make sure you have LSL-Mouse.jar and liblslxxx in the same directory ****

Launch : 
-- double click LSL-Mouse.jar, this should do it... 

Console use : 
1°) cd to the directory where LSL-Mouse.jar is located 
2°) java -jar LSL-Mouse

Command line options : java -jar LSL-Mouse.jar -h 

User input: press the following key
- space : switch to next phase 
- q : quit
- c : window message showing configuration

Developed by Pierre JEAN (IMT Mines Alès) and Denis MOTTET (Université Montpellier). 

