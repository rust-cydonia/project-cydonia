# Query/Cache System RFC

:3 :3 :3 :3 <3 :3

## Summary

Cache interned data implementing [`Freeze`](https://stdrs.dev/nightly/x86_64-unknown-linux-gnu/core/marker/trait.Freeze.html) in an [arena](https://stackoverflow.com/questions/12825148/what-is-the-meaning-of-the-term-arena-in-relation-to-memory) for later use or on disk for future runs of Cydonia.

## Motivation

Interning large data structures makes equality comparison a simple pointer equality check, [making this operation O(1) instead of O(n)](https://matklad.github.io/2020/03/22/fast-simple-rust-interner.html). Passing around interned data is also more efficient since it can be represented as either an index or pointer into some interner. In `rustc`, the main interner is the [`TyCtxt`](https://doc.rust-lang.org/nightly/nightly-rustc/rustc_middle/ty/struct.TyCtxt.html) (type context)
