---
layout: page
title: Architecture
permalink: /architecture/
order: 1
---

## Principles

Monte-Carlo ray tracing process converges relatively slowly (an error decreses with the inverse square root of the number of samples) but the sampling could be effectively performed in parallel, so it's a good idea to run Monte-Carlo integration on massively parallel hardware like GPUs.

Clay is able to run the part of its code on a pararllel hardware, and this part of the program is called *device part*. The remaining code is running on the main CPU as usual, and is called *host part*. 

### Host

Host part of the Clay is written in [Rust programming language](https://www.rust-lang.org/) and usually performs management tasks. It employs strict but flexible Rust trait system and type parametrization to provide a required level of modularity. You can assemble desired ray tracing pipeline from primitive building blocks, and if the desired functionality doesn't exist in Clay yet, you always can write it by yourself by implementing corresponding traits.

### Device

Device part of the Clay is written in [OpenCL](https://www.khronos.org/opencl/) and contains performance-critical parallel code.

Device code consists of so-called modules. The module contains some definitions with specific names and functions with specific signatures. Names and signatures of the module content are called the *interface*, and they are the same for all modules of the same type.

The type of device module and the interface are defined by the `Instance<Class>` trait implemented for the program entity. You can find more information about classes and its instances in the documentation of [clay-core](https://docs.rs/clay-core/) and [clay](https://docs.rs/clay/) crates.

Each device module corresponds to the specific host entity and resulting OpenCL kernel is assembled according to the hierarchy of host entities defined by user. Along with the host implementation you also able to write your own modules of OpenCL code to run on a GPU. 

## Pipeline

Rendering pipeline is a sequence of operations that is necessary to obtain a resulting image.

![Pipeline](/assets/pipeline.svg)

`Renderer` is the main entity, which is responsible for raytracing process. But it doesn't perform actual computation, it just stores host data and some metainformation.

For actual rendering it produces a `RenderWorker`, which is like a proxy of renderer in a specific `Context` (computing environment for a separate device with its own memory and command queue). There may be multiple workers in different contexts for a single renderer. Worker performs raytracing procedure: it runs OpenCL kernel that computes Monte-Carlo sampling for each pixel and accumulates the result into raw image.

The raw images of all workers after running of multiple rendering procedures are passed to a `Postproc`. It sums up these images, performs some user-defined filtering (e.g. color correction) if it's needed, and outputs the final RGB image that may be saved to a file or drawn in a window. 

## Renderer

![Renderer](/assets/renderer.svg)

The behaviour of the renderer is defined by `Scene` and `View`.

Clay uses backward propagation of rays from view to scene. For each pixel of the image the ray is casted from the view towards the specific direction defined by view orientation and coordinate of the pixel. The ray can hit object, in this case it will be absorbed by object and *one or zero* secondary rays will be produced.

There are some already implemented scenes and views in the library but you can implement your own.

### Scene

Existing scenes is designed to be a container of objects. Scene device module contains the function that takes a single ray, produced by view, and performs the search for an object hit by this ray. After object is found, an interaction between it and ray is performed, producing a secondary ray if it is needed. Secondary ray is handled in the same manner and this chain process continues until the ray is consumed by the light source or background, or number of bounces of the ray is exceeded the limit.

Scene also has a background which describes what happens to ray that hasn't hit any object on its way.

### View

The main function of view is to produce rays from each pixel of the viewport. View also has device function that takes pixes indices and produces ray computing its origin and direction. Through this mechanism view can simulate (e.g. stochastically) different effects like motion blur and depth of field.

## Objects

`Object` is an entity that is able to do these two things:
+ For the given ray it gives the closest point where this ray intersects it.
+ For the given ray and intersect point it returns color and produces secondary ray if it's needed.

An object may (but not must) be constructed from `Shape` and `Material`. The first describes the geometry of an object that is used to find an intersection point, and the second returns color and produces secondary rays according to the properties of itself (e.g. including BRDF implementation). The construction of such object is performed by `Covered` adapter.

An object could be mapped in space, using arbitrary mapping. These mappings should implement `Map` trait and contain device code to map rays and normals to/from the object local coordinate system. Two mapping could be chained together using `Chain` adapter, that creates new forward and backward transformations that applies these two mappings subsequently. There are a few already implemented mapping including shift, scale and arbitrary linear transformation using matrix. Mapping could be applied to an object using `ObjectMapper` adapter (also there is a `ShapeMapper` for mapping shapes separately).

Objects, shapes, materials, mapping, etc. have specific device-side interface and implementation in OpenCL, that is compiled and performs required computation on the device.

The type of the object to draw should be known before compiling the device kernel because we cannot run dynamic code in OpenCL. Particularly, the scene takes single type of the object as a parameter. But this doesn't mean that we can draw scene containing only one type of the object (e.g. only spheres). The mechanism calling `select` allows to create single composite object type from multiple different types. This mechanism stores in type identifier along with the object data, and type of the object is determined dynamically in the kernel, and code for appropriate object is run. This mechanism is available for shapes, materials and objects via `shape_select`, `material_select` and `object_select` macros respectively.

Also there are mechanism calling `combine` for materials. Let's imagine the situation where we need to have a some kind of glossy material that reflects 90% of the light diffusely and 10% - directly (like a mirror). Fortunately, there is a macro `material_combine` that allows to obtain a new material with such properties by combining diffuse material with the probability of 0.9 and reflective material with the probability of 0.1. This mechanism allows to combine arbitrary number of materials along with their corresponding probabilities.

![Object folding](/assets/object_folding.svg)

*Example of the object construction hierarchy. The color shows which class an entity is instance of.*
