# Query/Cache System RFC

:3 :3 :3 :3 <3 :3
nya mrrp meow mrrp

## Summary

Cache interned data implementing the `Cacheable` trait, similar to the rustc
interal [`Freeze`](https://stdrs.dev/nightly/x86_64-unknown-linux-gnu/core/marker/trait.Freeze.html)
trait in an [arena](https://stackoverflow.com/questions/12825148/what-is-the-meaning-of-the-term-arena-in-relation-to-memory)
for later use or on disk for future runs of Cydonia.

## Motivation

Interning large data structures makes equality comparisons [O(1) instead of O(n)](https://matklad.github.io/2020/03/22/fast-simple-rust-interner.html),
as an interner guarantees each value stored within is unique. Passing around
interned data is also more efficient as it can be represented as either an index
into some interner or pointer. In `rustc`, the interner we use is the [`TyCtxt`](https://doc.rust-lang.org/nightly/nightly-rustc/rustc_middle/ty/struct.TyCtxt.html)
(spoken as the type context) which internally holds this data in a `HashMap`.
We will likely use an arena instead to reduce both number of allocations and
making accessing the underlying data more efficient.

The main benefit of queries is that these do this work for you; i.e., they will
get this data from the global interner or on disk automagically. It's all
abstracted away, making these just as easy to use and write as any function.

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
type must be used which wraps the returned data of the query. The `Steal` type
represents immutable, borrowed data, until it's stolen in which it can never be
read from again. Queries return pure, immutable data and as such simply
returning a `Mutex` or any other ADT with interior mutability is disallowed. The
`Steal` type solves this by disallowing mutation until it can no longer be
observed as any further read will panic. When stealing data using the `steal`
method, there are two things you must be sure of:

- You require ownership of the data
- The query will never be read from again

As such, if you only need to read it, you should always call `borrow` instead of
`steal` so others can access it too. There is no way to get a mutable reference
to `T` in a `Steal<T>` to prevent premature mutation.

It's immediate Undefined Behavior to mutate the wrapped data before calling
`steal` because `T` must implement `Cacheable`. There is no way to (safely)
have interior mutability with a type implementing `Cacheable` (as this would be
a violation of the `unsafe impl Cacheable` contract) and transmuting the `&T`
acquired from `borrow` is Undefined Behavior. No, you can't do it, no, you're
not special.

## Reference-level Explanation

### The `Cacheable` trait

The semantics of this trait are similar to rustc's `Freeze` trait, in that any
type with interior mutability cannot be cached. However, this also extends to
types with interior mutability behind an indirection (like `&T` or `&mut T`). As
such, any type that implements this can be thought of as "frozen".

As auto traits are currently unstable, we must implement this manually on types.
Implementing this on a type should be `unsafe`, even if it isn't in the current
implementation, as there are no guarantees this will not trigger Undefined
Behavior in the future if used incorrectly.

### The `Steal` type

This is the one exception to the above `Cacheable` requirement. This can be
freely mutated through `steal` which gives ownership of the underlying data, and
makes it inaccessible to future callers of the query. You cannot undo this
operation, so tread carefully!

This type can be thought of as part of the query system itself rather than its
own type, thus why it can bypass the normal mutability restrictions. It's
similar to panicking upon calling the query but explicit in the type system.

<!-- TODO: We need to describe the actual query system too. -->

## Drawbacks

The complexity of the implementation may not be worth it for Cydonia. While we
will make this as easy as possible to use, it will inevitably end up making the
codebase more complex and harder to understand for new Rustaceans.

Queries which return `Steal<T>` are also something that will end up making
Cydonia harder to work with, as accessing this data will panic if it has been
stolen from. This is intentionally the only way to mutate data from a query and
so this type will be commonly used.

## Rationale and Alternatives

Queries are faster and more efficient in memory usage than regular functions in
many cases despite the upfront cost of hashing. They also allow us to compute
something really complex once then reuse it many times. For example, in the
future we could calculate N-body physics for objects in a system over a small
timespan then cache it on disk.

## Prior Art

The [Salsa](https://github.com/salsa-rs/salsa) crate allows for defining and
calling queries in an idiomatic way, inspired by [rustc's query system](https://rustc-dev-guide.rust-lang.org/query.html).

## Unresolved Questions

How exactly we will disallow interior mutability in the result of a query.
Currently it seems we will need to put careful thought into ensuring values
returned by a query can never be visibly mutated (minus `Steal`, of course).

The names of these concepts are up for debate. For example, maybe we can use
`Internable` instead of `Cacheable`?

## Future Possibilities

Publishing this on [crates.io](https://crates.io/) so others can use this too.
