---
layout: default
title: "Rasterizer - Task 6: \"Level sampling\" with mipmaps for texture mapping"
permalink: /rasterizer/task6/
---

# Task 6: "Level sampling" with mipmaps for texture mapping
In this task, I implement one final method to help reduce aliasing while trying to compromise as little as possible on image quality.
This is the technique of mipmaps, which adjusts the sampling strategy for each pixel dynamically across the image to try to use high detail where necessary to prevent blurriness and low detail in other places to prevent aliasing.
## What is level sampling?
In order to understand this technique, we need to know what mipmaps are.
Essentially, a mipmap consists of multiple downscaled[^1] copies of a texture image.
Downscaling the image has the effect of blurring the texture together.
A mipmap has copies of the texture repeatedly 2x2 downscaled until there's no detail left (just one color).
Then, every time you sample a pixel from the mipmap, you want to know how much detail is necessary for that portion of the image.

One way to approximate the importance of detail at a certain pixel is to see how far apart neighboring pixels are in texture space.
If two neighboring pixels sample from points really close to each other into the texture, then you want a lot of detail to capture the minute difference between the two pixels.
However, if they sample from points very far away from each other, then all of the detail in the middle is lost anyway, so it's better to sample from a more blurred texture to prevent aliasing.
We can compute this distance using the derivative of the texture space coordinates with respect to the pixel space coordinates, and then we use a heuristic to determine how much of a downscaled texture we want to sample from.
This approach is generally what the mipmap level sampling technique consists of.

## Tradeoffs
Now, we've acquired a small catalog of options for dealing with aliasing in textures.
First, we can supersample, sampling the texture multiple times per pixel to pick up on more detail.
Second, we can interpolate pixel position between texture pixels, which globally decreases aliasing and introduces a slight blur.
Finally, we have level sampling from mipmaps, which allows us to sample from different versions of textures to locally prevent aliasing at points we see fit.

The advantage of the supersample approach is that it will always work, and at a high enough sample rate will produce an image with no aliasing while still avoiding blur.
The main con is that supersampling is really slow; the number of samples per pixel is directly proportional to the runtime, and causes a major slowdown at high sample rates.
Pixel interpolation of textures is not nearly as slow, but it has the unfortunate side effect that it applies a global blurring effect to the whole image.
As such, it can never fully eliminate aliasing, and will reduce detail in parts of the image that didn't have aliasing in the first place.
Finally, we have mipmaps and level sampling, which is fairly fast as well and has the added bonus of doing a much better job of only antialiasing parts of the image that actually need it. 
This efficacy and time efficiency comes at the cost of taking up more memory than either of the other approaches (each texture will take up \\(\frac{4}{3}\\) as much memory); it also requires the precomputation of downscaled images to be able to be implemented.

## The comparison
Here are a couple of images with different level and pixel sampling techniques used.
The first image is one with no level sampling or pixel interpolation for comparison; the pixel viewer is aimed at the trail of two shooting stars, which can be seen to have significant aliasing.
![Deer level zero nearest pixel sampling](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task6-deer-0N.png)

The next image has interpolation in pixel sampling, but no level sampling; the aliasing is somewhat improved, but we can also see residual blurring even though the aliasing problem is not solved. 
Importantly, this blurring appears in the rest of the image as well, even in places it was unnecessary.
![Deer level zero linear pixel sampling](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task6-deer-0L.png)

The next image uses mipmaps, sampling from the nearest level to the value returned by our heuristic (computed via the derivative) but no pixel interpolation.
We can see here that the streak of pixels is actually connected properly now, but the edges of the line are still quite jagged. 
![Deer nearest level nearest pixel sampling](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task6-deer-NN.png)

Next has both nearest level mipmaps and pixel interpolation, which produces the best result in my opinion.
The streak of pixels connects properly and appears relatively straight to the eye.
Some detail is lost in the rest of the image, but I think the tradeoff is well worth it for how good the comets look.
![Deer nearest level linear pixel sampling](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task6-deer-NL.png)

Finally, there is an advanced technique for level sampling where we interpolate the pixel sample between the two nearest mipmap levels, which can be even more effective in preventing aliasing.
In my opinion, though, this technique is overkill for this specific image, and results in the image losing unnecessary amounts of detail.
The first of the two images below is the level interpolation technique alone, then the last image has both level interpolation and pixel interpolation.
![Deer linear level nearest pixel sampling](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task6-deer-LN.png)
![Deer linear level linear pixel sampling](/hw-webpages-sp24-spegeerino/docs/assets/hw1images/task6-deer-LL.png)

[^1]: Downscaling (\\(n \times n\\) downscaling) consists of grouping pixels together in \\(n \times n\\) groups and then taking the average of all of those groups to make a image that is smaller by a factor of \\(n^2\\) pixels.

 