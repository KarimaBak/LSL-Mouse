# Data header


The first line in `data.csv` is the `data_header`.
It consists in a collection of name-value pairs, where pairs are separated by `';'` (semicolon), within which name and value are separated by `' '` (space).
Aexemple of the (long) first line is:
```
screenWidth 1440;screenHeight 856;cornerX 372;cornerY 80;externalRadius 348;internalRadius 268;borderRadius 1;borderColor java.awt.Color[r=255,g=255,b=255];backgroundColor java.awt.Color[r=0,g=0,b=0];cursorRadius 16;cursorColorRecord java.awt.Color[r=255,g=0,b=0];cursorColorWait java.awt.Color[r=255,g=255,b=0];autoStart 3600;cycleMaxNumber 6;cycleDuration 3;software LSL-mouse;version 1.1.0;task CircularTarget;isWithLSL true
````
After parsing the previous `data_header`, we get :
```
screenWidth = 1440
screenHeight = 856
cornerX = 372
cornerY = 80
externalRadius = 348
internalRadius = 268
borderRadius = 1
borderColor = java.awt.Color[r=255,g=255,b=255]
backgroundColor = java.awt.Color[r=0,g=0,b=0]
cursorRadius = 16
cursorColorRecord = java.awt.Color[r=255,g=0,b=0]
cursorColorWait = java.awt.Color[r=255,g=255,b=0]
autoStart = 3600
cycleMaxNumber = 6
cycleDuration = 3
software = LSL-mouse
version = 1.1.0
task = CircularTarget
isWithLSL = true
```
## Parameters description
The parameters in the `data_header` detail the current configuration of the display and of the target, so to allow for the "replay" the recorded data, or to allow for the post-hoc analysis of the data. The name of each parameter should be be easy to understand, and the table below provides more information:  


| Parameter   |  unit | comment |
| ------------- |------------- | ------------ |
|  screenWidth   		|  pixel |
|  screenHeight 		|  pixel |
|  cornerX      		|  pixel | origin is top-left
|  cornerY      		|  pixel | origin is top-left
|  externalRadius 	|  pixel |
|  internalRadius  	|  pixel |
|  borderWidth 			|  pixel |
|  borderColor 		  |  RGB  |
|  backgroundColor 	|  RGB |
|  cursorRadius 		|  pixel |
|  cursorColorRecord|  RGB | color during Record phase  
|  cursorColorWait 	|  RGB | color during Pause phase
|  autoStart        |second |
|  cycleMaxNumber 	|   | number of Record-Pause cycles in the sequence
|  cycleDuration 		| second 	|  duration of the Record (or Pause) phase
|  software 				|   |
|  version 					|   |
|  task 						|   |
|  isWithLSL 				|   |  `true` if LSL is active

## Code examples
### isInside
To check if the cursor is within the target, LSL-mouse uses :

``` java
// check if the cursor is inside the circular target
// inside = no part of the cursor is hitting the walls...

// mouse events are relative to the window listener
int X = mouseEvent.getX();
int Y = mouseEvent.getY();

// make event relative to frame (using insets)
X = X - configuration.getFrameInsets().left;
Y = Y - configuration.getFrameInsets().top;

// make event relative to center of circle
int dx = configuration.getCenterX() - X;
int dy = configuration.getCenterY() - Y;

double d = Math.sqrt(dx * dx + dy * dy ); // distance from circle center

int limitInternal = configuration.getInternalRadius();
int limitExternal = configuration.getExternalRadius();

// cursor has a radius : remove the size of the cursor
limitInternal = limitInternal + configuration.getCursorRadius() ;
limitExternal = limitExternal - configuration.getCursorRadius() ;

// border is drawn inside the circle: remove border of external circle
limitExternal = limitExternal - configuration.getBorder();

boolean isInside = (d < limitExternal & d > limitInternal);
```
