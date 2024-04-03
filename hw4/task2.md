---
layout: default
title: "Homework 4: ClothSim"
permalink: /clothsim/task2/
author: Aditya Baireddy and Andrew Liu
---
{% assign media = site.baseurl | append: "docs/assets/hw4" %}
# Part 2: Simulation via numerical integration
## Overview
In this part, we began to actually simulate the movement of the cloth.
To do this, we used Verlet integration: we compute the net force on each point mass (based on the spring forces and external forces), and then use that to determine the acceleration of that particular part of the cloth.
Also, to prevent the cloth from deforming in strange ways, we constrained point masses to prevent them from getting too far from their neighbors: the lengths of structural and shearing springs are constrained to be at most 10% greater than their rest length after every simulation step.

This physical simulation gives us a lot of parameters to tweak to change the material properties of the cloth.
We'll be taking a look at how changing these parameters changes the simulated motion of the cloth.
## Changing the spring constant
The first thing we can do is to change the underlying spring constant for the springs in the mass-spring representation of the cloth.
Intuitively, increasing the spring constant should make the cloth stiffer, while lowering it should make it bouncier; a lower spring constant will apply less corrective force to deformations, so deformations will last for more time.
In the sequence below, the left column shows a spring constant of 500, the middle column a spring constant of 5,000 (the default), and the right column a spring constant of 50,000.
<p style="text-align:center">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<br>

</p>