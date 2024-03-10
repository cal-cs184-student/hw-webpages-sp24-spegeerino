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
3. If it does, the primitive we intersect will return its color based on the normal vector of the primitive at the point of intersection. Otherwise, we'll sample the environment's ambient lighting in the direction that the ray travelled in.
4. Average the colors that we get from each ray, and set the pixel to be that color. 

# Computing triangle intersections
## The algorithm
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

## That's a lot of determinants, isn't it?
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