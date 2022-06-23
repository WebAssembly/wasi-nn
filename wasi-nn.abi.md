# Types

## <a href="#tensor_dimensions" name="tensor_dimensions"></a> `tensor-dimensions`: list<`u32`>

  The dimensions of a tensor.
  
  The array length matches the tensor rank and each element in the array
  describes the size of each dimension.

Size: 8, Alignment: 4

## <a href="#tensor_type" name="tensor_type"></a> `tensor-type`: variant

  The type of the elements in a tensor.

Size: 1, Alignment: 1

### Variant Cases

- <a href="tensor_type.fp16" name="tensor_type.fp16"></a> [`fp16`](#tensor_type.fp16)


- <a href="tensor_type.fp32" name="tensor_type.fp32"></a> [`fp32`](#tensor_type.fp32)


- <a href="tensor_type.up8" name="tensor_type.up8"></a> [`up8`](#tensor_type.up8)


- <a href="tensor_type.ip32" name="tensor_type.ip32"></a> [`ip32`](#tensor_type.ip32)


## <a href="#tensor_data" name="tensor_data"></a> `tensor-data`: list<`u8`>

  The tensor data.
  
  Initially coneived as a sparse representation, each empty cell would be filled with zeros and
  the array length must match the product of all of the dimensions and the number of bytes in the
  type (e.g., a 2x2 tensor with 4-byte f32 elements would have a data array of length 16).
  Naturally, this representation requires some knowledge of how to lay out data in memory--e.g.,
  using row-major ordering--and could perhaps be improved.

Size: 8, Alignment: 4

## <a href="#tensor" name="tensor"></a> `tensor`: record

  A tensor.

Size: 20, Alignment: 4

### Record Fields

- <a href="tensor.dimensions" name="tensor.dimensions"></a> [`dimensions`](#tensor.dimensions): [`tensor-dimensions`](#tensor_dimensions)

  Describe the size of the tensor (e.g., 2x2x2x2 -> [2, 2, 2, 2]). To represent a tensor
  containing a single value, use `[1]` for the tensor dimensions.

- <a href="tensor.tensor_type" name="tensor.tensor_type"></a> [`tensor-type`](#tensor.tensor_type): [`tensor-type`](#tensor_type)

  Describe the type of element in the tensor (e.g., f32).

- <a href="tensor.data" name="tensor.data"></a> [`data`](#tensor.data): [`tensor-data`](#tensor_data)

  Contains the tensor data.

## <a href="#graph_builder" name="graph_builder"></a> `graph-builder`: list<`u8`>

  The graph initialization data.
  
  This consists of an array of buffers because implementing backends may encode their graph IR in
  parts (e.g., OpenVINO stores its IR and weights separately).

Size: 8, Alignment: 4

## <a href="#graph_builder_array" name="graph_builder_array"></a> `graph-builder-array`: list<[`graph-builder`](#graph_builder)>


Size: 8, Alignment: 4

## <a href="#graph_encoding" name="graph_encoding"></a> `graph-encoding`: variant

  Describes the encoding of the graph. This allows the API to be implemented by various backends
  that encode (i.e., serialize) their graph IR with different formats.

Size: 1, Alignment: 1

### Variant Cases

- <a href="graph_encoding.openvino" name="graph_encoding.openvino"></a> [`openvino`](#graph_encoding.openvino)


- <a href="graph_encoding.onnx" name="graph_encoding.onnx"></a> [`onnx`](#graph_encoding.onnx)


- <a href="graph_encoding.tensorflow" name="graph_encoding.tensorflow"></a> [`tensorflow`](#graph_encoding.tensorflow)


## <a href="#execution_target" name="execution_target"></a> `execution-target`: variant

  Define where the graph should be executed.

Size: 1, Alignment: 1

### Variant Cases

- <a href="execution_target.cpu" name="execution_target.cpu"></a> [`cpu`](#execution_target.cpu)


- <a href="execution_target.gpu" name="execution_target.gpu"></a> [`gpu`](#execution_target.gpu)


- <a href="execution_target.tpu" name="execution_target.tpu"></a> [`tpu`](#execution_target.tpu)


## <a href="#error" name="error"></a> `error`: variant

  Error codes returned by functions in this API.

Size: 1, Alignment: 1

### Variant Cases

- <a href="error.success" name="error.success"></a> [`success`](#error.success)

  No error occurred.

- <a href="error.invalid_argument" name="error.invalid_argument"></a> [`invalid-argument`](#error.invalid_argument)

  Caller module passed an invalid argument.

- <a href="error.invalid_encoding" name="error.invalid_encoding"></a> [`invalid-encoding`](#error.invalid_encoding)

  Invalid encocing.

- <a href="error.missing_memory" name="error.missing_memory"></a> [`missing-memory`](#error.missing_memory)

  Caller module is missing a memory export.

- <a href="error.busy" name="error.busy"></a> [`busy`](#error.busy)

  Device or resource busy.

- <a href="error.runtime_error" name="error.runtime_error"></a> [`runtime-error`](#error.runtime_error)

  Runtime Error.

# Functions

----

#### <a href="#load" name="load"></a> `load` 

  Load an opaque sequence of bytes to use for inference.
##### Params

- <a href="#load.builder" name="load.builder"></a> `builder`: [`graph-builder-array`](#graph_builder_array)
- <a href="#load.encoding" name="load.encoding"></a> `encoding`: [`graph-encoding`](#graph_encoding)
- <a href="#load.target" name="load.target"></a> `target`: [`execution-target`](#execution_target)
##### Results

- <a href="#load." name="load."></a> ``: expected<handle<graph>, [`error`](#error)>

----

#### <a href="#init_execution_context" name="init_execution_context"></a> `init-execution-context` 

  Create an execution instance of a loaded graph.
##### Params

- <a href="#init_execution_context.graph" name="init_execution_context.graph"></a> `graph`: handle<graph>
##### Results

- <a href="#init_execution_context." name="init_execution_context."></a> ``: expected<handle<graph-execution-context>, [`error`](#error)>

----

#### <a href="#set_input" name="set_input"></a> `set-input` 

  Define the inputs to use for inference.
##### Params

- <a href="#set_input.ctx" name="set_input.ctx"></a> `ctx`: handle<graph-execution-context>
- <a href="#set_input.index" name="set_input.index"></a> `index`: `u32`
- <a href="#set_input.tensor" name="set_input.tensor"></a> `tensor`: [`tensor`](#tensor)
##### Results

- <a href="#set_input." name="set_input."></a> ``: expected<_, [`error`](#error)>

----

#### <a href="#compute" name="compute"></a> `compute` 

  Compute the inference on the given inputs.
##### Params

- <a href="#compute.ctx" name="compute.ctx"></a> `ctx`: handle<graph-execution-context>
##### Results

- <a href="#compute." name="compute."></a> ``: expected<_, [`error`](#error)>

----

#### <a href="#get_output" name="get_output"></a> `get-output` 

  Extract the outputs after inference.
##### Params

- <a href="#get_output.ctx" name="get_output.ctx"></a> `ctx`: handle<graph-execution-context>
- <a href="#get_output.index" name="get_output.index"></a> `index`: `u32`
##### Results

- <a href="#get_output." name="get_output."></a> ``: expected<[`tensor`](#tensor), [`error`](#error)>

