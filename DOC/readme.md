
# LSL-Mouse documentation

* [Command line arguments](#command-line-arguments)  
* [CSV files](#csv-files)    
	* [data.csv](#datacsv)  
	* [markers.csv](#markerscsv)  
* [LSL Streams (and matlab)](#lsl-streams-and-matlab)  
	* [Data](#data)  
	* [Markers](#markers)  
	

## Command line arguments

Command line arguments are possible :

```
java -jar LSL-Mouse.jar -borderColor blue -cycleDuration 10
```

| Parameter   |  unit | comment |
| ------------- |------------- | ------------ |
|  cornerX      		|  pixel | top-left corner of circles (origin is top-left)
|  cornerY      		|  pixel | top-left corner of circles (origin is top-left)
|  centerX      		|  pixel | center of circle (origin is top-left)
|  centerY      		|  pixel | center of circle (origin is top-left)
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

## CSV files
LSL-Mouse output consist in tow files:
- `data.csv`: the mouse coordinate over time
- `markers.csv`: the markers generated over time

### data.csv
A typical file looks as follows (note the very long first line):
```
screenWidth 1440;screenHeight 856;cornerX 372;cornerY 80;externalRadius 348;internalRadius 268;borderWidth 1;borderColor java.awt.Color[r=255,g=255,b=255];backgroundColor java.awt.Color[r=0,g=0,b=0];cursorRadius 16;cursorColorRecord java.awt.Color[r=255,g=0,b=0];cursorColorWait java.awt.Color[r=255,g=255,b=0];autoStart 3600;cycleMaxNumber 6;cycleDuration 3;software LSL-mouse;version 1.1.0;task CircularTarget;isWithLSL true
2020-04-30 11:45:45.190

timestamp,mouseX,mouseY,mouseInTarget
1588239949662,658.0,128.0,1.0
1588239949673,658.0,128.0,1.0
1588239949707,657.0,128.0,1.0
1588239949718,657.0,128.0,1.0
```
The file consists in :
- Line 1: line, describing the configuration (see [DataHeader.md](./DataHeader.md) )
- Line 2: timestamp (the same in `data.csv` and `markers.csv`)
- Line 3: empty
- Line 4: column headers
	- `timestamp` : recording date in [milliseconds](https://docs.oracle.com/javase/8/docs/api/java/lang/System.html#currentTimeMillis--)
	- `mouseX` and `mouseY`: coordinates of the center of the cursor **from top-left**
	- `mouseInTarget`: 1 if the cursor is within the target limits (0 otherwise)

- Next lines : data


### markers.csv
In LSL-Mouse, markers are (from Version 1.1.0) a bit more explicit…
The markers occur as follows in a typical sequence :

`2020-04-29 09:04:41.888 Lsl string : KeyTyped=32DoCycleChange`  
`2020-04-29 09:04:41.889 Lsl string : DoCycleChange:DoStartCycleTimedSequence`  
`2020-04-29 09:04:41.889 Lsl string : DoCycleChange:DoRecord RecordDone=0 PauseDone=0 ToDo=6`  
`2020-04-29 09:05:01.889 Lsl string : DoCycleChange:DoPause RecordDone=1 PauseDone=0 ToDo=6`  
`2020-04-29 09:05:21.893 Lsl string : DoCycleChange:DoRecord RecordDone=1 PauseDone=1 ToDo=6`  
...  
`2020-04-29 09:05:33.059 Lsl string : KeyTyped=113WINDOW_CLOSING`  


More detailed explanations below :

> USER : LSL-Mouse was launched : waiting for manual start
> USER : press ‘space’: start recording/pause sequence

- `2020-04-29 09:04:41.888 Lsl string : KeyTyped=32 DoCycleChange`  
	Key number `32` was typed `[ascii(32) = ‘ ’]`  
	Event `DoCycleChange` was sent  

- `2020-04-29 09:04:41.889 Lsl string : DoCycleChange:DoStartCycleTimedSequence`  
	Event `DoCycleChange:DoStartCycleTimedSequence` was sent  
	This is an automatic event (no KeyTyped)  
	NB : this is a direct consequence of the previous KeyTyped (1 ms before)  

- `2020-04-29 09:04:41.889 Lsl string : DoCycleChange:DoRecord RecordDone=0 PauseDone=0 ToDo=6`  
	Event `DoCycleChange:DoRecord` was sent with context `RecordDone=0 PauseDone=0 ToDo=6`  
	This is an automatic event (no KeyTyped)  

- `2020-04-29 09:05:01.889 Lsl string : DoCycleChange:DoPause RecordDone=1 PauseDone=0 ToDo=6`  
	Event `DoCycleChange:DoPause` was sent with context `RecordDone=1 PauseDone=0 ToDo=6`  
	This is an automatic event (no KeyTyped)   

- `2020-04-29 09:05:21.893 Lsl string : DoCycleChange:DoRecord RecordDone=1 PauseDone=1 ToDo=6`  
	Event `DoCycleChange:DoRecord` was sent with context `RecordDone=1 PauseDone=1 ToDo=6`  
	This is an automatic event (no KeyTyped)  

. . .  
	This will automatically continue up to the end of the Record/Pause sequence  

> USER : press ‘q’ to quit  

- `2020-04-29 09:05:33.059 Lsl string : KeyTyped=113 WINDOW_CLOSING`  
	Key number 113 was typed [ascii(113) = ‘q’]  
	Event WINDOW_CLOSING was sent  

## LSL Streams (and matlab)
LSL-Mouse streams data and markers following the LSL specifications
https://github.com/sccn/xdf/wiki/Specifications  

### Data
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


### Markers
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
