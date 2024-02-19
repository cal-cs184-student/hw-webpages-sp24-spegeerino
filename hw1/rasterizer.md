---
layout: default
title: "Homework 1: Rasterizer"
permalink: /rasterizer/
---

# Homework 1: Rasterizer
[Table of Contents]({{site.baseurl}}/rasterizer#table-of-contents)
## Introduction
This was the first homework/project for CS 184, which involved implementing a rasterizer for SVG files so they could be displayed in PNG format. 
Simple colored points and lines are drawn (although without any antialiasing).
The rasterizer handles any solid-color polygon in SVG format, by breaking it down into several solid-color triangles and then drawing each individually.
The rasterizer also has support for the "tri-color" triangle, which supports interpolating colors between the three vertex colors to get a spectrum of colors.
Finally, texture-mapping onto triangles is also supported, with bilinear and trilinear sampling from mipmaps (along with some less advanced options).  

# Showcase

# [Task 1: Drawing single-color triangles]({{site.baseurl}}/rasterizer/task1/)

![Sample rate 1 render of dragon](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task1-render-basic-svg3.png "Sample rate 1 render of dragon")

![Sample rate 1 render of colored triangles](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task1-render-basic-svg4.png "Sample rate 1 render of several colored triangles")

![Sample rate 1 render of a cube](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task1-render-basic-svg5.png "Sample rate 1 render of a cube")

![Sample rate 1 render of stars and hexagons](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task1-render-basic-svg6.png "Sample rate 1 render of stars and hexagons")

# [Task 2: Antialiasing by supersampling]({{site.baseurl}}/rasterizer/task2/)

## Triangles comparison
### 1 sample per pixel
![Sample rate 1 render of colored triangles with pixel viewer](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task2-svg4-SR1.png "Sample rate 1")
### 4 samples per pixel
![Sample rate 4 render of colored triangles with pixel viewer](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task2-svg4-SR4.png "Sample rate 4")
### 16 samples per pixel
![Sample rate 16 render of colored triangles with pixel viewer](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task2-svg4-SR16.png "Sample rate 16")
## Cube comparison
### 1 sample per pixel
![Sample rate 1 render of cube with pixel viewer](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task2-svg5-SR1.png "Sample rate 1")
### 4 samples per pixel
![Sample rate 4 render of cube with pixel viewer](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task2-svg5-SR4.png "Sample rate 4")
### 16 samples per pixel
![Sample rate 16 render of cube with pixel viewer](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task2-svg5-SR16.png "Sample rate 16")

# [Task 3: Transforms]({{site.baseurl}}/rasterizer/task3/)
![Robot doing a cartwheel](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task3-my-robot.png "Robot doing a cartwheel")

# [Task 4: Barycentric coordinates]({{site.baseurl}}/rasterizer/task4/)
![Color wheel](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task4-color-wheel.png "Color wheel")

# [Task 5: "Pixel sampling" for texture mapping]({{site.baseurl}}/rasterizer/task5/)
![Campanile with bilinear sampling](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task5-campanile-bilinear)

# [Task 6: "Level sampling" by mipmaps for texture mapping]({{site.baseurl}}/rasterizer/task6/)

# [Something creative?]({{site.baseurl}}/rasterizer/ec/)

# Table of contents

[Task 1: Drawing single-color triangles]({{site.baseurl}}/rasterizer/task1/)

[Task 2: Antialiasing by supersampling]({{site.baseurl}}/rasterizer/task2/)

[Task 3: Transforms]({{site.baseurl}}/rasterizer/task3/)

[Task 4: Barycentric coordinates]({{site.baseurl}}/rasterizer/task4/)

[Task 5: "Pixel sampling" for texture mapping]({{site.baseurl}}/rasterizer/task5/)

[Task 6: "Level sampling" by mipmaps for texture mapping]({{site.baseurl}}/rasterizer/task6/)

[Something creative?]({{site.baseurl}}/rasterizer/ec/)