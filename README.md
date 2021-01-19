[wasi-nn] is a proposal to add a WASI module for machine learning. The current API is available [here](https://github.com/WebAssembly/wasi-nn/blob/master/phases/ephemeral/witx/wasi_ephemeral_nn.witx) and more details about wasi-nn are presented in this [blog post](https://bytecodealliance.org/articles/using-wasi-nn-in-wasmtime) (e.g., explaining why wasi-nn should be a system-provided capability). Following the [WASI proposal process](https://github.com/WebAssembly/WASI/blob/master/docs/Process.md), this repository is a logical fork of the [main WASI repo](https://github.com/WebAssembly/WASI). 

----

# WebAssembly System Interface

![WASI](WASI.png)

This repository is for the WebAssembly System Interface (WASI) Subgroup of the
[WebAssembly Community Group]. Its [Charter] describes the goals, scope and
deliverables of the group. The repository may contain meeting notes, reports,
documentation, specifications and/or software produced by the group (although
larger projects may also have their own repositories).

[WebAssembly Community Group]: https://www.w3.org/community/webassembly/
[Charter]: Charter.md

We'll be adding more content here before long, but for now, check out these:
 - https://hacks.mozilla.org/2019/03/standardizing-wasi-a-webassembly-system-interface/ - Blog post introducing WASI
 - https://wasi.dev/ - Links to more information about WASI, including
   how to get started using it.
 - https://github.com/WebAssembly/WASI/issues - This repo's issue tracker

The issue tracker is the place to ask questions, make suggestions, and start
discussions.

Schedules and agendas for video conference and in-person meetings are posted
[here](meetings/README.md).

API specification proposals are [here](phases/README.md).
