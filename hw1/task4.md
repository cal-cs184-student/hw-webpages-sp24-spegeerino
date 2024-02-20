---
layout: default
title: "Rasterizer - Task 4: Barycentric coordinates"
permalink: /rasterizer/task4/
---

# Task 4: Barycentric coordinates
[Back to main page]({{site.baseurl}}/rasterizer/)

This task involved implementing triangles with a bit more variation in color; in particular, triangles that blend smoothly between three colors defined at their vertices.
The way that this is achieved in practice is by using barycentric coordinates, a coordinate system that is entirely based on the given triangle and its vertices.

## What are barycentric coordinates?
In loose terms, the barycentric coordinates of a point come from the fact that every point inside a triangle can be written as a weighted average of the triangle's vertices (when we take an average of points, we just average the coordinates individually).
For example, look at this triangle.
![Color-interpolated triangle](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task4-example-triangle.png "A color-interpolated triangle")
This triangle has a pure green associated with the bottom left corner, red with the bottom right, and blue at the top.
Then, points inside the triangle are colored based on the weights in the weighted average of the vertices, which causes the colors to blend smoothly between red, green, and blue.
If we move closer to a given vertex, its weight goes up in the weighted average, which means that as we get closer to the blue vertex on the top, the color of the triangle becomes more blue, as expected.
Note that, in order to compute a weighted average, we require the sum of the weights to be 1 (for it to actually be an average). 

## How does computation work?
In order to create this effect in the rasterizer, we have to actually compute the correct weights for any given point \\(P\\).
One strategy that's fairly intuitive is to draw the line \\(\overline{AP}\\), then see where it intersects line \\(\overline{BC}\\) (say, at point \\(D\\)).
Then, we can do two linear interpolations: we write \\(D\\) as a weighted sum of \\(B\\) and \\(C\\), which we can do by just comparing the distances \\(BD\\) and \\(CD\\) (if either of them are equal to the whole length of the side \\(BC\\), then we know the point isn't in the triangle and we can stop.)
This gives us the relative weight of \\(B\\) compared to \\(C\\) for \\(P\\).
Then, we interpolate the position of \\(P\\) on the line \\(\overline{AD}\\), and then the proportion of the weight of \\(A\\) to \\(D\\) in this interpolation is the relative weight of \\(A\\) to the combined weight of \\(B\\) and \\(C\\).

Practically speaking, this algorithm is quite slow because we have to compute distances, which involve doing square roots, but conceptually it's what we want.
We can use this fact to prove formulas that are faster to compute; the one I ended up using involves the proportion of the area of \\(\triangle BPC\\) compared to \\(\triangle ABC\\) to compute the weight of \\(A\\) in the barycentric coordinates of \\(P\\), which can be computed using some clever cross-product relation which means we only need to do 2 divisions to compute the weights (we use the formula twice and then subtract both from 1 to obtain the third barycentric coordinate.)

Once we have these coordinates, we can simply use the same weights on the colors of each vertex to get a blended color based on the point that the pixel occupies.

## The output
We can use this color interpolation to use a lot fewer triangles than solid color triangles would require in cases where colors are blended smoothly.
For example, take this color wheel:
![Color wheel](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task4-color-wheel.png "Color wheel")

[Back to main page]({{site.baseurl}}/rasterizer/)