# Unit System

owo

## Summary

A large collection of ADTs corresponding to common units, like `Millimeters` for
mm, `Kilometers` for km, etc., which can all be converted between each other
implicitly through generics. This will make our data strongly typed and explicit
in the type system what unit a function expects.

## Motivation

Simply using `f32`/`f64` as a parameter in Rust provides only one guarantee: it's a IEEE--754 floating point number. Us humans use arbitrary and often confusing unit systems; for example, the astronomical unit, corresponding to the average distance from the Earth to its parent; Sol. When a function takes a float and it interprets it as # of au, you can, for example, pass a float that's in kilometers instead which may cause all sorts of issues! We can take advantage of Rust's strong type system to take only units we expect, or implicitly convert between them (if possible).

<!-- TODO: Rewrite this section pls maxine help -->

## Guide-level Explanation

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
