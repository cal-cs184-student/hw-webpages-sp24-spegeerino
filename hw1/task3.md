---
layout: default
title: "Rasterizer - Task 3: Transforms"
permalink: /rasterizer/task3/
---

# Task 3: Transforms
[Back to main page]({{site.baseurl}}/rasterizer/)

This task involved allowing for transforms in the svg file.
Essentially, this boiled down to computing the right matrices to scale, rotate, and translate certain points and vectors in homogenous 2D coordinates. 
Then, we simply multiply each coordinate of interest (usually vertices of triangles) by the various transforms applied to it and get out the correct homogenous coordinates in the picture.
Then those coordinates are translated into pixel space coordinates and passed to the rasterizer to start actually computing pixel color values. 

## The right matrices
In homogenous 2D coordinates, a point (\\(x,y,s\\)) has an x-component, a y-component, and a *scale*, which corresponds to how much the two coordinates are scaled by. 
If we want to translate by some vector \\((\mathrm{d}x, \mathrm{d}y)\\), we simply need to map the given point to \\((x + s\mathrm{d}x, y + s\mathrm{d}y, s)\\).
The corresponding matrix transformation is therefore

<p>
\[
\begin{pmatrix}
1 & 0 & \mathrm{d}x \\
0 & 1 & \mathrm{d}y \\
0 & 0 & 1 \\
\end{pmatrix}
\]
</p>

For scaling in the \\(x\\)-direction by \\(s_x\\) and the \\(y\\)-direction by \\(s_y\\), we want the matrix

<p>
\[
\begin{pmatrix}
s_x & 0 & 0 \\
0 & s_y & 0 \\
0 & 0 & 1 \\
\end{pmatrix}
\]
</p>

Finally, for rotation by an angle \\(\theta\\) in the counterclockwise direction, we need to rotate the \\(x\\)-coordinate vector and \\(y\\)-coordinate vector by the angle \\(\theta\\), so we want the matrix

<p>
\[
\begin{pmatrix}
\cos \theta & -\sin \theta & 0 \\
\sin \theta & \cos \theta & 0 \\
0 & 0 & 1 \\
\end{pmatrix}
\]
</p>

Once these transforms were implemented, I could transform any svg image I wanted.
I took a provided image of an svg robot, and tried to make it look like it was doing a cartwheel; I don't think I did a very good job, but here it is:
![Robot doing a cartwheel](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task3-my-robot.png "Robot doing a cartwheel")

[Back to main page]({{site.baseurl}}/rasterizer/)