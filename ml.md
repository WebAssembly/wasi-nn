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
<li>interface <a href="#wasi:nn_inference"><code>wasi:nn/inference</code></a></li>
<li>interface <a href="#wasi:nn_graph"><code>wasi:nn/graph</code></a></li>
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
<h4><a name="tensor"><code>resource tensor</code></a></h4>
<hr />
<h3>Functions</h3>
<h4><a name="constructor_tensor"><code>[constructor]tensor: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="constructor_tensor.dimensions"><code>dimensions</code></a>: <a href="#tensor_dimensions"><a href="#tensor_dimensions"><code>tensor-dimensions</code></a></a></li>
<li><a name="constructor_tensor.ty"><code>ty</code></a>: <a href="#tensor_type"><a href="#tensor_type"><code>tensor-type</code></a></a></li>
<li><a name="constructor_tensor.data"><code>data</code></a>: <a href="#tensor_data"><a href="#tensor_data"><code>tensor-data</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="constructor_tensor.0"></a> own&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;</li>
</ul>
<h4><a name="method_tensor.dimensions"><code>[method]tensor.dimensions: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="method_tensor.dimensions.self"><code>self</code></a>: borrow&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_tensor.dimensions.0"></a> <a href="#tensor_dimensions"><a href="#tensor_dimensions"><code>tensor-dimensions</code></a></a></li>
</ul>
<h4><a name="method_tensor.ty"><code>[method]tensor.ty: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="method_tensor.ty.self"><code>self</code></a>: borrow&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_tensor.ty.0"></a> <a href="#tensor_type"><a href="#tensor_type"><code>tensor-type</code></a></a></li>
</ul>
<h4><a name="method_tensor.data"><code>[method]tensor.data: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="method_tensor.data.self"><code>self</code></a>: borrow&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_tensor.data.0"></a> <a href="#tensor_data"><a href="#tensor_data"><code>tensor-data</code></a></a></li>
</ul>
<h2><a name="wasi:nn_errors">Import interface wasi:nn/errors</a></h2>
<p>TODO: create function-specific errors (https://github.com/WebAssembly/wasi-nn/issues/42)</p>
<hr />
<h3>Types</h3>
<h4><a name="error_code"><code>enum error-code</code></a></h4>
<h5>Enum Cases</h5>
<ul>
<li><a name="error_code.invalid_argument"><code>invalid-argument</code></a></li>
<li><a name="error_code.invalid_encoding"><code>invalid-encoding</code></a></li>
<li><a name="error_code.timeout"><code>timeout</code></a></li>
<li><a name="error_code.runtime_error"><code>runtime-error</code></a></li>
<li><a name="error_code.unsupported_operation"><code>unsupported-operation</code></a></li>
<li><a name="error_code.too_large"><code>too-large</code></a></li>
<li><a name="error_code.not_found"><code>not-found</code></a></li>
<li><a name="error_code.security"><code>security</code></a></li>
<li><a name="error_code.unknown"><code>unknown</code></a></li>
</ul>
<h4><a name="error"><code>resource error</code></a></h4>
<hr />
<h3>Functions</h3>
<h4><a name="constructor_error"><code>[constructor]error: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="constructor_error.code"><code>code</code></a>: <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a></li>
<li><a name="constructor_error.data"><code>data</code></a>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="constructor_error.0"></a> own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_error.code"><code>[method]error.code: func</code></a></h4>
<p>Return the error code.</p>
<h5>Params</h5>
<ul>
<li><a name="method_error.code.self"><code>self</code></a>: borrow&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_error.code.0"></a> <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a></li>
</ul>
<h4><a name="method_error.data"><code>[method]error.data: func</code></a></h4>
<p>Errors can propagated with backend specific status through a string value.</p>
<h5>Params</h5>
<ul>
<li><a name="method_error.data.self"><code>self</code></a>: borrow&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_error.data.0"></a> <code>string</code></li>
</ul>
<h2><a name="wasi:nn_inference">Import interface wasi:nn/inference</a></h2>
<p>An inference &quot;session&quot; is encapsulated by a <a href="#graph_execution_context"><code>graph-execution-context</code></a>. This structure binds a
<a href="#graph"><code>graph</code></a> to input tensors before <code>compute</code>-ing an inference:</p>
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
#### <a name="graph_execution_context">`resource graph-execution-context`</a>
<hr />
<h3>Functions</h3>
<h4><a name="method_graph_execution_context.set_input"><code>[method]graph-execution-context.set-input: func</code></a></h4>
<p>Define the inputs to use for inference.</p>
<h5>Params</h5>
<ul>
<li><a name="method_graph_execution_context.set_input.self"><code>self</code></a>: borrow&lt;<a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a>&gt;</li>
<li><a name="method_graph_execution_context.set_input.name"><code>name</code></a>: <code>string</code></li>
<li><a name="method_graph_execution_context.set_input.tensor"><a href="#tensor"><code>tensor</code></a></a>: own&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_graph_execution_context.set_input.0"></a> result&lt;_, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="method_graph_execution_context.compute"><code>[method]graph-execution-context.compute: func</code></a></h4>
<p>Compute the inference on the given inputs.</p>
<p>Note the expected sequence of calls: <code>set-input</code>, <code>compute</code>, <code>get-output</code>. TODO: this
expectation could be removed as a part of
https://github.com/WebAssembly/wasi-nn/issues/43.</p>
<h5>Params</h5>
<ul>
<li><a name="method_graph_execution_context.compute.self"><code>self</code></a>: borrow&lt;<a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_graph_execution_context.compute.0"></a> result&lt;_, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="method_graph_execution_context.get_output"><code>[method]graph-execution-context.get-output: func</code></a></h4>
<p>Extract the outputs after inference.</p>
<h5>Params</h5>
<ul>
<li><a name="method_graph_execution_context.get_output.self"><code>self</code></a>: borrow&lt;<a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a>&gt;</li>
<li><a name="method_graph_execution_context.get_output.name"><code>name</code></a>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_graph_execution_context.get_output.0"></a> result&lt;own&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
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
#### <a name="graph_execution_context">`type graph-execution-context`</a>
[`graph-execution-context`](#graph_execution_context)
<p>
#### <a name="graph">`resource graph`</a>
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
<h4><a name="method_graph.init_execution_context"><code>[method]graph.init-execution-context: func</code></a></h4>
<h5>Params</h5>
<ul>
<li><a name="method_graph.init_execution_context.self"><code>self</code></a>: borrow&lt;<a href="#graph"><a href="#graph"><code>graph</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_graph.init_execution_context.0"></a> result&lt;own&lt;<a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a>&gt;, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
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
<li><a name="load.0"></a> result&lt;own&lt;<a href="#graph"><a href="#graph"><code>graph</code></a></a>&gt;, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
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
<li><a name="load_by_name.0"></a> result&lt;own&lt;<a href="#graph"><a href="#graph"><code>graph</code></a></a>&gt;, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
