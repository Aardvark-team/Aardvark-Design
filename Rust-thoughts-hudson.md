# Thoughts on Rust

Rust's concepts are strikingly similar to many of the ideas that I developed independently.
Here's what matches what I had developed conceptually:

- Type inference.
- Blocks as expressions that can return values (e.g. `let x = { let y = 1; y + 1 }`).
- Using dot notation for arrays and tuples (e.g. `let a = [1, 2, 3]; a.0`).
- `match` exhaustive pattern matching (e.g. `match x { 1 => 1, 2 => 2, _ => 3 }`).
- In place pattern matching. In Rust, that's `if let`.
- Compile-time safety and type checking.
- Using structs for classes.
- Immutable default.
- Traits & implementations.

There are still many things that I don't like about Rust, however:

- The macro system
- Implicit returns
- Namespaces

Now, of course, I don't at all agree with their syntax and implementation. But this list shows the concepts and designs that I like and dislike in Rust.
