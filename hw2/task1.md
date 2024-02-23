---
layout: default
title: "Homework 2: MeshEdit"
permalink: /meshedit/task1/
author: Aditya Baireddy and Andrew Liu
---
# Task 1: Bezier curves with 1D de Casteljau subdivision
[Back to main page]({{site.baseurl}}/meshedit)

## What are Bezier curves, and what are they for?
A large part of computer graphics involves using points, lines, and polygons to approximate real world objects.
However, these don't necessarily work that well for objects that are curved; it takes a lot of small straight objects to approximate the shape of a curved one.
So, there are situtations where it's easier to render objects that are inherently curved; like spheres, parabolas, or ellipsoids.
But even introducing these objects doesn't capture the full curvature of many different types of objects.
In order to mimic curvature that changes more unpredictably than the standard objects listed above, we would still need a lot of copies of spheres/parabolas/etc. to get a shape similar to the curved surface we're trying to approximate.

So, Bezier curves provide an alternative strategy for drawing curves that doesn't require a large amount of objects.
Instead, we can define *control points*, which give us a lot of freedom to manipulate the curve basically however we want. 
Control points can be moved around to "pull" the curve into the shape you want it to be.
This allows us to draw outlines of complex object contours with a single smooth function, which can create a more aesthetically pleasing result when rendered.
## Okay, but how do you draw that?
To define a Bezier curve in more detail, we need to be more specific about how these control points actually control the curve.
So, to actually define the points on a Bezier curve, we have some number of control points and a parameter \\(t\\) ranging from 0 to 1. 
Let's call our \\(n\\) control points \\(P_{1,0}, P_{2,0}, \cdots, P_{n,0}\\).
In the case below, \\(n = 6\\), and \\(t = 0.5\\).
![Bezier curve computation iteration 0]({{site.baseurl}}/docs/assets/hw2images/task1-example-iter0.png)
Then, the first step is to linearly interpolate between the \\(n\\) consecutive points a proportion of \\(t\\) along each segment \\(\overline{P_{i,0}P_{i+1,0}}\\) to get the point \\(P_{i, 1}\\).
This will give you \\(n-1\\) points \\(P_{1,1}, P_{2,1}, \cdots, P_{n-1, 1}\\).
![Bezier curve computation iteration 1]({{site.baseurl}}/docs/assets/hw2images/task1-example-iter1.png)
From here, we just repeat the process: now we have \\(n-1\\) control points, and we iterate repeatedly until we get down to 1 point left (using the same parameter \\(t\\) at each step).
Here's what that looks like:
![Bezier curve computation iteration 2]({{site.baseurl}}/docs/assets/hw2images/task1-example-iter2.png)
![Bezier curve computation iteration 3]({{site.baseurl}}/docs/assets/hw2images/task1-example-iter3.png)
![Bezier curve computation iteration 4]({{site.baseurl}}/docs/assets/hw2images/task1-example-iter4.png)
![Final Bezier curve point at parameter t = 0.5]({{site.baseurl}}/docs/assets/hw2images/task1-example-iter5.png)
The red point is the end result of the iteration, and this point is the point on the Bezier curve corresponding to the parameter \\(t = 0.5\\).
## The final result
In fact, the procedure described above is the method of de Casteljau subdivision for computing points of a Bezier curve.
There is another algebraic definition of a Bezier curve, but understanding that definition intuitively requires going through this procedure anyway.
So, we thought it made more sense to just define the Bezier curve by the recursive process instead.
The Bezier curve in full, then, is a function that takes in this parameter \\(t\\) on the interval \\([0,1]\\) and returns a point by executing this procedure.
When the whole curve is rendered at the same time, it looks like this (in green):
![Full Bezier curve render]({{site.baseurl}}/docs/assets/hw2images/task1-full-curve.png)
Notice that the first and last control point are always the endpoints of the curve; they correspond to the points at \\(t=0\\) and \\(t=1\\).
If we adjust the parameter \\(t\\), we can see that the point lands on a different part of the curve:
![Full Bezier curve render, t = 0.25]({{site.baseurl}}/docs/assets/hw2images/task1-full-curve-lowt.png)
We can adjust the curve by moving the control points.
One simple adjustment we can make is to "pull" the curve downwards towards the end by moving down a later control point, as shown below:
![Adjusted curve render]({{site.baseurl}}/docs/assets/hw2images/task1-adj-curve.png)
