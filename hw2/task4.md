---
layout: default
title: "Homework 2: MeshEdit"
permalink: /meshedit/task4/
author: Aditya Baireddy and Andrew Liu
---
[Back to main page]({{site.baseurl}}/meshedit)
# Task 4: Edge flip
The overall goal of this task was to flip an edge, which involved several pointer reassignments, as shown in the below diagram:

<p style = "text-align:center">
	<img src="/hw-webpages-sp24-spegeerino/docs/assets/hw2images/task4-edge-flip.png" alt="a diagram showing the edge flip operation" width="30%" style="text-align:center"/>
</p>

## The theory

To accomplish this, we flipped the edge in place with the following reassignments:

- Half Edges
	- \\(h_0\\)
		- next = \\(ba\\)
		- vertex = \\(d\\)
		- twin = \\(h_1\\) (unchanged)
		- edge = \\(e_0\\) (unchanged)
		- face = \\(f_0\\) (unchanged)
	- \\(h_1\\)
		- next = \\(dc\\)
		- vertex = \\(b\\)
		- twin = \\(h_1\\) (unchanged)
		- edge = \\(e_0\\) (unchanged)
		- face = \\(f_0\\) (unchanged)
	- \\(ad\\)
		- next = \\(h_0\\)
	- \\(dc\\)
		- next = \\(cb\\)
	- \\(cb\\)
		- next = \\(h_1\\)
	- \\(ba\\)
		- next = \\(ad\\)
- Faces
	- \\(f_0\\)'s halfedge was set to \\(ad\\)
	- \\(f_1\\)'s halfedge was set to \\(cb\\)
- Vertices
	- \\(a\\)'s halfedge was set to \\(ad\\)
	- \\(c\\)'s halfedge was set to \\(cb\\)
- Edges
	- \\(e_0\\) remained unchanged 

Our debugging journey mostly consisted of some weird bugs where an edge would flip only after 3 flips (the first two flips would delete faces from the render accidentally). The main problem we had was not understanding C++ references and how we could assign to the reference returned by a function. Once we learned this, we assigned the correct pointers, and finished the task. Lastly, below is a picture of our teapot with a few edges flipped:

## The practice

<p style = "text-align:center">
	<img src="/hw-webpages-sp24-spegeerino/docs/assets/hw2images/task4-flipped-teapot.png" alt="a mesh of a teapot with a few edges flipped" width="40%" style="text-align:center"/>
</p>