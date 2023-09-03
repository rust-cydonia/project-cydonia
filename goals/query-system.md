# Query/Cache System

This would work by hashing the “id” of a key, a `derive` macro would turn an input `struct` into a `struct` with a private key field. This would then be hashed and the data this id points to will be cached either in memory or on disk. Getter methods would be used to access the underlying data idiomatically. We would need some “context” type that stores the values and allows you to call these queries, similarly to `TyCtxt` in `rustc`. We also get dependency tracking for free with queries, but we don’t need this.

Because this takes time to load or hash, this should only be done when the value is particularly costly to compute. This also increases both disk space and memory usage of Cydonia and as such, this should only be used when the types aren’t crazy large. We also need some way to disable this system so we can target systems with low disk space or memory. We must be careful to ensure that the performance cost of hashing and loading does not outweigh the benefit of not recomputing everything.

This will be similar to how the [salsa](https://github.com/salsa-rs/salsa) crate works, but more compatible with bevy’s ECS. Is this unnecessary? Yes, we could use salsa, with a thin wrapper plugin, but this will likely run into unforeseen issues. We should try this though, just in case.

TODO(Centri3): Word this differently.
