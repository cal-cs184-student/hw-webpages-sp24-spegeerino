---
layout: default
title: "Homework 2: MeshEdit"
permalink: /meshedit/task6/
author: Aditya Baireddy and Andrew Liu
---
[Back to main page]({{site.baseurl}}/meshedit)
# Task 6: Loop subdivision for mesh upsampling
## What is loop subdivision?
Sometimes, we may want to increase the number of polygons in our mesh to smooth out jagged edges and create a more smoothly curved surface, specifically by adding more polygons to areas where faces meet at sharp angles. This is called *mesh upsampling.* One approach to rounding out these edges is *loop subdivision* for triangle meshes, which consists of dividing each triangle into 4 by connecting the midpoints of each side, then updating the positions of vertices as follows:
- For a vertex that existed in the old mesh, we interpolate between the original position of that vertex and the average of all of its neighboring vertices *in the original mesh*. To be precise, we weight the original position of the vertex by \\(1-nu\\), where \\(n\\) is the *degree* (number of neighboring vertices) of the vertex and \\(u\\) is \\(\frac{3}{16}\\) if \\(n = 3\\), otherwise \\(\frac{3}{8n}\\). Then we weight the sum of the positions of the neighbor vertices by \\(u\\).
- For a vertex that was added in the creation of the new mesh (essentially a vertex that used to be the midpoint of an edge), we assign its position based on the four vertices involved in splitting the edge (in the original mesh) the vertex is the midpoint. We add \\(\frac{3}{8}\\) times the positions of the two endpoints of the edge, and \\(\frac{1}{8}\\) times the positions of the two other vertices that form the two triangles with the relevant edge.

Here is a diagram to show what these updated positions look like geometrically:
![Diagram showing updated vertex positions]({{site.baseurl}}/docs/assets/hw2images/task6-vertex-positions.png)
*Credit to CS 184 staff for this diagram*

This overall has the effect of pushing new vertices slightly outwards, while pulling old vertices in, which smooths out harsh edges and introduces some new curvature.

## How do we implement it?
Our implementation was essentially split into 5 steps:
1. First, iterate over all vertices currently in the mesh (the "old" vertices) and compute what its new position will be in the upscaled mesh. Store this value in the newPosition field of the vertex.
2. Then, iterate over all edges currently in the mesh and compute what the position of the vertex that will be created by splitting this edge should be. Store this value in the newPosition field of the edge.
3. Split every *old* edge in the mesh (don't create new edges then split them too, or we'll be stuck in an infinite loop). While we're doing this, we also update any new edges we create (edges that are actually created by the edge split operation, not just the result of a pre-existing edge being split in two) to keep track of their new status in the isNew field.
4. Flip any *new* edge (determined in step 3) that connects a new vertex and an old vertex. After this step is complete, we have subdivided every triangle in the manner described in the previous section.
5. Update every vertex to its new position as calculated in steps 1 and 2.

### Wait, this works?
It may seem surprising that steps 3 and 4 actually do divide every triangle in the 4-1 fashion that we described conceptually above.
However, we can just consider an arbitrary triangle in the mesh; we split its three edges in some arbitrary order.

<p style = "text-align:center">
	<img src="/hw-webpages-sp24-spegeerino/docs/assets/hw2images/task6-subdivision-proof.png" alt="Diagram of equivalence between subdivision generation methods" width="60%" style="text-align:center"/>
</p>

In this diagram, we split the red outer edge first giving us the red edge through the triangle, then we split the blue and green outer edges in any order giving us the other blue and green edges.
The first edge we split will create a new edge that connects an old vertex (the one between the green and blue edges) and a new vertex (the one in the middle of the red edge).
Then, the other two edges will connect exactly as they're supposed to in order to subdivide the triangle into 4 triangles.
Now, all we have to do is flip the red inner edge and we'll split the triangle as desired; the red inner edge will now connect the pure blue vertex and pure green vertex, which is exactly what we wanted to happen, and we've divided the mesh like we were supposed to.

### Debugging woes

We initially had a bug where some vertices' positions were getting set to 0; this was because we didn't realize that a new vertex's halfedge may not point to the edge that stored the vertex's new position, which means it was just getting set to 0 instead because the relevant edges hadn't had their "newPosition" field set. 
So, when we found a new vertex (denoted by the isNew flag) and tried to check the relevant edge for its newPosition field by checking the vertex's halfedge's edge, we sometimes found a blank newPosition field and set the vertex's position to 0, which was not great.
Once we realized this, we implemented a nice trick that got around this problem entirely.
In particular, we set the vertex's newPosition field during the edge split operation (where we have immediate access to both the vertex and the edge that we're splitting). At the end, we don't have to search for the correct edge anymore; we just set all vertices' positions to their newPosition field.