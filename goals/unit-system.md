# Unit System

To do this, we will want to create tons of newtypes corresponding to common
units; like `Millimeters` for mm, `Centimeters` for cm, etc. To make this
not be a pain to work with, each type will implement `Into<T>` where `T`
is another related unit, for every other related unit. This can be done using a
macro (`macro_rules!`) but will still result in tons of boilerplate. It’s up for
debate whether a specific trait should be used instead (something like
`IntoMeterslike`).

The aforementioned parser should also allow “km/s” and such as a suffix, which
will construct these types. If it is not present, it uses the default for that
field-value pair (which will usually be the type of the field in the underlying
`struct`). This will only be available for the “New Format”.

The default unit for distance in Cydonia will be picometers, but this is subject
to change. Note that this is not the default for types in a script; only the
unit used for simulation, as unnecessary as it is.

TODO(Centri3): Word this differently.
