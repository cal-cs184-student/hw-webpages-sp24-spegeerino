---
layout: default
title: "Homework 2: MeshEdit"
permalink: /meshedit/
author: Aditya Baireddy and Andrew Liu
---
# Homework 2: MeshEdit
[Table of Contents]({{site.baseurl}}/meshedit#table-of-contents)
## Introduction
This homework came in two parts.
The first part was a simple exercise in drawing Bezier curves and surfaces, which involved implementing the method of de Casteljau subdivision.
This process involves looping over a sequence of control points and interpolating between them (according to a parameter \\(t\\)) in a specific way to result in a smoothly curved surface, when ranging \\(t\\) between 0 and 1.

This second part of the homework was about another common method of 3D geometric modeling: *meshes*.
To be specific, we implemented some standard features of a triangular mesh (with an underlying *half-edge* data structure), like Phong shading via weighted area normal vectors and loop subdivision for mesh upscaling.
These features enhance the visual appearance of a mesh modeled around some object; Phong shading allows interpolated shading along the face of a single triangle to make the shading look continuous, and upscaling a mesh gives more detail and smooths out harsh edges in a mesh with few polygons.

Overall, \(reflection on project goes here\)

## Showcase
### [Task 1: Bezier curves with 1D de Casteljau subdivision]({{site.baseurl}}/meshedit/task1)
![Bezier curve render example]({{site.baseurl}}/docs/assets/hw2images/task1-full-curve.png)
### [Task 2: Bezier surfaces with separable 1D de Casteljau]({{site.baseurl}}/meshedit/task2)
![Teapot made of Bezier surfaces]({{site.baseurl}}/docs/assets/hw2images/task2-example.png)
### [Task 3: Area-weighted vertex normals]({{site.baseurl}}/meshedit/task3)
![Cow with Phong shading]({{site.baseurl}}/docs/assets/hw2images/task3-showcase.png)
### [Task 4: Edge flip]({{site.baseurl}}/meshedit/task4)

### [Task 5: Edge split]({{site.baseurl}}/meshedit/task5)

### [Task 6: Loop subdivision for mesh upsampling]({{site.baseurl}}/meshedit/task6)

## Table of Contents
### Section 1: Bezier curves and surfaces
#### [Task 1: Bezier curves with 1D de Casteljau subdivision]({{site.baseurl}}/meshedit/task1)
#### [Task 2: Bezier surfaces with separable 1D de Casteljau]({{site.baseurl}}/meshedit/task2)
### Section 2: Triangle meshes and the half-edge data structure
#### [Task 3: Area-weighted vertex normals]({{site.baseurl}}/meshedit/task3)
#### [Task 4: Edge flip]({{site.baseurl}}/meshedit/task4)
#### [Task 5: Edge split]({{site.baseurl}}/meshedit/task5)
#### [Task 6: Loop subdivision for mesh upsampling]({{site.baseurl}}/meshedit/task6)