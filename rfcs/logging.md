# Logging

ily mawaxine !!! <3333333333333333

TODO

`EventReader<LoggingEvent>`; disallow `EventWriter<LoggingEvent>` outside of `mars-logging` somehow

for ICEs (internal cydonia errors) before logging has been fully initialized, we
should try to (and gracefully handle failure to) dump it to a file in json.
after that, it should do the same, but with more info, probably. stuff like
`tracing-error`'s `SpanTrace` (still mad about this not being `Spantrace`) i
guess. pre-logging fatal errors should be considered "early", and everything else
"late".

unresolved: should we name this something like IME for internal mars error, or
some silly acronym for MIME?

panics should always be considered a bug.

easy: disable the default `bevy::log::LogPlugin`

easy: our `LogPlugin` should be initialized first
