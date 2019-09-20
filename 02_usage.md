---
layout: page
title: Usage
permalink: /usage/
order: 2
---


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

Examples are located in `examples` subdirectory.

To run specific example:

```bash
cargo run --example <name-of-example>
```

Examples uses clay-viewer, so make sure that SDL2 is installed.

Available examples:

+ `00_ocl_info` - prints information about available OpenCL platforms and devices.

+ `01_1_spheres` and `02_2_motion`. The first draws static image of two spheres. The second is the same but you can fly around using your keyboard and mouse. You may read [here](https://github.com/clay-rs/clay-viewer) about controls.

  [![](/gallery/examples/01_spheres.min.png)](/gallery/examples/01_spheres.png)

+ `02_shapes` - draws different kinds of shapes using `shape_select` macro.

  [![](/gallery/examples/02_shapes.min.png)](/gallery/examples/02_shapes.png)

+ `03_materials` - different materials using `material_combine` and `material_select` macros.

  [![](/gallery/examples/03_materials.min.png)](/gallery/examples/03_materials.png)

+ `04_1_light_source` and `04_2_multiple_sources` - usage of importance sampling technique to render illumination from small but bright light sources.

  [![](/gallery/examples/04_1_light_source.min.png)](/gallery/examples/04_1_light_source.png)
  [![](/gallery/examples/04_2_multiple_sources.min.png)](/gallery/examples/04_2_multiple_sources.png)

+ `05_indirect_lighting` - draws simple interior of a small room illuminated by secondary rays.

  [![](/gallery/examples/05_indirect_lighting.min.png)](/gallery/examples/05_indirect_lighting.png)
