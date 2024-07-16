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
<li>interface <a href="#wasi:nn_tensor_0.2.0_rc_2024_06_25"><code>wasi:nn/tensor@0.2.0-rc-2024-06-25</code></a></li>
<li>interface <a href="#wasi:nn_errors_0.2.0_rc_2024_06_25"><code>wasi:nn/errors@0.2.0-rc-2024-06-25</code></a></li>
<li>interface <a href="#wasi:nn_inference_0.2.0_rc_2024_06_25"><code>wasi:nn/inference@0.2.0-rc-2024-06-25</code></a></li>
<li>interface <a href="#wasi:nn_graph_0.2.0_rc_2024_06_25"><code>wasi:nn/graph@0.2.0-rc-2024-06-25</code></a></li>
</ul>
</li>
</ul>
<h2><a name="wasi:nn_tensor_0.2.0_rc_2024_06_25"></a>Import interface wasi:nn/tensor@0.2.0-rc-2024-06-25</h2>
<p>All inputs and outputs to an ML inference are represented as <a href="#tensor"><code>tensor</code></a>s.</p>
<hr />
<h3>Types</h3>
<h4><a name="tensor_dimensions"></a><code>type tensor-dimensions</code></h4>
<p><a href="#tensor_dimensions"><a href="#tensor_dimensions"><code>tensor-dimensions</code></a></a></p>
<p>The dimensions of a tensor.
<p>The array length matches the tensor rank and each element in the array describes the size of
each dimension</p>
<h4><a name="tensor_type"></a><code>enum tensor-type</code></h4>
<p>The type of the elements in a tensor.</p>
<h5>Enum Cases</h5>
<ul>
<li><a name="tensor_type.fp16"></a><code>FP16</code></li>
<li><a name="tensor_type.fp32"></a><code>FP32</code></li>
<li><a name="tensor_type.fp64"></a><code>FP64</code></li>
<li><a name="tensor_type.bf16"></a><code>BF16</code></li>
<li><a name="tensor_type.u8"></a><code>U8</code></li>
<li><a name="tensor_type.i32"></a><code>I32</code></li>
<li><a name="tensor_type.i64"></a><code>I64</code></li>
</ul>
<h4><a name="tensor_data"></a><code>type tensor-data</code></h4>
<p><a href="#tensor_data"><a href="#tensor_data"><code>tensor-data</code></a></a></p>
<p>The tensor data.
<p>Initially conceived as a sparse representation, each empty cell would be filled with zeros
and the array length must match the product of all of the dimensions and the number of bytes
in the type (e.g., a 2x2 tensor with 4-byte f32 elements would have a data array of length
16). Naturally, this representation requires some knowledge of how to lay out data in
memory--e.g., using row-major ordering--and could perhaps be improved.</p>
<h4><a name="tensor"></a><code>resource tensor</code></h4>
<hr />
<h3>Functions</h3>
<h4><a name="constructor_tensor"></a><code>[constructor]tensor: func</code></h4>
<h5>Params</h5>
<ul>
<li><a name="constructor_tensor.dimensions"></a><code>dimensions</code>: <a href="#tensor_dimensions"><a href="#tensor_dimensions"><code>tensor-dimensions</code></a></a></li>
<li><a name="constructor_tensor.ty"></a><code>ty</code>: <a href="#tensor_type"><a href="#tensor_type"><code>tensor-type</code></a></a></li>
<li><a name="constructor_tensor.data"></a><code>data</code>: <a href="#tensor_data"><a href="#tensor_data"><code>tensor-data</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="constructor_tensor.0"></a> own&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;</li>
</ul>
<h4><a name="method_tensor.dimensions"></a><code>[method]tensor.dimensions: func</code></h4>
<p>Describe the size of the tensor (e.g., 2x2x2x2 -&gt; [2, 2, 2, 2]). To represent a tensor
containing a single value, use <code>[1]</code> for the tensor dimensions.</p>
<h5>Params</h5>
<ul>
<li><a name="method_tensor.dimensions.self"></a><code>self</code>: borrow&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_tensor.dimensions.0"></a> <a href="#tensor_dimensions"><a href="#tensor_dimensions"><code>tensor-dimensions</code></a></a></li>
</ul>
<h4><a name="method_tensor.ty"></a><code>[method]tensor.ty: func</code></h4>
<p>Describe the type of element in the tensor (e.g., <code>f32</code>).</p>
<h5>Params</h5>
<ul>
<li><a name="method_tensor.ty.self"></a><code>self</code>: borrow&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_tensor.ty.0"></a> <a href="#tensor_type"><a href="#tensor_type"><code>tensor-type</code></a></a></li>
</ul>
<h4><a name="method_tensor.data"></a><code>[method]tensor.data: func</code></h4>
<p>Return the tensor data.</p>
<h5>Params</h5>
<ul>
<li><a name="method_tensor.data.self"></a><code>self</code>: borrow&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_tensor.data.0"></a> <a href="#tensor_data"><a href="#tensor_data"><code>tensor-data</code></a></a></li>
</ul>
<h2><a name="wasi:nn_errors_0.2.0_rc_2024_06_25"></a>Import interface wasi:nn/errors@0.2.0-rc-2024-06-25</h2>
<p>TODO: create function-specific errors (https://github.com/WebAssembly/wasi-nn/issues/42)</p>
<hr />
<h3>Types</h3>
<h4><a name="error_code"></a><code>enum error-code</code></h4>
<h5>Enum Cases</h5>
<ul>
<li>
<p><a name="error_code.invalid_argument"></a><code>invalid-argument</code></p>
<p>Caller module passed an invalid argument.
</li>
<li>
<p><a name="error_code.invalid_encoding"></a><code>invalid-encoding</code></p>
<p>Invalid encoding.
</li>
<li>
<p><a name="error_code.timeout"></a><code>timeout</code></p>
<p>The operation timed out.
</li>
<li>
<p><a name="error_code.runtime_error"></a><code>runtime-error</code></p>
<p>Runtime Error.
</li>
<li>
<p><a name="error_code.unsupported_operation"></a><code>unsupported-operation</code></p>
<p>Unsupported operation.
</li>
<li>
<p><a name="error_code.too_large"></a><code>too-large</code></p>
<p>Graph is too large.
</li>
<li>
<p><a name="error_code.not_found"></a><code>not-found</code></p>
<p>Graph not found.
</li>
<li>
<p><a name="error_code.security"></a><code>security</code></p>
<p>The operation is insecure or has insufficient privilege to be performed.
e.g., cannot access a hardware feature requested
</li>
<li>
<p><a name="error_code.unknown"></a><code>unknown</code></p>
<p>The operation failed for an unspecified reason.
</li>
</ul>
<h4><a name="error"></a><code>resource error</code></h4>
<hr />
<h3>Functions</h3>
<h4><a name="constructor_error"></a><code>[constructor]error: func</code></h4>
<h5>Params</h5>
<ul>
<li><a name="constructor_error.code"></a><code>code</code>: <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a></li>
<li><a name="constructor_error.data"></a><code>data</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="constructor_error.0"></a> own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h4><a name="method_error.code"></a><code>[method]error.code: func</code></h4>
<p>Return the error code.</p>
<h5>Params</h5>
<ul>
<li><a name="method_error.code.self"></a><code>self</code>: borrow&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_error.code.0"></a> <a href="#error_code"><a href="#error_code"><code>error-code</code></a></a></li>
</ul>
<h4><a name="method_error.data"></a><code>[method]error.data: func</code></h4>
<p>Errors can propagated with backend specific status through a string value.</p>
<h5>Params</h5>
<ul>
<li><a name="method_error.data.self"></a><code>self</code>: borrow&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_error.data.0"></a> <code>string</code></li>
</ul>
<h2><a name="wasi:nn_inference_0.2.0_rc_2024_06_25"></a>Import interface wasi:nn/inference@0.2.0-rc-2024-06-25</h2>
<p>An inference &quot;session&quot; is encapsulated by a <a href="#graph_execution_context"><code>graph-execution-context</code></a>. This structure binds a
<a href="#graph"><code>graph</code></a> to input tensors before <code>compute</code>-ing an inference:</p>
<hr />
<h3>Types</h3>
<h4><a name="error"></a><code>type error</code></h4>
<p><a href="#error"><a href="#error"><code>error</code></a></a></p>
<p>
#### <a name="tensor"></a>`type tensor`
[`tensor`](#tensor)
<p>
#### <a name="tensor_data"></a>`type tensor-data`
[`tensor-data`](#tensor_data)
<p>
#### <a name="graph_execution_context"></a>`resource graph-execution-context`
<p>Bind a <a href="#graph"><code>graph</code></a> to the input and output tensors for an inference.</p>
<h2>TODO: this may no longer be necessary in WIT
(https://github.com/WebAssembly/wasi-nn/issues/43)</h2>
<h3>Functions</h3>
<h4><a name="method_graph_execution_context.set_input"></a><code>[method]graph-execution-context.set-input: func</code></h4>
<p>Define the inputs to use for inference.</p>
<h5>Params</h5>
<ul>
<li><a name="method_graph_execution_context.set_input.self"></a><code>self</code>: borrow&lt;<a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a>&gt;</li>
<li><a name="method_graph_execution_context.set_input.name"></a><code>name</code>: <code>string</code></li>
<li><a name="method_graph_execution_context.set_input.tensor"></a><a href="#tensor"><code>tensor</code></a>: own&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_graph_execution_context.set_input.0"></a> result&lt;_, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="method_graph_execution_context.compute"></a><code>[method]graph-execution-context.compute: func</code></h4>
<p>Compute the inference on the given inputs.</p>
<p>Note the expected sequence of calls: <code>set-input</code>, <code>compute</code>, <code>get-output</code>. TODO: this
expectation could be removed as a part of
https://github.com/WebAssembly/wasi-nn/issues/43.</p>
<h5>Params</h5>
<ul>
<li><a name="method_graph_execution_context.compute.self"></a><code>self</code>: borrow&lt;<a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_graph_execution_context.compute.0"></a> result&lt;_, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="method_graph_execution_context.get_output"></a><code>[method]graph-execution-context.get-output: func</code></h4>
<p>Extract the outputs after inference.</p>
<h5>Params</h5>
<ul>
<li><a name="method_graph_execution_context.get_output.self"></a><code>self</code>: borrow&lt;<a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a>&gt;</li>
<li><a name="method_graph_execution_context.get_output.name"></a><code>name</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_graph_execution_context.get_output.0"></a> result&lt;own&lt;<a href="#tensor"><a href="#tensor"><code>tensor</code></a></a>&gt;, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
<h2><a name="wasi:nn_graph_0.2.0_rc_2024_06_25"></a>Import interface wasi:nn/graph@0.2.0-rc-2024-06-25</h2>
<p>A <a href="#graph"><code>graph</code></a> is a loaded instance of a specific ML model (e.g., MobileNet) for a specific ML
framework (e.g., TensorFlow):</p>
<hr />
<h3>Types</h3>
<h4><a name="error"></a><code>type error</code></h4>
<p><a href="#error"><a href="#error"><code>error</code></a></a></p>
<p>
#### <a name="tensor"></a>`type tensor`
[`tensor`](#tensor)
<p>
#### <a name="graph_execution_context"></a>`type graph-execution-context`
[`graph-execution-context`](#graph_execution_context)
<p>
#### <a name="graph"></a>`resource graph`
<p>An execution graph for performing inference (i.e., a model).</p>
<h4><a name="graph_encoding"></a><code>enum graph-encoding</code></h4>
<p>Describes the encoding of the graph. This allows the API to be implemented by various
backends that encode (i.e., serialize) their graph IR with different formats.</p>
<h5>Enum Cases</h5>
<ul>
<li><a name="graph_encoding.openvino"></a><code>openvino</code></li>
<li><a name="graph_encoding.onnx"></a><code>onnx</code></li>
<li><a name="graph_encoding.tensorflow"></a><code>tensorflow</code></li>
<li><a name="graph_encoding.pytorch"></a><code>pytorch</code></li>
<li><a name="graph_encoding.tensorflowlite"></a><code>tensorflowlite</code></li>
<li><a name="graph_encoding.ggml"></a><code>ggml</code></li>
<li><a name="graph_encoding.autodetect"></a><code>autodetect</code></li>
</ul>
<h4><a name="execution_target"></a><code>enum execution-target</code></h4>
<p>Define where the graph should be executed.</p>
<h5>Enum Cases</h5>
<ul>
<li><a name="execution_target.cpu"></a><code>cpu</code></li>
<li><a name="execution_target.gpu"></a><code>gpu</code></li>
<li><a name="execution_target.tpu"></a><code>tpu</code></li>
</ul>
<h4><a name="graph_builder"></a><code>type graph-builder</code></h4>
<p><a href="#graph_builder"><a href="#graph_builder"><code>graph-builder</code></a></a></p>
<p>The graph initialization data.
<p>This gets bundled up into an array of buffers because implementing backends may encode their
graph IR in parts (e.g., OpenVINO stores its IR and weights separately).</p>
<hr />
<h3>Functions</h3>
<h4><a name="method_graph.init_execution_context"></a><code>[method]graph.init-execution-context: func</code></h4>
<h5>Params</h5>
<ul>
<li><a name="method_graph.init_execution_context.self"></a><code>self</code>: borrow&lt;<a href="#graph"><a href="#graph"><code>graph</code></a></a>&gt;</li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="method_graph.init_execution_context.0"></a> result&lt;own&lt;<a href="#graph_execution_context"><a href="#graph_execution_context"><code>graph-execution-context</code></a></a>&gt;, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="load"></a><code>load: func</code></h4>
<p>Load a <a href="#graph"><code>graph</code></a> from an opaque sequence of bytes to use for inference.</p>
<h5>Params</h5>
<ul>
<li><a name="load.builder"></a><code>builder</code>: list&lt;<a href="#graph_builder"><a href="#graph_builder"><code>graph-builder</code></a></a>&gt;</li>
<li><a name="load.encoding"></a><code>encoding</code>: <a href="#graph_encoding"><a href="#graph_encoding"><code>graph-encoding</code></a></a></li>
<li><a name="load.target"></a><code>target</code>: <a href="#execution_target"><a href="#execution_target"><code>execution-target</code></a></a></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="load.0"></a> result&lt;own&lt;<a href="#graph"><a href="#graph"><code>graph</code></a></a>&gt;, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
<h4><a name="load_by_name"></a><code>load-by-name: func</code></h4>
<p>Load a <a href="#graph"><code>graph</code></a> by name.</p>
<p>How the host expects the names to be passed and how it stores the graphs for retrieval via
this function is <strong>implementation-specific</strong>. This allows hosts to choose name schemes that
range from simple to complex (e.g., URLs?) and caching mechanisms of various kinds.</p>
<h5>Params</h5>
<ul>
<li><a name="load_by_name.name"></a><code>name</code>: <code>string</code></li>
</ul>
<h5>Return values</h5>
<ul>
<li><a name="load_by_name.0"></a> result&lt;own&lt;<a href="#graph"><a href="#graph"><code>graph</code></a></a>&gt;, own&lt;<a href="#error"><a href="#error"><code>error</code></a></a>&gt;&gt;</li>
</ul>
