---
layout: default
title: "Rasterizer - Task 1: Drawing single-color triangles"
permalink: /rasterizer/task1/
---

# Task 1: Drawing single-color triangles

## The rasterization process (the naive method)

Let's say the input to our `rasterize_triangle()` function is 3 points and a color: the three points being \\(A, B, C\\), the vertices of our triangle of interest.
The goal is to only fill indices in the pixel buffer which correspond to points inside the triangle. 
In order to do this, the code begins by determining the bounding box of the triangle; it takes the minimum and maximum of both the \\(x\\) and \\(y\\) coordinates (call these \\(x_L, y_L, x_U, y_U\\)). 
Then, the rectangle with two corners \\((x_L, y_L), (x_U, y_U)\\) contains the entire triangle, so we only have to test pixels lying inside this bounding box for containment in the triangle.
In order to find which of these pixels should be colored, I used the line-test approach for determining triangle containment of a point.

For the line tests to work correctly, it's necessary to ensure that our points are in counterclockwise (CCW) order, so we check that before beginning to iterate over pixels in the bounding box.
This can be done by taking the cross product of the two vectors \\({AB}, {AC}\\).
If the points are in CCW order, the angle \\(\theta\\) between \\({AB}, {AC}\\) is between 0 and \\(\pi\\), so that the cross product \\({AB} \times {AC} = |{AB}||{AC}| \sin \theta\\) is positive.
Otherwise, the angle \\(\theta\\) is between \\(-\pi\\) and 0, so that the cross product is negative.
If the cross product is negative, the code simply interchanges points \\(B\\) and \\(C\\), which changes the order of the vertices to CCW from clockwise (CW).

Now that the points are in CCW order, the line tests can be computed, so we can progress to iterating over all pixels in the bounding box and evaluating the line test at each point.
The line test for a given point \\(P\\) and some "directed" side of the triangle (for example \\(\overline{AB}\\)) involves taking the cross product of vectors \\({AB}\\) and \\({AP}\\); if it's positive, then \\(P\\) is on the "left" side of the line, and otherwise \\(P\\) is on the "right" side of the line.
Since the vertices of the triangle are in CCW order, a point needs to be on the "left" of every one of these directed sides to be inside the triangle.
Therefore, we take the coordinates of the pixel (and offset by 0.5 as necessary) and compute the three line tests. 
If we find that all of these line tests return a positive integer, we can call `fill_pixel()` on the corresponding pixel with the given color, and that's the rasterization procedure.
And, just to be clear, since the code only ever iterates over the pixels in the bounding box, the required runtime complexity bound is achieved.

## The output
![Sample rate 1 render of colored triangles with pixel viewer](images/task1%20-%20svg4-w-pixel-viewer.png "Sample rate 1 render of colored triangles with pixel viewer")

We can see some pretty heavy aliasing here in the pixel viewer, owing to the fact that this simple rasterizer doesn't take any measures to prevent it.
We address this problem in the next task.

## Some optimizations
We do a lot of redundant and otherwise unnecessary work in this computation, which slows down the rasterizer.
One implementation I implemented is to factor out some computations from the inner loops; we can compute the coefficients of the line test cross product in advance outside of the loop, meaning we don't have to recompute them for each line test.
Further, once we're within the x-loop but outside the y-loop we can do some additional multiplications that won't have to be repeated inside the y-loop each time for some additional speedup.

Unfortunately, the speedup from these optimizations was relatively minor. 
The best speedup came from quitting out of the inner loop early when we know that one of the line tests will never pass again.
To be more precise, if the coefficient of \\(y\\) is negative for one of the line tests, we know that once the computed value is negative, it won't get positive again from increasing \\(y\\), so we can quit out of the loop immediately.
Essentially what this means is once we go from the left side from the right side of a side, we can progress to the next value of \\(x\\) immediately, and skip some line test computations.

Here are some timing comparisons for my naive implementation vs my optimized implementation, ranging over several SVGs:

| Trial | Naive | Optimized |
| --- | --- | --- |
| Dragon 1 | 94.809 ms | 54.347 ms |
| Dragon 2 | 70.393 ms | 44.322 ms |
| Dragon 3 | 72.599 ms | 43.019 ms |
| Dragon 4 | 73.202 ms | 44.506 ms |
| Dragon 5 | 74.397 ms | 46.275 ms |
| Dragon Average | 77.080 ms | 46.494 ms |
| Triangles 1 | 8.417 ms | 4.777 ms |
| Triangles 2 | 7.292 ms | 4.504 ms |
| Triangles 3 | 5.452 ms | 5.413 ms |
| Triangles 4 | 10.935 ms | 3.935 ms |
| Triangles 5 | 7.868 ms | 7.999 ms |
| Triangles Average | 7.993 ms | 5.326 ms |

On the dragon svg, the optimized implementation rasterized in approximately 60.3% of the time (a 65.8% speedup).
On the triangle svg, the optimized implementation rasterized in approximately 66.6% of the time (a 50.1% speedup). 