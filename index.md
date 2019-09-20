---
layout: home
---

![](/assets/title_image.jpg)

Clay is modular and customizable Monte-Carlo ray tracing engine written in Rust and OpenCL.

## About

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
