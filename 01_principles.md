---
layout: page
title: Principles
permalink: /principles/
order: 1
---

## Basics

Clay is based on strict but flexible Rust trait system and type parametrization, that means you can assemble desired ray tracing pipeline from primitive building blocks. If desired functionality doesn't exist in Clay yet, you always can write it by yourself by implementing corresponding traits. Moreover, you can even write your own modules of OpenCL code to run on a GPU. (And make a pull request after that, if you want to.)

Clay is able to run its OpenCL kernel code on massively parallel hardware like GPUs, that makes it much faster than CPU-only analogs, and allows it to render images of sufficient quality even in real-time.

## Rendering

Clay uses backward propagation of rays from camera to scene. For each pixel of the image the ray is casted from the camera towards the specific direction defined by camera orientation and coordinate of the pixel. The ray can hit object, in this case it will be absorbed by object and *one or zero* secondary rays will be produced.
