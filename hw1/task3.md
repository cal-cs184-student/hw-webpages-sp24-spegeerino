---
layout: default
title: "Rasterizer - Task 3: Transforms"
permalink: /rasterizer/task3/
---

# Task 3: Transforms
This task involved allowing for transforms in the svg file.
Essentially, this boiled down to computing the right matrices to scale, rotate, and translate certain points and vectors in homogenous 2D coordinates. 
Then, we simply multiply each coordinate of interest (usually vertices of triangles) by the various transforms applied to it and get out the correct homogenous coordinates in the picture.
Then those coordinates are translated into pixel space coordinates and passed to the rasterizer to start actually computing pixel color values. 

## The right matrices
In homogenous 2D coordinates, a point (\\(x,y,s\\)) has an x-component, a y-component, and a *scale*, which corresponds to how much the two coordinates are scaled by. 
If we want to translate by some vector \\((\mathrm{d}x, \mathrm{d}y)\\), we simply need to map the given point to \\((x + s\mathrm{d}x, y + s\mathrm{d}y, s)\\).
The corresponding matrix transformation is therefore