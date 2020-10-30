
# LSL-Mouse/DEV

## V 1.2.0 release candidate
Error rate display added  

`class PerformanceAtTask` computes the effective performance at each new sample

The logic is as follows :  
- count starts when the move has reached the path
- count a move from point A to point B if both A and B are inside/outside
- count for all record periods : effective performance is the sum of all periods (except the moves to reach the path)
- count a move from A to B in two metrics:   
  - Euclidian distance (pixel) : the classical distance from A to B.
    - For a same angular distance, a move that is farther from the circle centre will produce a longer path
  - Angular distance (radian): the phase angle from A to B
    - For a same Euclidian distance, a move that is farther from the circle centre will produce a smaller angle


In the computations, the effective performance is updated at each new move, following the logic :
- we want the standard deviation of the distribution of the radius of the trajectory
- we compute the sum of squares (and mean) incrementally ([see formula](http://datagenetics.com/blog/november22017/index.html))
- we use the sum of square to get the standard deviation
- we use the standard deviation to get the effective tolerance

This is done within `setEffectivePerformance`:

``` Java
private void setEffectivePerformance (double radius, int X, int Y ) {
double currentMean = radius;
double currentSumS = 0 ;
int currentNbSample = nbSample + 1;

// 	http://datagenetics.com/blog/november22017/index.html
currentMean = radiusMean + (radius - radiusMean) / currentNbSample ;
currentSumS = radiusSumS + (radius - radiusMean) * (radius - currentMean);

radiusMean = currentMean;
radiusSumS = currentSumS;
radiusStd  = stdev(currentSumS, currentNbSample);
effectiveTolerance = Math.sqrt(2 * Math.PI * Math.E) * radiusStd;

nbSample = currentNbSample;

//System.out.println( nbSample + ","+ X + "," + Y + "," + radius + "," + radiusMean + "," + radiusSumS + "," + radiusStd);			  

}
```

One tricky part is to properly compute the angular metrics...
My solution is to :
- shift (translate) X,Y to center of circle
- compute angle from horizontal using `atan`
- get the unsigned difference in angle (do not care if moving clockwise or not)
- take care of 'jumps' around 0 with atan...

``` Java
private double phaseAngleDistance(int cX, int cY, int pX, int pY) {
// translate current XY to center of circle
int Xc = configuration.getCenterX() - cX;
int Yc = configuration.getCenterY() - cY;
// translate previous XY to center of circle
int Xp = configuration.getCenterX() - pX;
int Yp = configuration.getCenterY() - pY;
// get angles from horizontal
double Ac = Math.atan2(Yc, Xc);
double Ap = Math.atan2(Yp, Xp);
// get the unsigned difference in angle (do not care if moving clockwise or not)
double angle = Math.abs(Ac - Ap); // angular distance is unsigned...
// cancel angle jumps around horizontal
if (angle > Math.PI) {
  angle = 2 * Math.PI - angle;
}
// System.out.println(Xc + "," + Yc + "," + Xp + "," + Yp + "," + Ac + "," + Ap  + "," + angle);
return angle ;
}

```



## History

* 2020-01-29 :  
  - type 'w' to display the "effective target width


More detailled information: see in [DOC](/../DOC/).  

-----  
Developed by Pierre JEAN (IMT Mines Alès) and Denis MOTTET (Univ. Montpellier).
