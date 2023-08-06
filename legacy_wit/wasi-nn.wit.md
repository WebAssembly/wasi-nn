# `wasi-nn` API

`wasi-nn` is a WASI API for performing machine learning (ML) inference. The API is not (yet) capable
of performing ML training. WebAssembly programs that want to use a host's ML capabilities can access
these capabilities through `wasi-nn`'s core abstractions: _backends_, _graphs_, and _tensors_. A
user selects a _backend_ for inference and `load`s a model, instantiated as a _graph_, to use in the
_backend_. Then, the user passes _tensor_ inputs to the _graph_, computes the inference, and
retrieves the _tensor_ outputs.

This module draws inspiration from the inference side of
[WebNN](https://webmachinelearning.github.io/webnn/#api). See the
[README](https://github.com/WebAssembly/wasi-nn/blob/main/README.md) for more context about the
design and goals of this API.

### Tensor

All inputs and outputs to an ML inference are represented as `tensor`s:

```wit
// The dimensions of a tensor.
//
// The array length matches the tensor rank and each element in the array
// describes the size of each dimension.
type tensor-dimensions = list<u32>

// The type of the elements in a tensor.
enum tensor-type {
    fp16,
    fp32,
    up8,
    ip32
}

// The tensor data.
//
// Initially conceived as a sparse representation, each empty cell would be filled with zeros and
// the array length must match the product of all of the dimensions and the number of bytes in the
// type (e.g., a 2x2 tensor with 4-byte f32 elements would have a data array of length 16).
// Naturally, this representation requires some knowledge of how to lay out data in memory--e.g.,
// using row-major ordering--and could perhaps be improved.
type tensor-data = list<u8>

// A tensor.
record tensor {
    // Describe the size of the tensor (e.g., 2x2x2x2 -> [2, 2, 2, 2]). To represent a tensor
    // containing a single value, use `[1]` for the tensor dimensions.
    dimensions: tensor-dimensions,

    // Describe the type of element in the tensor (e.g., f32).
    tensor-type: tensor-type,

    // Contains the tensor data.
    data: tensor-data,
}
```

### Graph

A `graph` is a `load`-ed instance of a specific ML model (e.g., MobileNet) for a specific ML
framework (e.g., TensorFlow):

```wit
// The graph initialization data.
//
// This consists of an array of buffers because implementing backends may encode their graph IR in
// parts (e.g., OpenVINO stores its IR and weights separately).
type graph-builder = list<u8>
type graph-builder-array = list<graph-builder>

// An execution graph for performing inference (i.e., a model).
resource graph

// Describes the encoding of the graph. This allows the API to be implemented by various backends
// that encode (i.e., serialize) their graph IR with different formats.
enum graph-encoding {
    openvino,
    onnx,
    tensorflow,
    pytorch,
    tensorflowlite
}

// Define where the graph should be executed.
enum execution-target {
    cpu,
    gpu,
    tpu
}

// Load an opaque sequence of bytes to use for inference.
load: func(builder: graph-builder-array, encoding: graph-encoding, target: execution-target) -> expected<graph, error>
```

### Inference

An inference "session" is encapsulated by a `graph-execution-context`. This structure binds a
`graph` to input tensors before `compute`-ing an inference:

```wit
// Bind a `graph` to the input and output tensors for an inference.
resource graph-execution-context

// Create an execution instance of a loaded graph.
init-execution-context: func(graph: graph) -> expected<graph-execution-context, error>
```

Inputs and outputs to the inference request &mdash; `compute` &mdash; are identified by a numeric
index:

```wit
// Define the inputs to use for inference.
set-input: func(ctx: graph-execution-context, index: u32, tensor: tensor) -> expected<unit, error>

// Compute the inference on the given inputs.
compute: func(ctx: graph-execution-context) -> expected<unit, error>

// Extract the outputs after inference.
get-output: func(ctx: graph-execution-context, index: u32) -> expected<tensor-data, error>
```

### Errors

```wit
// Error codes returned by functions in this API.
enum error {
    // No error occurred.
    success,
    // Caller module passed an invalid argument.
    invalid-argument,
    // Invalid encoding.
    invalid-encoding,
    // Caller module is missing a memory export.
    missing-memory,
    // Device or resource busy.
    busy,
    // Runtime Error.
    runtime-error,
}
```
