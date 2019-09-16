---
layout: page
title: Basics
permalink: /basics/
order: 1
---

{% include description.md %}

**Ray tracing** is a technique of generating photorealistic images via simulation of light rays propagation according to the rules of geometric optics.

**Monte-Carlo** is a method of finding integrals by accumulating values in randomly sampled points of the function being integrated.
In ray tracing we are finding the light integral in each pixel of our virtual camera. The Monte-Carlo method converges relatively slowly (the error is inversely proportianl to the square root of the number of samples), but the sampling could be effectively preformed in parallel, so it's a good idea to run Monte-Carlo integration on massively parallel hardware like GPUs. 

Clay uses backward propagation of rays from camera to scene. For each pixel of the image the ray is casted from the camera towards the specific direction defined by camera orientation and coordinate of the pixel. The ray can hit object, in this case it will be absorbed by object and *one or zero* secondary rays will be produced.
