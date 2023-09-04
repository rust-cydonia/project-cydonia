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

Queries can be called like any other method on an ADT, and take one or more key
arguments. In this context, key represents a newtype which stores some index or
pointer into an interner. The callee will then either compute the return value,
if this is the first time it's been called with these arguments, or get it from
an arena or on disk.

The implementation of a query is guaranteed to have no side-effects, informally,
they can be considered pure functions. It's a logic bug for one to do so, and
careful thought must be put into its implementation to ensure this isn't the
case.

To allow mutation or ownership of data acquired from a query, the `Steal<T>`
type must be used which wraps the returned data of the query. The `Steal<T>`
type represents immutable, borrowed data, until it's stolen in which it can
never be read from again. Queries return pure, "immutable" data and as such
simply returning a `Mutex` or any other ADT with interior mutability is
disallowed. The `Steal<T>` type solves this by disallowing mutation until it can
no longer be observed. When stealing data using the `steal` method, there are
two things you must be sure of:

- You require ownership of the data
- The query will never be read from again

As such, if you only need to read it, you should always call `borrow` instead of
`steal`. There is no way to get a mutable reference to `T` in a `Steal<T>` to
prevent premature mutation.

It's immediate Undefined Behavior to mutate the wrapped data before calling
`steal` because `T` must implement `Freeze`. Informally, there is no way to have
interior mutability within a `Steal<T>` and transmuting the `&` acquired from
`borrow` is Undefined Behavior. No, you can't do it, no, you're not special.

## Reference-level Explanation

<!-- TODO: Explain in detail how the implementation will work. -->

## Drawbacks

The complexity of the implementation may not be worth it for Cydonia. While we
will make this as easy as possible to use, it will inevitably end up making the
codebase more complex and harder to understand for new Rustaceans.

Queries which return `Steal<T>` are also something that will end up making
Cydonia harder to work with, as accessing this data will panic if it has been
stolen from. This is intentionally the only way to mutate data from a query and
so this type will be commonly used.

## Rationale And Alternatives

<!-- TODO: Explain WHY. -->

## Prior Art

The [Salsa](https://github.com/salsa-rs/salsa) crate allows for defining and
calling queries in an idiomatic way, inspired by [rustc's query system](https://rustc-dev-guide.rust-lang.org/query.html).

## Unresolved Questions

<!-- TODO: A LOT. -->

## Future Possibilities

N/A
