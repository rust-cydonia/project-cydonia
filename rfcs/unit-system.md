# Unit System

owo

## Summary

A large collection of [ADT](https://en.wikipedia.org/wiki/Algebraic_data_type)s
corresponding to common units, like `Millimeters` for mm, `Kilometers` for km,
etc., which can all be converted between each other implicitly through generics.
This will make our data strongly typed and explicit in the type system what unit
a function expects.

## Motivation

Simply using `f32` and/or `f64` as a function parameter in Rust provides only
one (strong) guarantee - it's an IEEE-754 conforming floating point number. Us
humans use arbitrary and often confusing unit systems - for example, the
astronomical unit, corresponding to the average distance from the Earth to its
parent; Sol. When a function takes a float and it interprets it as # of au, you
can, if you're eepy, pass a float that's in kilometers instead which may cause
all sorts of issues! We can take advantage of Rust's strong type system to take
only units we expect, or implicitly convert between them (if possible).

<!-- TODO: Rewrite this section pls mawaxine help -->

## Guide-level Explanation

If you wish to take any unit of distance, you should prefer this type over
`f64`:

```rust
fn owo(into_meters: impl TryInto<Meters>) -> ... { ... }
```

This may be overwhelming if you're new to Rust, but don't worry; it's actually
quite simple!

In this context, [`impl`] can be thought of as defining an anonymous type
parameter. For further reading, see the [reference](https://doc.rust-lang.org/reference/types/impl-trait.html).
[`TryInto<T>`](https://doc.rust-lang.org/nightly/std/convert/trait.TryInto.html)
is a [trait](https://doc.rust-lang.org/nightly/std/keyword.trait.html) which
defines fallible conversions, where the type `T` (within the angle brackets) is
the type being converted to. `Meters` is a type corresponding to the meter. So,
in english - this type says "Give me any type that can be converted into meters,
even if it may fail". Inside the function, conversion is done like this:

```rust
into_meters.try_into().unwrap();
```

If you're in a function returning [`Result`](https://doc.rust-lang.org/nightly/std/result/enum.Result.html),
consider using the try operator instead - `?`.

```diff
-into_meters.try_into().unwrap();
+into_meters.try_into()?;
```

Now you can use interpret this parameter as meters carefree!

<!-- TODO: Explain how to add new units -->
<!-- TODO: Explain how to convert from a numeric type to a unit using traits >

## Reference-level Explanation

<!-- TODO: Explain how we will actually do this. -->

## Drawbacks

## Rationale and Alternatives

## Prior Art

N/A

## Unresolved Questions

<!-- TODO -->

## Future Possibilities

Publishing this on [crates.io](https://crates.io/) so others can use this too.

[`impl`]: https://doc.rust-lang.org/nightly/std/keyword.impl.html
