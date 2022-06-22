# `wasi-nn`

A proposed [WebAssembly System Interface](https://github.com/WebAssembly/WASI) API for machine
learning (ML), also known as neural networks.

### Current Phase

`wasi-nn` is currently in [Phase 2].

[Phase 2]: https://github.com/WebAssembly/WASI/blob/42fe2a3ca159011b23099c3d10b5b1d9aff2140e/docs/Proposals.md#phase-2---proposed-spec-text-available-cg--wg

### Champions

- Andrew Brown
- Mingqiu Sun

### Phase 4 Advancement Criteria

`wasi-nn` must have at least two complete independent implementations.

## Table of Contents

- [Introduction](#introduction)
- [Goals](#goals)
- [Non-goals](#non-goals)
- [API walk-through](#api-walk-through)
  - [Use case 1](#use-case-1)
  - [Use case 2](#use-case-2)
- [Detailed design discussion](#detailed-design-discussion)
  - [[Tricky design choice 1]](#tricky-design-choice-1)
  - [[Tricky design choice 2]](#tricky-design-choice-2)
- [Considered alternatives](#considered-alternatives)
  - [[Alternative 1]](#alternative-1)
  - [[Alternative 2]](#alternative-2)
- [Stakeholder Interest & Feedback](#stakeholder-interest--feedback)
- [References & acknowledgements](#references--acknowledgements)

### Introduction

`wasi-nn` is a WASI API for performing ML inference. ML models are typically trained
using a large data set, resulting in one or more files that describe the model's weights. The model
is then used to compute an "inference," e.g., the probabilities of classifying an image as a set of
tags. This API is concerned initially with inference, not training.

Why expose ML inference as a WASI API? Though the functionality of inference can be encoded into
WebAssembly, there are two primary motivations for `wasi-nn`:
1. __ease of use__: an entire ecosystem already exists to train and use models (e.g., Tensorflow,
   ONNX, OpenVINO, etc.); `wasi-nn` is designed to make it easy to use existing model formats as-is
2. __performance__: the nature of ML inference makes it amenable to hardware acceleration of various
   kinds; without this hardware acceleration, inference can suffer slowdowns of several hundred
   times. Hardware acceleration for ML is very diverse &mdash; SIMD (e.g., AVX512), GPUs, TPUs,
   FPGAs &mdash; and it is unlikely (impossible?) that all of these would be supported natively in
   WebAssembly

WebAssembly programs that want to use a host's ML capabilities can access these capabilities through
`wasi-nn`'s core abstractions: _backends_, _graphs_, and _tensors_. A user selects a _backend_ for
inference and loads a model, instantiated as a _graph_, to use in the _backend_. Then, the user
passes _tensor_ inputs to the _graph_, computes the inference, and retrieves the _tensor_ outputs.

`wasi-nn` _backends_ correspond to existing ML frameworks, e.g., Tensorflow, ONNX, OpenVINO, etc.
`wasi-nn` places no requirements on hosts to support specific _backends_; the API is purposefully
designed to allow the largest number of ML frameworks to implement it. `wasi-nn` _graphs_ can be
passed as opaque byte sequences to support any number of model formats. This makes the API
framework- and format-agnostic, since we expect device vendors to provide the ML _backend_ and
support for their particular _graph_ format.

Users can find language bindings for `wasi-nn` at the [wasi-nn bindings] repository; request
additional language support there. More information about `wasi-nn` can be found at:

[wasi-nn bindings]: https://github.com/bytecodealliance/wasi-nn

 - Blog post: [Machine Learning in WebAssembly: Using wasi-nn in
   Wasmtime](https://bytecodealliance.org/articles/using-wasi-nn-in-wasmtime)
 - Blog post: [Implementing a WASI Proposal in Wasmtime:
   wasi-nn](https://bytecodealliance.org/articles/implementing-wasi-nn-in-wasmtime)
 - Blog post: [Neural network inferencing for PyTorch and TensorFlow with ONNX, WebAssembly System
   Interface, and wasi-nn](https://deislabs.io/posts/wasi-nn-onnx/)
 - Recorded talk: [Machine Learning with Wasm
   (wasi-nn)](https://www.youtube.com/watch?v=lz2I_4vvCuc)
 - Recorded talk: [Lightning Talk: High Performance Neural Network Inferencing Using
   wasi-nn](https://www.youtube.com/watch?v=jnM0tsRVM_8)

### Goals

The primary goal of `wasi-nn` is to allow users to perform ML inference from WebAssembly using
existing models (i.e., ease of use) and with maximum performance. Though the primary focus is
inference, we plan to leave open the possibility to perform ML training in the future (request
training in an [issue](https://github.com/WebAssembly/wasi-nn/issues)!).

Another design goal is to make the API framework- and model-agnostic; this allows for implementing
the API with multiple ML frameworks and model formats. The `load` method will return an error
message when an unsupported model encoding scheme is passed in. This approach is similar to how a
browser deals with image or video encoding.

### Non-goals

wasi-nn is not designed to provide support for individual ML operations (a "model builder" API). The
ML field  is still evolving rapidly, with new operations and network topologies emerging
continuously. It would be a challenge to define an evolving set of operations to support in the API.
Instead, our approach is to start with a "model loader" API, inspired by WebNN’s model loader
proposal.

### API walk-through

[Walk through of how someone would use this API.]

#### [Use case 1]

[Provide example code snippets and diagrams explaining how the API would be used to solve the given problem]

#### [Use case 2]

[etc.]

### Detailed design discussion

[This section should mostly refer to the .wit.md file that specifies the API. This section is for any discussion of the choices made in the API which don't make sense to document in the spec file itself.]

#### Should WASI filesystem be case-sensitive, case-insensitive, or platform-dependent?

Even just among popular platforms, there are case-sensitive and
case-insensitive filesystems in wide use.

It would be nice to have an API which presented consistent behavior across
platforms, so that applications don't have to worry about subtle differences,
and subtle bugs due to those differences.

However, implementing case sensitivity on a case-insensitive filesystem, or
case-insensitivity on a case-sensitive filesystem, are both tricky to do.

One issue is that case insensitivity depends on a Unicode version, so the
details can differ between different case-insensitive platforms. Another
issue is tha WASI filesystem in general can't assume it has exclusive access
to the filesystem, so approaches that involve checking for files with names
that differ only by case can race with other processes creating new files.

#### [Tricky design choice 2]

[etc.]

### Considered alternatives

[This section is not required if you already covered considered alternatives in the design discussion above.]

#### [Alternative 1]

[Describe an alternative which was considered, and why you decided against it.]

#### [Alternative 2]

[etc.]

### Stakeholder Interest & Feedback

TODO before entering Phase 3.

[This should include a list of implementers who have expressed interest in implementing the proposal]

### References & acknowledgements

Many thanks for valuable feedback and advice from:

- [Person 1]
- [Person 2]
- [etc.]
