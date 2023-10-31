# Contributing

Thank you for contributing to this specification. This repository follows the
same guidelines as the [WASI contribution guidelines].

[WASI contribution guidelines]: https://github.com/WebAssembly/WASI/blob/main/Contributing.md

The specification is written in WIT (WebAssembly Interface Types) &mdash; see `wit/wasi-nn.wit`.
This is parseable by WIT tools (e.g., [`wit-bindgen`], [`wit-abi`]). Note that, when altering the
WIT specification, the Markdown example file (`ml-example.md`) must also be updated or CI will fail.
To use [`wit-bindgen`] to update the ABI file, run:

[`wit-bindgen`]: https://github.com/bytecodealliance/wit-bindgen
[`wit-abi`]: https://github.com/WebAssembly/wasi-tools/tree/main/wit-abi

```console
$ cargo install wit-bindgen-cli@0.12.0
$ wit-bindgen markdown --html-in-md wit
```
