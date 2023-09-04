# Query/Cache System RFC

:3 :3 :3 :3 <3 :3
nya mrrp meow mrrp

## Summary

Cache interned data implementing [`Freeze`](https://stdrs.dev/nightly/x86_64-unknown-linux-gnu/core/marker/trait.Freeze.html)
in an [arena](https://stackoverflow.com/questions/12825148/what-is-the-meaning-of-the-term-arena-in-relation-to-memory)
for later use or on disk for future runs of Cydonia.

## Motivation

<!-- TODO: This may be TMI in the motivation section. -->

Interning large data structures makes equality comparisons [O(1) instead of O(n)](https://matklad.github.io/2020/03/22/fast-simple-rust-interner.html),
as an interner guarantees each value stored within is unique. Passing around
interned data is also more efficient as it can be represented as either an index
or pointer into some interner. In `rustc`, the interner used is the [`TyCtxt`](https://doc.rust-lang.org/nightly/nightly-rustc/rustc_middle/ty/struct.TyCtxt.html)
(spoken as the type context) which internally holds this data in a `HashMap`.
We will likely use an arena instead to reduce both number of allocations and
making accessing the underlying data more efficient.

The main benefit of queries is that these do this work for you; i.e., they will
get this data from the global interner or on disk automagically. It's all
abstracted away, making these just as easy to use as any function.

## Guide-level Explanation

## Reference-level Explanation

## Drawbacks

## Rationale And Alternatives

## Prior Art

## Unresolved Questions

## Future Possibilities
