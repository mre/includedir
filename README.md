includedir
===========

[![Travis](https://img.shields.io/travis/tilpner/includedir.svg?style=flat-square)](https://travis-ci.org/tilpner/includedir)
[![Appveyor](https://img.shields.io/appveyor/ci/tilpner/includedir.svg?label=appveyor&style=flat-square)](https://ci.appveyor.com/project/tilpner/includedir)
[![Crates.io version](https://img.shields.io/crates/v/includedir.svg?style=flat-square)](https://crates.io/crates/includedir)
[![docs.rs](https://docs.rs/includedir/badge.svg)](https://docs.rs/includedir)
[![Crates.io license](https://img.shields.io/crates/l/includedir.svg?style=flat-square)](https://crates.io/crates/includedir)

Include a directory in your Rust binary, e.g. static files for your web server or assets for your game.

## Features

* [x] Automatically compile data into binary
* [x] Use [rust-phf](https://github.com/sfackler/rust-phf) for efficient lookup
* [x] Wrapping API around the phf map, to abstract away additional features
* [x] Compression, with optional crate "flate2"
* [x] Reading from source files for debug builds

## Example

**Cargo.toml**
```toml
[package]
name = "example"
version = "0.1.0"

build = "build.rs"
include = ["data"]

[dependencies]
phf = "0.8.0"
includedir = "0.6.0"

[build-dependencies]
includedir_codegen = "0.6.0"
```

**build.rs**

```rust
extern crate includedir_codegen;

use includedir_codegen::Compression;

fn main() {
    includedir_codegen::start("FILES")
        .dir("data", Compression::Gzip)
        .build("data.rs")
        .unwrap();
}
```

**src/main.rs**

```rust
extern crate includedir;
extern crate phf;

use std::env;

include!(concat!(env!("OUT_DIR"), "/data.rs"));

fn main() {
    FILES.set_passthrough(env::var_os("PASSTHROUGH").is_some());

    println!("{:?}", FILES.get("data/foo"))
}
```
