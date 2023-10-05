<h1><a name="ml">World ml</a></h1>
<p><code>wasi-nn</code> is a WASI API for performing machine learning (ML) inference. The API is not (yet)
capable of performing ML training. WebAssembly programs that want to use a host's ML
capabilities can access these capabilities through <code>wasi-nn</code>'s core abstractions: <em>graphs</em> and
<em>tensors</em>. A user <a href="#load"><code>load</code></a>s an ML model -- instantiated as a <em>graph</em> -- to use in an ML <em>backend</em>.
Then, the user passes <em>tensor</em> inputs to the <em>graph</em>, computes the inference, and retrieves the
<em>tensor</em> outputs.</p>
<p>This example world shows how to use these primitives together.</p>
<ul>
<li>Imports:
<ul>
<li>interface <a href="#wasi:nn_tensor"><code>wasi:nn/tensor</code></a></li>
<li>interface <a href="#wasi:nn_errors"><code>wasi:nn/errors</code></a></li>
<li>interface <a href="#wasi:nn_graph"><code>wasi:nn/graph</code></a></li>
<li>interface <a href="#wasi:nn_inference"><code>wasi:nn/inference</code></a></li>
</ul>
</li>
</ul>
<h2><a name="wasi:nn_tensor">Import interface wasi:nn/tensor</a></h2>
<p>All inputs and outputs to an ML inference are represented as <a href="#tensor"><code>tensor</code></a>s.</p>
<hr />
<h3>Types</h3>
<h4><a name="tensor_dimensions"><code>type tensor-dimensions</code></a></h4>
<p><a href="#tensor_dimensions"><a href="#tensor_dimensions"><code>tensor-dimensions</code></a></a></p>
<p>The dimensions of a tensor.
<p>The array length matches the tensor rank and each element in the array describes the size of
each dimension</p>
<h4><a name="tensor_type"><code>enum tensor-type</code></a></h4>
<p>The type of the elements in a tensor.</p>
<h5>Enum Cases</h5>
<ul>
<li><a name="tensor_type.fp16"><code>FP16</code></a></li>
<li><a name="tensor_type.fp32"><code>FP32</code></a></li>
<li><a name="tensor_type.fp64"><code>FP64</code></a></li>
<li><a name="tensor_type.bf16"><code>BF16</code></a></li>
<li><a name="tensor_type.u8"><code>U8</code></a></li>
<li><a name="tensor_type.i32"><code>I32</code></a></li>
<li><a name="tensor_type.i64"><code>I64</code></a></li>
</ul>
<h4><a name="tensor_data"><code>type tensor-data</code></a></h4>
<p><a href="#tensor_data"><a href="#tensor_data"><code>tensor-data</code></a></a></p>
<p>The tensor data.
<p>Initially conceived as a sparse representation, each empty cell would be filled with zeros
and the array length must match the product of all of the dimensions and the number of bytes
in the type (e.g., a 2x2 tensor with 4-byte f32 elements would have a data array of length
16). Naturally, this representation requires some knowledge of how to lay out data in
memory--e.g., using row-major ordering--and could perhaps be improved.</p>
<h4><a name="tensor"><code>record tensor</code></a></h4>
<h5>Record Fields</h5>
<ul>
<li><a name="tensor.dimensions"><code>dimensions</code></a>: <a href="#tensor_dimensions"><a href="#tensor_dimensions"><code>tensor-dimensions</code></a></a></li>
<li><a name="tensor.tensor_type"><a href="#tensor_type"><code>tensor-type</code></a></a>: <a href="#tensor_type"><a href="#tensor_type"><code>tensor-type</code></a></a></li>
<li><a name="tensor.data"><code>data</code></a>: <a href="#tensor_data"><a href="#tensor_data"><code>tensor-data</code></a></a></li>
</ul>
<h2><a name="wasi:nn_errors">Import interface wasi:nn/errors</a></h2>
<p>TODO: create function-specific errors (https://github.com/WebAssembly/wasi-nn/issues/42)</p>
<hr />
<h3>Types</h3>
<h4><a name="error"><code>enum error</code></a></h4>
<h5>Enum Cases</h5>
<ul>
<li><a name="error.invalid_argument"><code>invalid-argument</code></a></li>
<li><a name="error.invalid_encoding"><code>invalid-encoding</code></a></li>
<li><a name="error.busy"><code>busy</code></a></li>
<li><a name="error.runtime_error"><code>runtime-error</code></a></li>
<li><a name="error.unsupported_operation"><code>unsupported-operation</code></a></li>
<li><a name="error.too_large"><code>too-large</code></a></li>
<li><a name="error.not_found"><code>not-found</code></a></li>
</ul>
<h2><a name="wasi:nn_graph">Import interface wasi:nn/graph</a></h2>
<p>A <a href="#graph"><code>graph</code></a> is a loaded instance of a specific ML model (e.g., MobileNet) for a specific ML
framework (e.g., TensorFlow):</p>
<hr />
<h3>Types</h3>
<h4><a name="error"><code>type error</code></a></h4>
<p><a href="#error"><a href="#error"><code>error</code></a></a></p>
<p>
#### <a name="tensor">`type tensor`</a>
[`tensor`](#tensor)
<p>
#### <a name="graph">`type graph`</a>
`u32`
<p>An execution graph for performing inference (i.e., a model).
<p>TODO: replace with <code>resource</code> (https://github.com/WebAssembly/wasi-nn/issues/47).</p>
<h4><a name="graph_encoding"><code>enum graph-encoding</code></a></h4>
<p>Describes the encoding of the graph. This allows the API to be implemented by various
backends that encode (i.e., serialize) their graph IR with different formats.</p>
<h5>Enum Cases</h5>
<ul>
<li><a name="graph_encoding.openvino"><code>openvino</code></a></li>
<li><a name="graph_encoding.onnx"><code>onnx</code></a></li>
<li><a name="graph_encoding.tensorflow"><code>tensorflow</code></a></li>
<li><a name="graph_encoding.pytorch"><code>pytorch</code></a></li>
<li><a name="graph_encoding.tensorflowlite"><code>tensorflowlite</code></a></li>
<li><a name="graph_encoding.autodetect"><code>autodetect</code></a></li>
</ul>
<h4><a name="execution_target"><code>enum execution-target</code></a></h4>
<p>Define where the graph should be executed.</p>
<h5>Enum Cases</h5>
<ul>
<li><a name="execution_target.cpu"><code>cpu</code></a></li>
<li><a name="execution_target.gpu"><code>gpu</code></a></li>
<li><a name="execution_target.tpu"><code>tpu</code></a></li>
</ul>
<h4><a name="graph_builder"><code>type graph-builder</code></a></h4>
<p><a href="#graph_builder"><a href="#graph_builder"><code>graph-builder</code></a></a></p>
<p>The graph initialization data.
<p>This gets bundled up into an array of buffers because implementing backends may encode their
graph IR in parts (e.g., OpenVINO stores its IR and weights separately).</p>
<hr />
<h3>Functions</h3>
<h4><a name="load"><code>load: func</code></a></h4>
<p>Load a <a href="#graph"><code>graph</code></a> from an opaque sequence of bytes to use for inference.</p>
<h5>Params</h5>
<ul>
<li><a name="load.builder"><code>builder</code></a>: list&lt;<a href="#graph_builder"><a href="#graph_builder"><code>graph-builder</code></a></a>&gt;</li>
<li><a name="load.encoding"><code>encoding</code></a>: <a href="#graph_encoding"><a href="#graph_encoding"><code>graph-encoding</code></a></a></li>
<li><a name="load.target"><code>target</code></a>: <a href="#execution_target"><a href="#execution_target"><code>execution-target</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="load.0"></a> result&lt;<a href="#graph"><a href="#graph"><code>graph</code></a></a>, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h4><a name="load_by_name"><code>load-by-name: func</code></a></h4>
<p>Load a <a href="#graph"><code>graph</code></a> by name.</p>
<p>How the host expects the names to be passed and how it stores the graphs for retrieval via
this function is <strong>implementation-specific</strong>. This allows hosts to choose name schemes that
range from simple to complex (e.g., URLs?) and caching mechanisms of various kinds.</p>
<h5>Params</h5>
<ul>
<li><a name="load_by_name.name"><code>name</code></a>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="load_by_name.0"></a> result&lt;<a href="#graph"><a href="#graph"><code>graph</code></a></a>, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h2><a name="wasi:nn_inference">Import interface wasi:nn/inference</a></h2>
<p>An inference &quot;session&quot; is encapsulated by a <a href="#graph_execution_context"><code>graph-execution-context</code></a>. This structure binds a
<a href="#graph"><code>graph</code></a> to input tensors before <a href="#compute"><code>compute</code></a>-ing an inference:</p>
<hr />
<h3>Types</h3>
<h4><a name="error"><code>type error</code></a></h4>
<p><a href="#error"><a href="#error"><code>error</code></a></a></p>
<p>
#### <a name="tensor">`type tensor`</a>
[`tensor`](#tensor)
<p>
#### <a name="tensor_data">`type tensor-data`</a>
[`tensor-data`](#tensor_data)
<p>
#### <a name="graph">`type graph`</a>
[`graph`](#graph)
<p>
#### <a name="graph_execution_context">`type graph-execution-context`</a>
`u32`
<p>Bind a `graph` to the input and output tensors for an inference.
<p>TODO: this is no longer necessary in WIT (https://github.com/WebAssembly/wasi-nn/issues/43)</p>
<hr />
<h3>Functions</h3>
<h4><a name="init_execution_context"><code>init-execution-context: func</code></a></h4>
<p>Create an execution instance of a loaded graph.</p>
<h5>Params</h5>
<ul>
<li><a name="init_execution_context.graph"><a href="#graph"><code>graph</code></a></a>: <a href="#graph"><a href="#graph"><code>graph</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="init_execution_context.0"></a> result&lt;<a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a>, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h4><a name="set_input"><code>set-input: func</code></a></h4>
<p>Define the inputs to use for inference.</p>
<h5>Params</h5>
<ul>
<li><a name="set_input.ctx"><code>ctx</code></a>: <a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a></li>
<li><a name="set_input.index"><code>index</code></a>: <code>u32</code></li>
<li><a name="set_input.tensor"><a href="#tensor"><code>tensor</code></a></a>: <a href="#tensor"><a href="#tensor"><code>tensor</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="set_input.0"></a> result&lt;_, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h4><a name="compute"><code>compute: func</code></a></h4>
<p>Compute the inference on the given inputs.</p>
<p>Note the expected sequence of calls: <a href="#set_input"><code>set-input</code></a>, <a href="#compute"><code>compute</code></a>, <a href="#get_output"><code>get-output</code></a>. TODO: this
expectation could be removed as a part of https://github.com/WebAssembly/wasi-nn/issues/43.</p>
<h5>Params</h5>
<ul>
<li><a name="compute.ctx"><code>ctx</code></a>: <a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="compute.0"></a> result&lt;_, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h4><a name="get_output"><code>get-output: func</code></a></h4>
<p>Extract the outputs after inference.</p>
<h5>Params</h5>
<ul>
<li><a name="get_output.ctx"><code>ctx</code></a>: <a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a></li>
<li><a name="get_output.index"><code>index</code></a>: <code>u32</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="get_output.0"></a> result&lt;<a href="#tensor_data"><a href="#tensor_data"><code>tensor-data</code></a></a>, <a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
