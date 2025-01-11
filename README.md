# fx-hash

[![crates.io](https://img.shields.io/crates/v/fx-hash.svg)](https://crates.io/crates/fx-hash)
[![Documentation](https://docs.rs/fx-hash/badge.svg)](https://docs.rs/fx-hash)

Fork of [rustc-hash](https://github.com/rust-lang/rustc-hash).
This is for personal usage only, please use `rustc-hash`.

A speedy, non-cryptographic hashing algorithm used by `rustc`.
The [hash map in `std`](https://doc.rust-lang.org/std/collections/struct.HashMap.html) uses SipHash by default, which provides resistance against DOS attacks.
These attacks aren't a concern in the compiler so we prefer to use a quicker,
non-cryptographic hash algorithm.

The original hash algorithm provided by this crate was one taken from Firefox,
hence the hasher it provides is called FxHasher. This name is kept for backwards
compatibility, but the underlying hash has since been replaced. The current
design for the hasher is a polynomial hash finished with a single bit rotation,
together with a wyhash-inspired compression function for strings/slices, both
designed by Orson Peters.

For `rustc` we have tried many different hashing algorithms. Hashing speed is
critical, especially for single integers. Spending more CPU cycles on a higher
quality hash does not reduce hash collisions enough to make the compiler faster
on real-world benchmarks.

## Usage

This crate provides `FxHashMap` and `FxHashSet` as collections.
They are simply type aliases for their `std::collection` counterparts using the Fx hasher.

```rust
use fx_hash::{FxHashMap, FxHashMapExt, FxHashSet, FxHashSetExt};

let mut map: FxHashMap<u32, u32> = FxHashMap::new();
map.insert(22, 44);

let mut set: FxHashSet<u32> = FxHashSet::new();
set.insert(27);
```

### `no_std`

The `std` feature is on by default to enable collections.
It can be turned off in `Cargo.toml` like so:

```toml
rustc-hash = { version = "0.1.0", default-features = false }
```
