---
layout: page
---

![](/assets/title_image.jpg)

Clay is a flexible Monte-Carlo ray tracing engine written in Rust and OpenCL.

## About

Clay ray tracing engine is:

+ **Fast** - because of the OpenCL, Clay is able to run its kernel code in massively
  parallel computing systems (e.g. GPUs), that makes it much faster than CPU-only analogs,
  and allows it to render images of sufficient quality even in real-time.

+ **Modular** - Clay is based on strict but flexible Rust trait system and type parametrization,
  that means you can assemble desired ray tracing pipeline from primitive building blocks.

+ **Extendable** - if desired functionality doesn't exist in Clay yet, you always can write
  it by yourself by implementing corresponding traits. Moreover, you can even write your own
  modules of OpenCL code to run on a GPU. (And make a pull request after that, if you want to.)

This project is primarily aimed to be a convenient framework for experimenting with ray tracing, testing new techniques, making proof of concepts and other research activity in this field.

The key principles of the project is modularity and extendability. The performance is also one of the primary goals, as long as it doesn't significantly reduce flexibility.

## Source

[![Crates.io][crates_badge]][crates]
[![Docs.rs][docs_badge]][docs]
[![Travis CI][travis_badge]][travis]
[![License][license_badge]][license]

[crates_badge]: https://img.shields.io/crates/v/clay.svg
[docs_badge]: https://docs.rs/clay/badge.svg
[travis_badge]: https://api.travis-ci.org/clay-rs/clay.svg?branch=master
[license_badge]: https://img.shields.io/crates/l/clay.svg

[crates]: https://crates.io/crates/clay
[docs]: https://docs.rs/clay
[travis]: https://travis-ci.org/clay-rs/clay
[license]: /license

All components of the project including [the main crate](https://github.com/clay-rs/clay) are located on the Github in [Clay-rs organization](https://github.com/clay-rs).

## Knowledge base

### [Architecture](/architecture)

This section describes the basic principles the project is built onto and contains informantion about rendering mechanics, program entities and their hierarchy.

### [Usage](/usage)

This section briefly shows how to use Clay and contains a list of examples.

## Contribution

Contributions are extremely welcome. The project is designed to be very powerful, but for now it contains only basic functionality, so there is a lot of things to implement.

## Licence

The project is licenced under dual *MIT/Apache-2.0* license, look [here](/license) for more information.
