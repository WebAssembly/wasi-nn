# `wasi-nn`

A proposed [WebAssembly System Interface](https://github.com/WebAssembly/WASI) API for machine
learning (ML).

### Current Phase

`wasi-nn` is currently in [Phase 2].

[Phase 2]: https://github.com/WebAssembly/WASI/blob/42fe2a3ca159011b23099c3d10b5b1d9aff2140e/docs/Proposals.md#phase-2---proposed-spec-text-available-cg--wg

### Champions

- [Andrew Brown](https://github.com/abrown)
- [Mingqiu Sun](https://github.com/mingqiusun)

### Phase 4 Advancement Criteria

`wasi-nn` must have at least two complete independent implementations.

## Table of Contents

- [Introduction](#introduction)
- [Goals](#goals)
- [Non-goals](#non-goals)
- [API walk-through](#api-walk-through)
- [Detailed design discussion](#detailed-design-discussion)
- [Considered alternatives](#considered-alternatives)
- [Stakeholder Interest & Feedback](#stakeholder-interest--feedback)
- [References & acknowledgements](#references--acknowledgements)

### Introduction

`wasi-nn` is a WASI API for performing ML inference. Its name derives from the fact that ML models
are also known as neural networks (`nn`). ML models are typically trained using a large data set,
resulting in one or more files that describe the model's weights. The model is then used to compute
an "inference," e.g., the probabilities of classifying an image as a set of tags. This API is
concerned initially with inference, not training.

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

`wasi-nn` is not designed to provide support for individual ML operations (a "model builder" API).
The ML field  is still evolving rapidly, with new operations and network topologies emerging
continuously. It would be a challenge to define an evolving set of operations to support in the API.
Instead, our approach is to start with a "model loader" API, inspired by WebNN’s model loader
proposal.

### API walk-through

The following example describes how a user would use `wasi-nn`:

```rust
// Load the model.
let encoding = wasi_nn::GRAPH_ENCODING_...;
let target = wasi_nn::EXECUTION_TARGET_CPU;
let graph = wasi_nn::load(&[bytes, more_bytes], encoding, target);

// Configure the execution context.
let context = wasi_nn::init_execution_context(graph);
let tensor = wasi_nn::Tensor { ... };
wasi_nn::set_input(context, 0, tensor);

// Compute the inference.
wasi_nn::compute(context);
wasi_nn::get_output(context, 0, &mut output_buffer, output_buffer.len());
```

Note that the details above will depend on the model and backend used; the pseudo-Rust simply
illustrates the general idea, minus any error-checking. Consult the
[AssemblyScript](https://github.com/bytecodealliance/wasi-nn/tree/main/assemblyscript/examples) and
[Rust](https://github.com/bytecodealliance/wasi-nn/tree/main/rust/examples) bindings for more
detailed examples.

### Detailed design discussion

For the details of the API, see [wasi-nn.wit](wit/wasi-nn.wit).

<!--
This section should mostly refer to the .wit.md file that specifies the API. This section is for
any discussion of the choices made in the API which don't make sense to document in the spec file
itself.
-->

#### Should `wasi-nn` support training models?

Ideally, yes. In the near term, however, exposing (and implementing) the inference-focused API is
sufficiently complex to postpone a training-capable API until later. Also, models are typically
trained offline, prior to deployment, and it is unclear why training models using WASI would be an
advantage over training them natively. (Conversely, the inference API does make sense: performing ML
inference in a Wasm deployment is a known use case). See associated discussion
[here](https://github.com/WebAssembly/wasi-nn/issues/6) and feel free to open pull requests or
issues related to this that fit within the goals above.

#### Should `wasi-nn` support inspecting models?

Ideally, yes. The ability to inspect models would allow users to determine, at runtime, the tensor
shapes of the inputs and outputs of a model. As with ML training (above), this can be added in the
future.

<!--
More "tricky" design choices fit here.
-->

### Considered alternatives

There are other ways to perform ML inference from a WebAssembly program:

1. a user could specify a __custom host API__ for ML tasks; this is similar to the approach taken
   [here](https://github.com/second-state/wasmedge_tensorflow_interface). The advantages and
   disadvantages are in line with other "spec vs. custom" trade-offs: the user can precisely tailor
   the API to their use case, etc., but will not be able to switch easily between implementations.
2. a user could __compile a framework and/or model to WebAssembly__; this is similar to
   [here](https://github.com/sonos/tract) and
   [here](https://blog.tensorflow.org/2020/03/introducing-webassembly-backend-for-tensorflow-js.html).
   The primary disadvantage to this approach is performance: WebAssembly, even with the recent
   addition of 128-bit SIMD, does not have optimized primitives for performing ML inference or
   accessing ML-optimized hardware. The performance loss can be of several orders of magnitude.


### Stakeholder Interest & Feedback

TODO before entering Phase 3.

<!--
This should include a list of implementers who have expressed interest in implementing the proposal
-->

### References & acknowledgements

Many thanks for valuable feedback and advice from:

- [Brian Jones](https://github.com/brianjjones)
- [Radu Matei](https://github.com/radu-matei)
- [Steve Schoettler](https://github.com/stevelr)
