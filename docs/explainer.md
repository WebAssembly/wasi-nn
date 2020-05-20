# Design Motivation and Considerations:

## Why WASM for ML?
Trained machine learning models are typically deployed on a variety of devices with different architecture and operating systems.
Web Assembly provides an ideal portable form of deployment for those models. 

## Why WASI?
Although a whole machine learning framework could be potentially compiled into WASM, special hardware acceleration is often needed in order to be performant. Fro example, SIMD instructions such as AVX512 on a CPU can speed up performance by several hundred times. Other hardware acclerator examples are GPU, TPU, FPGA. All of those accerleration mechanisms are not available within WASM. In addition,
the field of machine learning is still evolving rapidly, with new operations and network topologies emerging continuously. It would be a challenge to define an evolving set of operations to support in the API. 

## Model loader API
Out approach is to start with a model loader API, inspired by WebNN’s model loader proposal. It treats a machine learning graph as an opaque handle that we can use to send inputs, get outputs, and perform forward propagation. Since inferencing is the main ML use case, and our initial focus, this set of API is sufficent. 

Even though we are focusing on inferencing now, future-proof is one of our design goals. For example, future APIs could be added to support back propagation without changing our current model loading and forward propagation APIs.

Another design goal is to make the API ML framework and model format agnostic. We expect a device vendor to provide NN framework and graph encoding support. Load method would return an error message when an unsupported model encoding scheme is passed in. This approach is similar to how a browser deals with image or video encoding.



