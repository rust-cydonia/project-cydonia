# Logging

ily mawaxine !!! <3333333333333333

TODO

`EventReader<LoggingEvent>`; disallow `EventWriter<LoggingEvent>` outside of
`mars-logging` somehow

value inspection; smth like `trait Value` which allows you to inspect the
"value" of any type (that implements it) at runtime. Basically similar to
reflection

for ICEs (internal cydonia errors) before logging has been fully initialized, we
should try to (and gracefully handle failure to) dump it to a file in json.
after that, it should do the same, but with more info, probably. stuff like
`tracing-error`'s `SpanTrace` (still mad about this not being `Spantrace`) i
guess. pre-logging fatal errors should be considered "early", and everything else
"late".

we actually won't use `tracing` for this as an `Event` is `!Send`. Fun. Let's
reimplement this ourselves!

PLEASE future cewentri actually write this.......

panics should always be considered a bug.

easy: disable the default `bevy::log::LogPlugin`

easy: our `LogPlugin` should be initialized first
