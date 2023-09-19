# Value Inspection

## Abstract

<!-- markdownlint-disable MD033 -->

The ability to inspect the current value of a [trait object](https://doc.rust-lang.org/nightly/book/ch17-02-trait-objects.html).
This could be done with [reflection](https://en.wikipedia.org/wiki/Reflective_programming)
in other languages, however reflection is not possible in Rust with
non-`'static` lifetimes<sup>[1](#1)</sup> so this would limit what can be inspected, and in
general, downcasting is just verbose. Instead, to make this as agnostic of the
underlying type as possible, we will convert to a common representation of a
Rust value which can then be [visited](https://en.wikipedia.org/wiki/Visitor_pattern)
at runtime.

<!-- markdownlint-enable MD033 -->

## Motivation


## [1]

The reason for this, very broadly, is because lifetimes are [erased](https://doc.rust-lang.org/nightly/nightly-rustc/rustc_middle/ty/type.RegionKind.html#variant.ReErased) at runtime so downcasting to `A<'static>` from `A<'a>` would be unsound; you're extending the lifetime yet it still only lives for `'a`! There's currently no way to enforce this in the type system, and likely, there never will be.

It's possible to make [`Any`](https://doc.rust-lang.org/nightly/std/any/trait.Any.html) an `unsafe trait` and its methods `unsafe` as well, but this is ridiculously unsafe. While it is no longer unsound, it is still Undefined Behavior!

