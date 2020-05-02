
# LSL-Mouse documentation

<!--
cat DOC/readme.md | /Users/dm/bin/gh-md-toc -
 -->

* [Command line arguments](#command-line-arguments)
* [Output in CSV files](#output-in-csv-files)
	* [Header block](#header-block)
		* [configuration line](#configuration-line)
		* [timestamp](#timestamp)
	* [data.csv](#datacsv)
	* [markers.csv](#markerscsv)
		* [markers block](#markers-block)
		* [typical markers sequence](#typical-markers-sequence)
* [LSL Streams (and matlab)](#lsl-streams-and-matlab)
	 * [Data](#data)
	 * [Markers](#markers)
* [Code examples](#code-examples)
	 * [isInside](#isinside)

# Command line arguments

Command line arguments are possible :

```
java -jar LSL-Mouse.jar -borderColor blue -cycleDuration 10
```

| Parameter   |  unit | comment |
| ------------- |------------- | ------------ |
|  cornerX      		|  pixel | top-left corner of circles (origin is top-left)
|  cornerY      		|  pixel | top-left corner of circles (origin is top-left)
|  centerX      		|  pixel | center of circle (origin is top-left) **supersedes cornerX**
|  centerY      		|  pixel | center of circle (origin is top-left) **supersedes cornerY**
|  externalRadius 	|  pixel |
|  internalRadius  	|  pixel |
|  borderRadius			|  pixel |
|  cursorRadius 		|  pixel |
|  borderColor 		  |  RGB   |
|  backgroundColor 	|  RGB   |
|  cursorColorRecord|  RGB   | color during Record phase  
|  cursorColorWait 	|  RGB   | color during Pause phase
|  autoStart        | second |
|  cycleMaxNumber 	|        | number of Record-Pause cycles in the sequence
|  cycleDuration 		| second | duration of the Record (or Pause) phase

RGB should be a valid color name such as {blue, red, white, yellow}
https://docs.oracle.com/javase/8/docs/api/java/awt/Color.html

# Output in CSV files
LSL-Mouse CSV output consist in 2 files :
- `data.csv`: the mouse coordinate over time
- `markers.csv`: the markers generated over time  

These are CSV files organized as follows :
- header block  
- empty line
- data block

## Header block
The first lines in the CSV files are the `header_block` (up to the empty line) :  
- configuration line
- timestamp  

The header block is **identical** in all CSV files corresponding to the same record.


### configuration line
The `configuration_line` (L1) consists in a collection of name-value pairs, where pairs are separated by `';'` (semicolon), within which name and value are separated by `' '` (space).  
An exemple of the (long) first line is:
```
screenWidth 1440;screenHeight 856;cornerX 372;cornerY 80;centerX 720;centerY 428;externalRadius 348;internalRadius 268;borderRadius 1;cursorRadius 16;borderColor java.awt.Color[r=255,g=255,b=255];backgroundColor java.awt.Color[r=0,g=0,b=0];cursorColorRecord java.awt.Color[r=255,g=0,b=0];cursorColorWait java.awt.Color[r=255,g=255,b=0];autoStart 3600;cycleMaxNumber 6;cycleDuration 3;software LSL-mouse;version 1.1.0;task CircularTarget;isWithLSL true
````
After parsing the previous `configuration_line`, we get :
```
screenWidth = 1440
screenHeight = 856
cornerX = 372
cornerY = 80
centerX = 720
centerY = 428
externalRadius = 348
internalRadius = 268
borderRadius = 1
cursorRadius = 16
borderColor = java.awt.Color[r=255,g=255,b=255]
backgroundColor = java.awt.Color[r=0,g=0,b=0]
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

The parameters in the `configuration_line` detail the current configuration of the display and of the target, so to allow for the "replay" the recorded data, or to allow for the post-hoc analysis of the data. The name of each parameter should be be easy to understand, and the table below provides more information:  


| Parameter   |  unit | comment |
| ------------- |------------- | ------------ |
|  screenWidth   		|  pixel | :warning: origin is top-left of screen
|  screenHeight 		|  pixel | :warning: origin is top-left of screen
|  cornerX      		|  pixel | top-left corner of largest circle (deprecated)
|  cornerY      		|  pixel | top-left corner of largest circle (deprecated)
|  centerX      		|  pixel | center of circles 
|  centerY      		|  pixel | center of circles
|  externalRadius 	|  pixel |
|  internalRadius  	|  pixel |
|  borderRadius			|  pixel |
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

### timestamp
The timestamp corresponds to date (in milliseconds) of the effective start of LSL-Mouse, with the following format  
`2020-04-29 12:05:21.855`

## data.csv
A typical data file looks as follows (note the very long first line):
```
screenWidth 1440;screenHeight 856;cornerX 372;cornerY 80;externalRadius 348;internalRadius 268;borderWidth 1;borderColor java.awt.Color[r=255,g=255,b=255];backgroundColor java.awt.Color[r=0,g=0,b=0];cursorRadius 16;cursorColorRecord java.awt.Color[r=255,g=0,b=0];cursorColorWait java.awt.Color[r=255,g=255,b=0];autoStart 3600;cycleMaxNumber 6;cycleDuration 3;software LSL-mouse;version 1.1.0;task CircularTarget;isWithLSL true
2020-04-30 11:45:45.190

timestamp,mouseX,mouseY,mouseInTarget
1588239949662,658.0,128.0,1.0
1588239949673,658.0,128.0,1.0
1588239949707,657.0,128.0,1.0
1588239949718,657.0,128.0,1.0
```

The `data_block` contains 4 columns of data (coma separated):
- Line 1: column headers
	- `timestamp` : recording date in [milliseconds](https://docs.oracle.com/javase/8/docs/api/java/lang/System.html#currentTimeMillis--)
	- `mouseX` and `mouseY`: coordinates of the center of the cursor **from top-left**
	- `mouseInTarget`: 1 if the cursor is within the target limits (0 otherwise)  
- Next lines : data



## markers.csv

A typical markers file looks as follows (note the very long first line):
```
screenWidth 1440;screenHeight 856;cornerX 372;cornerY 80;centerX 720;centerY 428;externalRadius 348;internalRadius 268;borderRadius 1;cursorRadius 16;borderColor java.awt.Color[r=255,g=255,b=255];backgroundColor java.awt.Color[r=0,g=0,b=0];cursorColorRecord java.awt.Color[r=255,g=0,b=0];cursorColorWait java.awt.Color[r=255,g=255,b=0];autoStart 3600;cycleMaxNumber 6;cycleDuration 3;software LSL-mouse;version 1.1.0;task CircularTarget;isWithLSL true
2020-05-02 20:28:22.268

2020-05-02 20:28:26.054,1588444106054,KeyTyped=32 DoCycleChange
2020-05-02 20:28:26.056,1588444106056,DoCycleChange:DoStartCycleTimedSequence
2020-05-02 20:28:26.056,1588444106056,DoCycleChange:DoRecord RecordDone=0 PauseDone=0 ToDo=6
2020-05-02 20:28:29.060,1588444109060,DoCycleChange:DoPause RecordDone=1 PauseDone=0 ToDo=6
2020-05-02 20:28:32.060,1588444112060,DoCycleChange:DoRecord RecordDone=1 PauseDone=1 ToDo=6

```
### markers block
The `markers_block` contains 3 columns (coma separated):
- Col 1: `timestamp` in a human readable information
- Col 2: `timestamp` in [Java milliseconds](https://docs.oracle.com/javase/8/docs/api/java/lang/System.html#currentTimeMillis--)
- Col 3: Marker (string)


### typical markers sequence  
In LSL-Mouse, markers are (from Version 1.1.0) a bit more explicit…
The markers occur as follows in a typical sequence :

```
2020-05-02 20:28:26.054,1588444106054,KeyTyped=32 DoCycleChange
2020-05-02 20:28:26.056,1588444106056,DoCycleChange:DoStartCycleTimedSequence
2020-05-02 20:28:26.056,1588444106056,DoCycleChange:DoRecord RecordDone=0 PauseDone=0 ToDo=6
2020-05-02 20:28:29.060,1588444109060,DoCycleChange:DoPause RecordDone=1 PauseDone=0 ToDo=6
2020-05-02 20:28:32.060,1588444112060,DoCycleChange:DoRecord RecordDone=1 PauseDone=1 ToDo=6
...
2020-05-02 20:29:02.071,1588444142071,DoCycleChange:DoEndPause RecordDone=6 PauseDone=6 ToDo=6
2020-05-02 20:29:06.425,1588444146425,KeyTyped=113 WINDOW_CLOSING
```

More detailed explanations below :

> USER : LSL-Mouse was launched : waiting for manual start  
> USER : press ‘space’: start recording/pause sequence

- `2020-05-02 20:28:26.054,1588444106054,KeyTyped=32 DoCycleChange`  
	Key number `32` was typed `[ascii(32) = ‘ ’]`  
	Event `DoCycleChange` was sent  

- `2020-05-02 20:28:26.056,1588444106056,DoCycleChange:DoStartCycleTimedSequence`  
	Event `DoCycleChange:DoStartCycleTimedSequence` was sent  
	This is an automatic event (no KeyTyped)  
	NB : this is a direct consequence of the previous KeyTyped (~1 ms before)  

- `2020-05-02 20:28:26.056,1588444106056,DoCycleChange:DoRecord RecordDone=0 PauseDone=0 ToDo=6`  
	Event `DoCycleChange:DoRecord` was sent with context `RecordDone=0 PauseDone=0 ToDo=6`  
	This is an automatic event (no KeyTyped)  

- `2020-05-02 20:28:29.060,1588444109060,DoCycleChange:DoPause RecordDone=1 PauseDone=0 ToDo=6`  
	Event `DoCycleChange:DoPause` was sent with context `RecordDone=1 PauseDone=0 ToDo=6`  
	This is an automatic event (no KeyTyped)   

- `2020-05-02 20:28:32.060,1588444112060,DoCycleChange:DoRecord RecordDone=1 PauseDone=1 ToDo=6`  
	Event `DoCycleChange:DoRecord` was sent with context `RecordDone=1 PauseDone=1 ToDo=6`  
	This is an automatic event (no KeyTyped)  

. . .  
	This will automatically continue up to the end of the Record/Pause sequence  

> USER : press ‘q’ to quit  

- `2020-05-02 20:29:06.425,1588444146425,KeyTyped=113 WINDOW_CLOSING`  
	Key number 113 was typed [ascii(113) = ‘q’]  
	Event WINDOW_CLOSING was sent  

# LSL Streams (and matlab)
LSL-Mouse streams data and markers following the LSL specifications
https://github.com/sccn/xdf/wiki/Specifications  

## Data
Data are stored in a stream of type [MoCap](https://github.com/sccn/xdf/wiki/MoCap-Meta-Data):
- in stream numbered `s`, which corresponds to the stream that satisfies:
``` matlab
streams{s}.info.source_id == ‘MouseCircularData’
% and also (though not useful)
% streams{s}.info.name = ‘Mouse’ & streams{s}.info.type = ‘MoCap’
```
- with description in
``` matlab
streams{s}.info.desc.channels.channel{:}
```

- with data and timestamps in
``` matlab
mouseX = streams{s}.time_series(1, :);
mouseY = streams{s}.time_series(2, :);
mouseInTarget = streams{s}.time_series(3, :);
timestamp = streams{s}.time_stamps;
% consider transposition to get column vectors
```  


## Markers
Markers are stored in a stream of type [Markers](https://github.com/sccn/xdf/wiki/Markers-Meta-Data):
- in stream numbered `s`, which corresponds to the stream that satisfies:
``` matlab
streams{s}.info.source_id == ‘MouseCircularMarkers’
% and also (though not useful)
% streams{s}.info.name = ‘Mouse’ & streams{s}.info.type = ‘Markers’
```
- with labels and timestamps in
``` matlab
streams{s}.time_series{i}  % {} because markers contain strings
streams{s}.time_stamps(i)
```  

# Code examples
## isInside
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
