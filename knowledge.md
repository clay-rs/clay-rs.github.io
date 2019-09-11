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

*Device part* is a piece OpenCL code that contains some definitions with specific names and functions with specific signatures. The term 'module' is used for this piece of code. Names and signatures of the module content are called the interface, and they are the same for all modules of the same type. Corresponding device functionality is fully defined in the module, and resulting OpenCL kernel is assembled from these modules.

The type of device module and the interface are defined by the `Instance<Class>` trait implemented for the entity. You can find more information about classes and its instances in the documentation of [clay-core](https://docs.rs/clay-core/) and [clay](https://docs.rs/clay/) crates.

### Pipeline

![Pipeline](/assets/pipeline.svg)

`Renderer` is the main entity, which is responsible for raytracing process. But it doesn't perform actual computation, it just stores host data and some metainformation.

For actual rendering it produces a `RenderWorker`, which is like a proxy of renderer in a specific `Context` (computing environment for a separate device with its own memory and command queue). There may be multiple workers in different contexts for a single renderer. Worker performs raytracing procedure: it runs OpenCL kernel that computes Monte-Carlo sampling for each pixel and accumulates the result into raw image.

The raw images of all workers after running of multiple rendering procedures are passed to a `Postproc`. It sums up these images, performs some user-defined filtering (e.g. color correction) if it's needed, and outputs the final RGB image that may be saved to a file or drawn in a window. 

### Renderer

![Renderer](/assets/renderer.svg)

The behaviour of the renderer is defined by `Scene` and `View`. There are some already implemented scenes and views in the library but you can implement your own.

#### Scene

Existing scenes is designed to be a container of objects. Scene device module contains the function that takes a single ray, produced by view, and performs the search for an object hit by this ray. After object is found, an interaction between it and ray is performed, producing a secondary ray if it is needed. Secondary ray is handled in the same manner and this chain process continues until the ray is consumed by the light source or background, or number of bounces of the ray is exceeded the limit.

Scene also has a background which describes what happens to ray that hasn't hit any object on its way.

#### View

The main function of view is to produce rays from each pixel of the viewport (Clay uses reversed ray propagation - from the camera to the light source). View also has device function that takes pixes indices and produces ray computing its origin and direction. Through this mechanism view can simulate (e.g. stochastically) different effects like MSAA and depth-of-field.

### Objects

`Object` is an entity that is able to do these two things:
+ For the given ray it gives the closest point where this ray intersects it.
+ For the given ray and intersect point it returns color and produces secondary ray if it's needed.

An object may (but not must) be constructed from `Shape` and `Material`. The first describes the geometry of an object that is used to find an intersection point, and the second returns color and prodeces secondary rays according to the properties of itself. The construction of such object is performed by `Covered` adapter.

An object could be mapped in space, using arbitrary mapping. These mappings should implement `Map` trait and contain device code to map rays and normals to/from the object local coordinate system. Two mapping could be chained together using `Chain` adapter, that creates new forward and backward transformations that applies these two mappings subsequently. There are a few already implemented mapping including shift, scale and arbitrary linear transformation using matrix. Mapping could be applied to an object using `ObjectMapper` adapter (also there is a `ShapeMapper` for mapping shapes separately).

Objects, shapes, materials, mapping, etc. have specific device-side interface and implementation in OpenCL, that is compiled and performs required computation on the device.

The type of the object to draw should be known before compiling the device kernel because we cannot run dynamic code in OpenCL. Particularly, the scene takes single type of the object as a parameter. But this doesn't mean that we can draw scene containing only one type of the object (e.g. only spheres). The mechanism calling `select` allows to create single composite object type from multiple different types. This mechanism stores in type identifier along with the object data, and type of the object is determined dynamically in the kernel, and code for appropriate object is run. This mechanism is available for shapes, materials and objects via `shape_select`, `material_select` and `object_select` macros respectively.

Also there are mechanism calling `combine` for materials. Let's imagine the situation where we need to have a some kind of glossy material that reflects 90% of the light diffusely and 10% - directly (like a mirror). Fortunately, there is a macro `material_combine` that allows to obtain a new material with such properties by combining diffuse material with the probability of 0.9 and reflective material with the probability of 0.1. This mechanism allows to combine arbitrary number of materials along with their corresponding probabilities.

![Object folding](/assets/object_folding.svg)

*Example of the object construction hierarchy. The color shows which class an entity is instance of.*

## Usage

Clay host code is written in [Rust](https://www.rust-lang.org/).

To start using Clay just add the following code to the `Cargo.toml` of your Rust project:

```toml
[dependencies]
clay = "0.1"
```

Clay requires OpenCL version 1.1 or above to be installed. Also [SDL2](https://libsdl.org/) library the should be installed if you want to use [clay-viewer](https://github.com/clay-rs/clay-viewer).

## Examples

At first the Clay source code should be downloaded:

```bash
git clone https://github.com/clay-rs/clay
cd clay
```

To run specific example:
```bash
cargo run --example <name-of-example>
```

Examples uses clay-viewer, so make sure that SDL2 is installed.

Available examples:

+ `00_ocl_info` - prints information about available OpenCL platforms and devices.
+ `01_diffuse_spheres` - draws static image of two spheres.
+ `02_spheres_and_motion` - the same but you can fly around using your keyboard and mouse. You can read [here](https://github.com/clay-rs/clay-viewer) about controls.
+ `03_shape_select` - draws different kinds of shapes using `select` macro.
+ `04_light_source` - usage of importance sampling technique to render illumination from small but bright light source.
+ `05_materials` - draws simple interior of a small room with different materials created using `combine` macro. *This example is relatively heavy, so you'll need to wait for a while to get an image of sufficient quality.* 
+ ...
+ `99_advanced` - experiments with some new techniques, changes frequently. 
