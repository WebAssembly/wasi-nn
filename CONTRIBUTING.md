# Contributing

Thank you for contributing to this specification. This repository follows the
same guidelines as the [WASI contribution guidelines].

[WASI contribution guidelines]: https://github.com/WebAssembly/WASI/blob/main/Contributing.md

The specification is written in WIT (WebAssembly Interface Types) syntax in code
blocks inside a Markdown file (`wasi-nn.wit.md`). This is parseable by WIT tools
(e.g., [`wit-bindgen`], [`wit-abi`]). Note that, when altering the WIT
specification, the ABI file (`wasi-nn.abi.md`) must also be updated or CI will
fail. To use [`wit-abi`] to update the ABI file, run:

[`wit-bindgen`]: https://github.com/bytecodealliance/wit-bindgen
[`wit-abi`]: https://github.com/WebAssembly/wasi-tools/tree/main/wit-abi

```console
$ git clone https://github.com/WebAssembly/wasi-tools
$ cd wasi-tools/wit-abi
$ cargo build
$ target/debug/wit-abi <path to wasi-nn directory>
```
