---
layout: default
title: "Homework 3: PathTracer"
permalink: /pathtracer/task1/
author: Aditya Baireddy and Andrew Liu
---
[Back to main page]({{site.baseurl}}/pathtracer)
# How to raytrace
In broad terms, the rendering pipeline for raytracing consists of the following steps performed for every pixel on the screen:
1. Generate a fixed number $N$ of sample rays starting at the camera and pointing to a random position within the pixel.
2. For each ray, check if it intersects any *primitives* (for us, triangles or spheres) in the scene.
3. If it does, the primitive we intersect will return its color based on the normal vector of the primitive at the point of intersection. Otherwise, we'll sample the environment's ambient lighting in the direction that the ray travelled in. (Color sampling was implemented for us, so all we had to do was call the appropriate function.)
4. Average the colors that we get from each ray, and set the pixel to be that color. 
The first two steps of this process were conceptually the hard parts of this task, so we'll explain them in more detail.

# Ray generation
Ray generation involves a lot of changes of perspective.
When we randomly generate a position within a pixel, we do it in *screen coordinates*, which range from $(0,0)$ to $(W, H)$ where $W,H$ are the width and the height of the screen in pixels: our pixel is at $P_o = (x_o,y_o),$ and we generate some sample point $P = (x,y)$ in the bounds between $(x_o,y_o)$ and $(x_o+1, y_o+1)$.
However, we want to generate a ray in *world space,* which is the coordinate system our primitives and light sources use for the most part when it comes to handling intersections.
To do this, we have to translate between a lot of different coordinate systems.
Our first step is to normalize $P$ into screen coordinates from $(0,0)$ to $(1,1)$ by scaling down by $W$ and $H$, which gives us *normalized screen coordinates*, which is what the camera's `generate_ray(x,y)` function takes in.
Then, we can call `generate_ray(x,y)`, which will do the following:
1. Compute the position of the screen in camera space. Using the camera's horizontal and vertical field of view, the fact that the screen plane is 1 unit away from the camera in the negative $z$ direction, and some trigonometry, we can compute the width and height of the screen plane in camera space. Finally, the point in the middle of the screen is always $(0, 0 -1)$ in camera space, which pinpoints the screen's exact location.
2. Translate the normalized screen coordinates of the sample point into *camera space coordinates*. We do this by linearly interpolating the position of the pixel between the sides of the screen plane that we found in step 1.
3. Multiply by the camera's built in camera-to-world transformation matrix to get the position of the sample point in world space.
4. Finally, construct a ray between the camera's position in world space (stored in the Camera object) and the sample point's position in world space (which we just calculated) and return it.

# Primitive intersection
Once we've actually generated the ray, we have to then check if it intersects any of the primitives in our scene.
The process for this (for now) just consists of looping over every primitive in the scene and calling its own `intersect(Ray)` function to determine if the intersection occurs, and then returning at which primitive the intersection is closest to the ray origin (and thus closest to the camera).
The actual workings of the `intersect(Ray)` function depend on what primitive we're working with: for us we have two main ones, a triangle and a sphere.
Sphere intersections are computed by solving an equation relating distance of a point from a line and the radius of the sphere, while triangle intersections can be done in a variety of ways. 
The method we chose is explained in the next section. 

## Computing triangle intersections
To render raytraced scenes quickly, we need to optimize primitive intersections, since we compute them quite often.
We implemented the Möller–Trumbore triangle intersection algorithm, which uses principles of linear algebra to reuse as much work as possible.
Our inputs are a triangle $\triangle ABC$[^1], a ray $\overrightarrow{r}$ with an origin $O$ and a direction $D$ (as well as bounds determining where along the ray we want to process intersections), and an intersection structure meant to store data if we find the intersection (All coordinates are in world space). 

First, we check to see if the ray is parallel to the triangle: to do this, we take the determinant of the 3x3 matrix with columns $\overrightarrow{AB}, \overrightarrow{AC}, D$. If the ray is parallel, the determinant of the matrix will be 0 since the columns would be linearly dependent. In that case, we return immediately and say the ray does not intersect the triangle (technically, it's possible the two are on the same plane, but this affects 0 area on the screen anyway so it's not an issue).

If the ray is not parallel to the triangle, we know that the ray must intersect the plane that the triangle is on, even if it's not within the triangle itself. We can parameterize this intersection as $O + tD$ for some real number $t$.
Then, we use barycentric coordinates to define the same point on the plane of the triangle, which gives us the equation
\\[O + tD = (1-u-v)A + uB + vC\\]
We rewrite this as $O + tD = A + u(B-A) + v(C-A) = A + u\overrightarrow{AB} + v\overrightarrow{AC}$, and then we can bring the constants to one side to get a matrix equation:

<p>
\[
\begin{pmatrix}
\\
-D & \overrightarrow{AB} & \overrightarrow{AC} \\
\\
\end{pmatrix}
\begin{pmatrix}
t \\
u \\
v
\end{pmatrix} = \mathbf{M}\mathbf{x} = O - A
\]
</p>

Now, in order to solve this equation for $t,u,v$, we can apply Cramer's rule and compute the determinants of $\mathbf{M}$ with one column replaced by $O-A$.
Then we need to check each value to see if the intersection of the ray with the plane is in the correct place; if any of these checks fail, we can immediately return that no intersection exists. 
First, we compute $t$ and check that $t$ falls between the ray's bounds for where its intersection are allowed to occur.
Then, we compute $u$, which has to fall between $0$ and $1$.
Finally, we compute $v$, and check that $v > 0$ and $u + v < 1$. 
If all of these checks pass, then the barycentric coordinates of $O + tD$ fall inside the triangle, so we can compute the intersection point using them and populate the intersection struct accordingly, and report that we have found an intersection.

### That's a lot of determinants, isn't it?
So far, this algorithm appears quite inefficient: we compute four separate determinants of 3x3 matrices ($1$ for parallel check, $3+1$ for Cramer's rule, but the fourth one is the same matrix as the parallel check), which should be really slow.
However, the determinants involve a lot of the same vectors, which actually allows us to save a LOT of operations.
We make use of the fact that $\(\mathbf{u} \times \mathbf{v}\) \cdot \mathbf{w}$ is actually the determinant of the matrix with columns $\mathbf{u}, \mathbf{v}, \mathbf{w}$. 
Further, we can do a lot of tricks with swapping columns flipping the sign of the determinant to reuse cross products that we've already computed.
Overall, in the worst case, we do 2 cross products and 4 dot products, which is 18 multiplications and 12 additions, compared to 48 multiplications and 20 additions for a naive determinant computation. 

# The result
<p style = "text-align:center">
	<img src="/hw-webpages-sp24-spegeerino/docs/assets/hw3/task1-spheres.png" alt="spheres in a room" width="30%" style="text-align:center"/>
	<img src="/hw-webpages-sp24-spegeerino/docs/assets/hw3/task1-banana.png" alt="banana on a pedestal" width="30%" style="text-align:center"/>
	<img src="/hw-webpages-sp24-spegeerino/docs/assets/hw3/task1-cow.png" alt="cow." width="30%" style="text-align:center"/>
</p>

## Footnotes
[^1]: Technically speaking, the triangle itself calls an intersection function with the ray and intersection struct, so it's not an input, but it's functionally equivalent.