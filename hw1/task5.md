---
layout: default
title: "Rasterizer - Task 5: \"Pixel sampling\" for texture mapping"
permalink: /rasterizer/task5/
---

# Task 5: "Pixel sampling" for texture mapping
[Back to main page]({{site.baseurl}}/rasterizer/)

## What is pixel sampling?
Textures typically aren't stored as images that look good to human eyes.
A texture meant to be applied to a surface that isn't flat looks quite distorted when viewed as a flat image, which means mapping a texture onto a surface is not as simple as just displaying the texture image in the right place.
In order to make the texture look right, we tell each triangle what parts of the texture it should use, and then take a weighted average between the vertices of the triangle to find the proper pixel(s) of the texture to sample from.
Sound familiar? We're going to use barycentric coordinates again to determine what part of the texture we should color each pixel with, by figuring out what weights we should assign to each vertex. 
This is called "pixel sampling" because we have to compute the barycentric coordinates for each pixel (in fact for each sample point, so if we're supersampling it could be multiple times per pixel) to map the texture value onto.

However, once we compute the position in the texture that we should sample, we still have to translate that back to an actual color. 
Since textures are only discrete (they're PNGs with some finite number of pixels) while the positions we calculate are continuous, we have to come up with some way of determining the color from a position in texture space. 
There are several strategies that are possible for sampling each pixel, but we implemented two. 
The first (and simplest) is nearest pixel sampling, which means we just take the closest pixel of the texture to wherever our interpolation tells us the position is in texture space.
The other method is bilinear sampling, which involves taking the 4 closest pixels and interpolating their color values with respect to the position in texture space of the point that we originally found.

Nearest pixel sampling can lead to some aliasing artifacts in the texture that bilinear sampling can clean up.
On the other hand, bilinear sampling can result in a texture that looks needlessly blurry, so there are tradeoffs between using either sampling method in different circumstances.

## The implementation
Essentially, textures have their own coordinates, in \\(uv\\)-space.
Rendering a textured triangle means we take in 3 vertices of the triangles, with their coordinates in both pixel space and texture space.
When we want to render a textured triangle (which is used to build up everything else that needs to be textured), we first compute the barycentric coordinates like described in task 4.
Then, we use those same weights we computed for pixel space to compute a weighted average in texture space of the texture coordinates of the 3 vertices. 

Now that we have these continuous coordinates, we sample by either rounding the coordinates to the nearest pixel (nearest pixel sampling) or taking the floor and ceiling of the coordinates in both \\(u\\) and \\(v\\) to find the 4 nearest points, then interpolate in both directions to compute a color that's weighted by how close the coordinates were to each of the 4 pixels respectively, which gives us a much larger possible range of colors as opposed to only colors that appear in the original texture.

## Comparison of sampling methods
In some cases, bilinear sampling will be much better looking than nearest sampling. 
For example, look at the next two images; the first is nearest pixel sampling, and the second is bilinear pixel sampling.
You should see that the bilinear sampling renders the spike on top of the Campanile more faithfully than nearest sampling, since when the texture gets really thin towards the top, bilinear sampling can still display a change in color, but nearest sampling just misses the thin strip of pixels entirely.

![Campanile with nearest sampling](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task5-campanile-nearest.png)
![Campanile with bilinear sampling](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task5-campanile-bilinear.png)

However, increasing the sample rate can actually change the favor to nearest sampling; when we sample more, the more densely packed grid of samples can pick up on the details in the texture even with just nearest sampling. 
At that point, bilinear sampling only serves to make everything blurrier and therefore harder to see. 
These next two images are supersampled with a 4x4 grid in each pixel, which in my opinion makes nearest sampling look better than bilinear sampling.

![Campanile with nearest sampling, supersampled](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task5-campanile-supersample-nearest.png)
![Campanile with bilinear sampling, supersampled](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task5-campanile-supersample-bilinear.png)

[Back to main page]({{site.baseurl}}/rasterizer/)