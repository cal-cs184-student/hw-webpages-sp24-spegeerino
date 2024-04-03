---
layout: default
title: "Homework 4: ClothSim"
permalink: /clothsim/task2/
author: Aditya Baireddy and Andrew Liu
---
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
In the sequence below, the left column shows a spring constant of 500, the middle column a spring constant of 5,000 (the default), and the right column a spring constant of 50,000:
<p style="text-align:center">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<br>
Everything starts in the same place.
<br>
<img src="../../docs/assets/hw4/pinned2_lowk_half_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_half_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_highk_half_fall.png" style="width:30%">
<br>
About half of the cloth has been pulled on by the spring forces at this point in the simulation.
Notice how the low spring constant causes a lot of ripples to appear in the cloth, while the higher spring constant cloth appears much more smooth.
There's also a reduction in the curvature between the two pinned points with the higher spring constant cloth.
<br>
<img src="../../docs/assets/hw4/pinned2_lowk_full_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_full_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_highk_full_fall.png" style="width:30%">
<br>
More of the same story here, after the cloth has fallen down almost fully.
This image allows us to see the difference in curvature between the pinned points more easily; as the cloth gets more stiff with a higher spring constant, the curvature is less.
<br>
<img src="../../docs/assets/hw4/pinned2_lowk_rest.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_rest.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_highk_rest.png" style="width:30%">
<br>
Final resting position. 
Again, higher spring constants make the cloth appear smoother and reduces the curvature along the top.
There are less ripples throughout the body of the cloth with higher spring constant.
Also, as a result of some numerical instability, the cloths with lower spring constants "bounce around" slightly even in their rest position, while the higher spring constant cloth stays mostly still.
<br>
</p>

## Changing density
In some ways, changing the density should have inverse effects to changing the spring constant.
The corrective forces stay the same, but now they have more or less effect depending on the effective mass of each point mass.
So, if we increase density, the springs become less effective at preventing deformation, and the cloth should once again become bouncier, and decreasing density should make the cloth stiffer.
The left column is a density of 1.5 g/cm^2, the middle column is a density of 15 g/cm^2 (the default), and the right column is a density of 150 g/cm^2:

<p style="text-align:center">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<br>
Like before, everything starts in the same place.
<br>
<img src="../../docs/assets/hw4/pinned2_lowd_half_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_half_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_highd_half_fall.png" style="width:30%">
<br>
About half of the cloth has been pulled on by the spring forces at this point in the simulation.
The cloth with low density looks much stiffer, while the cloth with high density looks bouncier.
The stiff cloth has reduced curvature between the pinned points, as well as a much smoother surface.

<br>
<img src="../../docs/assets/hw4/pinned2_lowd_full_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_full_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_highd_full_fall.png" style="width:30%">
<br>
More of the same story here, after the cloth has fallen down almost fully.
This image allows us to see the difference in curvature between the pinned points more easily.
Again, note how these images look almost exactly like the spring constant images, just in the reverse direction.
<br>
<img src="../../docs/assets/hw4/pinned2_lowd_rest.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_rest.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_highd_rest.png" style="width:30%">
<br>
Final resting position. 
Again, lower density makes the cloth appear smoother and reduces the curvature along the top.
There are less ripples throughout the body of the cloth with higher spring constant.
Also, as a result of some numerical instability, the cloth with higher density "bounce around" slightly even in their rest position, and the low density cloth stays more or less still.
<br>
</p>
In total, the entire sequence looks like we took the spring constant images and just swapped the left and right column, which goes with exactly what we expected to happen.
The density changes should be inverse to the spring constant changes, because adding mass decreases the effect of a constant force and vice versa.

## Changing damping percentage
The damping percentage essentially tells us how much of each movement to "remove" from consideration; we lessen all changes in position slightly to help keep the simulation stable.
Changing this has some interesting effects.
When we up the damping percentage, we should expect the simulation to take less time to come to rest; the cloth should lose energy faster as a result of the dampened movement and therefore come to rest quicker.
We also expect the cloth to overall move slower, since the dampening effect essentially provides a "terminal velocity" where external forces in the same direction will combine with previous velocity and then get damped back down to the same velocity as before.
Increasing the dampening would lower this terminal velocity, making everything overall slower.
Of course, lowering the damping percentage should have the opposing effect.
In the next sequence of images, the left column has a damping percentage of 0.1%, the middle column 0.2%, and the right column 0.4%:
<p style="text-align:center">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_start.png" style="width:30%">
<br>
<img src="../../docs/assets/hw4/pinned2_lowdamp_half_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_half_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_highdamp_half_fall.png" style="width:30%">
<br>
About half of the cloth has been pulled on by the spring forces at this point in the simulation.
The low damping percentage has fallen much faster, and in general has more speed.
Visually, we can see that lowered damping percentage increases the amount of ripples in the cloth.
<br>
<img src="../../docs/assets/hw4/pinned2_lowdamp_full_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_full_fall.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_highdamp_rest.png" style="width:30%">
<br>
After the cloth has fallen down more, the high damping percentage cloth has already come fully to rest (although it took longer to get here).
The default percentage cloth still has some momentum, and the low damping percentage cloth has a lot more momentum, enough that...
<br>
<img src="../../docs/assets/hw4/pinned2_lowdamp_counter_swing.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_rest.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_highdamp_rest.png" style="width:30%">
<br>
The low damping percentage cloth actually swings a good way past the resting position, and in this image is entering its counter swing.
The other two cloths have already come to rest.
Like before, there are still many more ripples in the low damping percentage cloth as those small details don't get smoothed out when the damping percentage is so low.
<br>
<img src="../../docs/assets/hw4/pinned2_lowdamp_rest.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_default_rest.png" style="width:30%">
<img src="../../docs/assets/hw4/pinned2_highdamp_rest.png" style="width:30%">
<br>
Finally, all the cloths come to rest.
</p>

## Hanging from four corners
We have one more image to show: a cloth hanging from four pinned corners.
We used the default parameters here:
<p style="text-align:center">
<img src="../../docs/assets/hw4/pinned4_rest.png" style="width:80%">
</p>