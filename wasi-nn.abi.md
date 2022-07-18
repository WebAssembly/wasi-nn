# Types

## <a href="#tensor_dimensions" name="tensor_dimensions"></a> `tensor-dimensions`: list<`u32`>


Size: 8, Alignment: 4

## <a href="#tensor_type" name="tensor_type"></a> `tensor-type`: enum


Size: 1, Alignment: 1

### Enum Cases

- <a href="tensor_type.fp16" name="tensor_type.fp16"></a> [`fp16`](#tensor_type.fp16)


- <a href="tensor_type.fp32" name="tensor_type.fp32"></a> [`fp32`](#tensor_type.fp32)


- <a href="tensor_type.up8" name="tensor_type.up8"></a> [`up8`](#tensor_type.up8)


- <a href="tensor_type.ip32" name="tensor_type.ip32"></a> [`ip32`](#tensor_type.ip32)


## <a href="#tensor_data" name="tensor_data"></a> `tensor-data`: list<`u8`>


Size: 8, Alignment: 4

## <a href="#tensor" name="tensor"></a> `tensor`: record


Size: 20, Alignment: 4

### Record Fields

- <a href="tensor.dimensions" name="tensor.dimensions"></a> [`dimensions`](#tensor.dimensions): [`tensor-dimensions`](#tensor_dimensions)


- <a href="tensor.tensor_type" name="tensor.tensor_type"></a> [`tensor-type`](#tensor.tensor_type): [`tensor-type`](#tensor_type)


- <a href="tensor.data" name="tensor.data"></a> [`data`](#tensor.data): [`tensor-data`](#tensor_data)


## <a href="#graph_builder" name="graph_builder"></a> `graph-builder`: list<`u8`>


Size: 8, Alignment: 4

## <a href="#graph_builder_array" name="graph_builder_array"></a> `graph-builder-array`: list<[`graph-builder`](#graph_builder)>


Size: 8, Alignment: 4

## <a href="#graph_encoding" name="graph_encoding"></a> `graph-encoding`: enum


Size: 1, Alignment: 1

### Enum Cases

- <a href="graph_encoding.openvino" name="graph_encoding.openvino"></a> [`openvino`](#graph_encoding.openvino)


- <a href="graph_encoding.onnx" name="graph_encoding.onnx"></a> [`onnx`](#graph_encoding.onnx)


- <a href="graph_encoding.tensorflow" name="graph_encoding.tensorflow"></a> [`tensorflow`](#graph_encoding.tensorflow)


- <a href="graph_encoding.pytorch" name="graph_encoding.pytorch"></a> [`pytorch`](#graph_encoding.pytorch)


## <a href="#execution_target" name="execution_target"></a> `execution-target`: enum


Size: 1, Alignment: 1

### Enum Cases

- <a href="execution_target.cpu" name="execution_target.cpu"></a> [`cpu`](#execution_target.cpu)


- <a href="execution_target.gpu" name="execution_target.gpu"></a> [`gpu`](#execution_target.gpu)


- <a href="execution_target.tpu" name="execution_target.tpu"></a> [`tpu`](#execution_target.tpu)


## <a href="#error" name="error"></a> `error`: enum


Size: 1, Alignment: 1

### Enum Cases

- <a href="error.success" name="error.success"></a> [`success`](#error.success)


- <a href="error.invalid_argument" name="error.invalid_argument"></a> [`invalid-argument`](#error.invalid_argument)


- <a href="error.invalid_encoding" name="error.invalid_encoding"></a> [`invalid-encoding`](#error.invalid_encoding)


- <a href="error.missing_memory" name="error.missing_memory"></a> [`missing-memory`](#error.missing_memory)


- <a href="error.busy" name="error.busy"></a> [`busy`](#error.busy)


- <a href="error.runtime_error" name="error.runtime_error"></a> [`runtime-error`](#error.runtime_error)


# Functions

----

#### <a href="#load" name="load"></a> `load` 

##### Params

- <a href="#load.builder" name="load.builder"></a> `builder`: [`graph-builder-array`](#graph_builder_array)
- <a href="#load.encoding" name="load.encoding"></a> `encoding`: [`graph-encoding`](#graph_encoding)
- <a href="#load.target" name="load.target"></a> `target`: [`execution-target`](#execution_target)
##### Result

- expected<handle<graph>, [`error`](#error)>

----

#### <a href="#init_execution_context" name="init_execution_context"></a> `init-execution-context` 

##### Params

- <a href="#init_execution_context.graph" name="init_execution_context.graph"></a> `graph`: handle<graph>
##### Result

- expected<handle<graph-execution-context>, [`error`](#error)>

----

#### <a href="#set_input" name="set_input"></a> `set-input` 

##### Params

- <a href="#set_input.ctx" name="set_input.ctx"></a> `ctx`: handle<graph-execution-context>
- <a href="#set_input.index" name="set_input.index"></a> `index`: `u32`
- <a href="#set_input.tensor" name="set_input.tensor"></a> `tensor`: [`tensor`](#tensor)
##### Result

- expected<`unit`, [`error`](#error)>

----

#### <a href="#compute" name="compute"></a> `compute` 

##### Params

- <a href="#compute.ctx" name="compute.ctx"></a> `ctx`: handle<graph-execution-context>
##### Result

- expected<`unit`, [`error`](#error)>

----

#### <a href="#get_output" name="get_output"></a> `get-output` 

##### Params

- <a href="#get_output.ctx" name="get_output.ctx"></a> `ctx`: handle<graph-execution-context>
- <a href="#get_output.index" name="get_output.index"></a> `index`: `u32`
##### Result

- expected<[`tensor-data`](#tensor_data), [`error`](#error)>

