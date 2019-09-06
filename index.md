---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

Clay is flexible Monte-Carlo ray tracing engine written in Rust and OpenCL.

## Features

Clay ray tracing engine is:
+ **Fast** - because of the OpenCL, Clay is able to run its kernel code in heterogenous
  computing systems (e.g. GPUs), that makes it much faster than CPU-only analogs,
  and allows it to render images of sufficient quality even in real-time.
+ **Modular** - Clay is based on very flexible Rust trait system and type parametrization,
  that means you can assemble desired ray tracing pipeline from primitive building blocks.
+ **Extendable** - if desired functionality doesn't exist in Clay yet, you always can write
  it by yourself by implementing corresponding traits. Moreover, you can even write your own
  modules of OpenCL code to run on a GPU. (And make a pull request after that, if you want to.)

## About

This project is primarily aimed to be a convenient framework to experimenting with ray tracing,
testing new techniques, making proof of concepts and other research activity in this field.

The key principles of the project is modularity and extendability.
The performance is also one of the primary goals, as long as it doesn't significantly reduce flexibility.

## Knowledge
1. [Introduction](/knowledge/introduction)
2. [Architecture](/knowledge/architecture)
3. [Usage](/knowledge/usage)
4. [Examples](/knowledge/examples)
