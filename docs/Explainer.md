# Design Motivation and Considerations

## Why Wasm for ML?
Trained machine learning models are typically deployed on a variety of devices with different
architectures and operating systems. Web Assembly provides an ideal portable form of deployment for
those models. 

## Why WASI?
Although a whole machine learning framework could be potentially compiled into Wasm, special
hardware acceleration is often needed in order to be performant. For example, SIMD instructions such
as AVX512 on a CPU can speed up performance by several hundred times. Other hardware acclerator
examples are GPU, TPU, FPGA. All of those acceleration mechanisms are not available within Wasm. In
addition, the field of machine learning is still evolving rapidly, with new operations and network
topologies emerging continuously. It would be a challenge to define an evolving set of operations to
support in the API. 

## Design Goals
Our approach is to start with a model loader API, inspired by WebNN’s model loader proposal. It
treats a machine learning graph as an opaque handle that we can use to send inputs, get outputs, and
perform forward propagation. Since inferencing is the main ML use case, and our initial focus, this
API surface is sufficent. 

Even though we are focusing on inferencing now, future-proofing the specification is one of our
design goals. For example, future APIs could be added to support back propagation without changing
our current model loading and forward propagation APIs.

Another design goal is to make the API framework and format agnostic. We expect device vendors to
provide the ML framework and support for their particular graph format. The `load` method would
return an error message when an unsupported model encoding scheme is passed in. This approach is
similar to how a browser deals with image or video encoding.

## More Information
More information is available in the following blog posts:
 - [Machine Learning in WebAssembly: Using wasi-nn in
   Wasmtime](https://bytecodealliance.org/articles/using-wasi-nn-in-wasmtime))
 - [Implementing a WASI Proposal in Wasmtime:
   wasi-nn](https://bytecodealliance.org/articles/implementing-wasi-nn-in-wasmtime)
