Clay ray tracing engine is:
+ **Fast** - because of the OpenCL, Clay is able to run its kernel code in heterogenous computing systems (e.g. GPUs), that makes it much faster than CPU-only analogs, and allows it to render images of sufficient quality even in real-time.
+ **Modular** - Clay is based on strict but flexible Rust trait system and type parametrization, that means you can assemble desired ray tracing pipeline from primitive building blocks.
+ **Extendable** - if desired functionality doesn't exist in Clay yet, you always can write it by yourself by implementing corresponding traits. Moreover, you can even write your own modules of OpenCL code to run on a GPU. (And make a pull request after that, if you want to.)
