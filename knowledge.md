---
layout: page
title: Knowledge
permalink: /knowledge/
---

{% include knowledge_header.md %}

## Basics

**Ray tracing** is a technique of generating photorealistic images via simulation of light rays propagation according to the rules of geometric optics.

**Monte-Carlo** is a method of finding integrals by accumulating values in randomly sampled points of the function being integrated.
In ray tracing we are finding the light integral in each pixel of our virtual camera. The Monte-Carlo method converges relatively slowly (the error is inversely proportianl to the square root of the number of samples), but the sampling could be effectively preformed in parallel, so it's a good idea to run Monte-Carlo integration on massively parallel hardware like GPUs. 

Clay uses backward propagation of rays from camera to scene. For each pixel of the image the ray is casted from the camera towards the specific direction defined by camera orientation and coordinate of the pixel. The ray can hit object, in this case it will be absorbed by object and *one or zero* secondary rays will be produced.

## Architecture

Clay is a heterogenous computing software, so a lot of entities in it consists of *host* and *device parts*.

*Host part* performs operations on the host that usually is about resourse management, e.g. handling host data and loading it to the device. Host part is also gives information about device part.

*Device part* is a piece OpenCL code that contains some definitions with specific names and functions with specific signatures. The term 'module' is used for this piece of code. Names and signatures of the module content are called the interface, and they are the same for all modules of same type. Corresponding device functionality is fully defined in the module, and resulting OpenCL kernel is assembled from these modules.

The type of device module and the interface are defined by the `Instance<Class>` trait implemented for the entity. You can find more information about classes and its instances in the documentation of [clay-core](https://docs.rs/clay-core/) and [clay](https://docs.rs/clay/) crates.

### Rendering pipeline

![Pipeline](/assets/pipeline.svg)

`Renderer` is the main entity, which is responsible for raytracing process. But it doesn't perform actual computation, it just stores host data and some metainformation.

For actual rendering it produces a `RenderWorker`, which is like a proxy of renderer in a specific `Context` (computing environment for a separate device with its own memory and command queue). There may be multiple workers in different contexts for a single renderer. Worker performs raytracing procedure: it runs OpenCL kernel that computes Monte-Carlo sampling for each pixel and accumulates the result into raw image.

The raw images of all workers after running of multiple rendering procedures are passed to a `Postproc`. It sums up these images, performs some user-defined filtering (e.g. color correction) if it's needed, and outputs the final RGB image that may be saved to a file or drawn in a window. 

### Renderer content

![Renderer](/assets/renderer.svg)

The behaviour of the renderer is defined by `Scene` and `View`. There are some already implemented scenes and views in the library but you can implement your own.

#### Scene

Existing scenes is designed to be a container of objects. Scene device module contains the function that takes a single ray, produced by view, and performs the search for an object hit by this ray. After object is found, an interaction between it and ray is performed, producing a secondary ray if it is needed. Secondary ray is handled in the same manner and this chain process continues until the ray is consumed by the light source or background, or number of bounces of the ray is exceeded the limit.

Scene also has a background which describes what happens to ray that hasn't hit any object on its way.

#### View

The main function of view is to produce rays from each pixel of the viewport (Clay uses reversed ray propagation - from the camera to the light source). View also has device function that takes pixes indices and produces ray computing its origin and direction. Through this mechanism view can simulate (e.g. stochastically) different effects like MSAA and depth-of-field.

### Objects

![Object folding](/assets/object_folding.svg)

## Usage

## Examples
