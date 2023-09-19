# Value Inspection

## Abstract

<!-- markdownlint-disable MD033 -->

The ability to inspect the current value of a [trait object](https://doc.rust-lang.org/nightly/book/ch17-02-trait-objects.html).
This could be done with [reflection](https://en.wikipedia.org/wiki/Reflective_programming)
in other languages, however reflection is not possible in Rust with
non-`'static` lifetimes<sup>[1](#1)</sup> so this would limit what can be
inspected, and in general, downcasting is just verbose. Instead, to make this as
agnostic of the underlying type as possible, we will convert to a common
representation of a Rust value which can then be [visited](https://en.wikipedia.org/wiki/Visitor_pattern)
at runtime.

<!-- markdownlint-enable MD033 -->

## Motivation

The ability to dispatch structured data to logging subscribers; Informally,
giving logging the ability to arbitrarily format or discard this data however it
wishes. This is advantageous over simply `dyn Debug` as this forces it to be
formatted in one specific way. For example, an in-game console can use this to
allow specific fields to be collapsed, rather than just displaying it in text.
How nice!

We can also serialize data like this easily. Deriving `Serialize` can increase
compile time a lot. Serialization of `Value`, however, is very simple.

## Guide-level Explanation

TODO

## Reference-level Explanation

TODO

## Drawbacks

Complexity - `dyn Debug` is simple, while visiting a `Value` isn't.

## Rationale and Alternatives

We could use [`bevy_reflect`](https://docs.rs/bevy_reflect/latest/bevy_reflect/index.html)
instead. This will restrict us to `'static` lifetimes, however, which means we
will be unable to inspect types with borrowed data. This is a problem, but the
true extent of this is unclear.

## Prior Art

The [`valuable`](https://docs.rs/valuable/latest/valuable/index.html) crate.

## Unresolved Questions

TODO

## Future Possibilities

Publishing this on [crates.io](https://crates.io/) so others can use this too.

### [1]

The reason for this, very broadly, is because lifetimes are [erased](https://doc.rust-lang.org/nightly/nightly-rustc/rustc_middle/ty/type.RegionKind.html#variant.ReErased)
at runtime so downcasting to `A<'static>` from `A<'a>` would be unsound; you're
extending the lifetime yet it still only lives for `'a`! There's currently no
way to enforce this either in the type system or at runtime, and likely, there
never will be.

Constraining `Any` to `'static` means there's no possible way to extend a
lifetime like this, as `'static` lifetimes already live as long as possible. As
such, downcasting is safe yet potentially less powerful than in other languages.
It's a tradeoff.
