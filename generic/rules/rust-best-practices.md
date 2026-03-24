---
id: rust-best-practices
title: Rust Best Practices & Code Hygiene
labels: [rust, code-hygiene, best-practices]
priority: 20
---

# Rust Best Practices & Code Hygiene

Compiled from: Rust API Guidelines, Rust Design Patterns book, Idiomatic Rust, Clippy lints.

## Naming Conventions (C-CASE, C-CONV, C-WORD-ORDER)

- **snake_case** for functions, methods, variables, modules, crates
- **CamelCase** for types, traits, enum variants
- **SCREAMING_SNAKE_CASE** for constants and statics
- **Conversion methods**: `as_` (cheap, borrowed), `to_` (expensive, owned), `into_` (consuming)
- **Iterators**: `iter()` (borrows), `iter_mut()` (mutable borrow), `into_iter()` (consumes)
- **Getters**: no `get_` prefix — `fn name(&self) -> &str`, not `fn get_name()`
- **Constructors**: `new()` as inherent static method, not standalone function
- **Boolean methods**: `is_`, `has_`, `can_` prefixes — `fn is_empty(&self) -> bool`

## Trait Implementation (C-COMMON-TRAITS)

Eagerly implement standard traits on all public types:
- `Debug` — always, required for error messages and test output
- `Clone` — unless the type represents a unique resource
- `Default` — when there's an obvious default state (derive when fields default to 0/empty)
- `PartialEq, Eq` — for value comparison
- `Display` — for human-facing output (Debug is for developers)
- `Send + Sync` — unless type contains non-thread-safe internals
- `Serialize, Deserialize` — for data types that cross boundaries

## Error Handling

### Do
- Return `Result<T, E>` from fallible functions, never panic in libraries
- Use `thiserror` for library error types, `anyhow` for application errors
- Provide context: `map_err(|e| format!("failed to load {}: {}", path, e))`
- Use `?` operator, never `try!` macro
- Make error types meaningful: `enum ConfigError { NotFound(String), ParseFailed { key: String, source: ... } }`

### Don't
- `unwrap()` in library code — only at top-level binary entry points
- `expect()` without a descriptive message — `expect("config file must exist")` is OK
- Panic for recoverable errors
- Use `String` as error type — too opaque

## Ownership & Borrowing

### Positive Patterns
- **Borrow by default**: `fn process(data: &[u8])` not `fn process(data: Vec<u8>)`
- **Accept borrowed types for arguments**: `fn search(name: &str)` not `fn search(name: String)`
- **Return owned types**: `fn generate() -> String` not `fn generate() -> &str` (unless lifetime is clear)
- **Use `Cow<str>`** when sometimes borrowing, sometimes owning
- **Temporary mutability**: `let mut v = compute(); v.sort(); let v = v;` — rebind as immutable

### Anti-Patterns
- **Clone to satisfy borrow checker**: If you're cloning to avoid a borrow error, rethink the data flow
- **Excessive `Rc<RefCell<T>>`**: Usually indicates wrong ownership model — restructure instead
- **Holding locks across conditional blocks**: `if let Ok(guard) = mutex.lock()` risks deadlock

## API Design

### Do
- **Builder pattern** for complex construction: `Config::builder().port(8080).timeout(30).build()`
- **Newtype pattern** for type safety: `struct TileId(u32)` not bare `u32`
- **Generic where useful**: `fn read<R: Read>(reader: R)` not `fn read(reader: File)`
- **Object-safe traits** when trait objects are expected
- **Expose intermediate results**: `fn parse(&self) -> Parsed` then `fn execute(parsed: Parsed)`
- **Caller decides allocation**: return iterators, not collected vecs when possible

### Don't
- **Out-parameters**: `fn parse(input: &str) -> Result<T>` not `fn parse(input: &str, out: &mut T)`
- **Boolean flags**: `fn connect(secure: bool)` → use `enum Protocol { Http, Https }`
- **Deref polymorphism**: only smart pointers should impl Deref
- **Overloaded operators** that surprise: `Add` should only mean mathematical addition

## Struct Design

- **Private fields by default** — expose via methods, allows future changes
- **Derive common traits**: `#[derive(Debug, Clone, PartialEq)]` on data types
- **Use `Default` derive** when all fields have natural defaults
- **Non-exhaustive enums**: `#[non_exhaustive]` for public enums that may grow
- **Builder for complex structs**: more than 4 fields → builder pattern

## Module Organization

- **Prefer small crates** over large monoliths — faster compilation, clearer deps
- **Contain unsafe in small modules** — isolate unsafe behind safe interfaces
- **Re-export important types** from crate root for ergonomic imports
- **One concern per module** — if a file exceeds ~500 lines, split it

## Common Gotchas

1. **`#[deny(warnings)]` is an anti-pattern** — breaks on new compiler versions. Use `#[warn(...)]` or CI clippy instead
2. **`if let` with `Mutex`** — the lock is held for the entire `if let` block, not just the condition
3. **String concatenation** — use `format!()` or `write!()`, not `+` chains
4. **Iterator vs loop** — prefer `iter().map().filter().collect()` over manual `for` loops with `push`
5. **`mem::replace` and `mem::take`** — use instead of `Option` dance (`take` replaces with Default)
6. **Shadowing for type conversion**: `let x: u32 = x.parse()?;` — rebind with new type
7. **`todo!()` over empty implementations** — marks incomplete code that compiles
8. **`unreachable!()` over `panic!("impossible")`** — semantic clarity

## Clippy Enforcement

Always run with `-D warnings`:
```
cargo clippy --workspace -- -D warnings
```

Key lint categories:
- `clippy::derivable_impls` — use derive instead of manual Default
- `clippy::needless_range_loop` — use iterators instead of index loops
- `clippy::manual_is_multiple_of` — use `.is_multiple_of()` not `% == 0`
- `clippy::type_complexity` — extract type aliases for complex types
- `clippy::too_many_arguments` — refactor into builder or config struct

## Testing

- **Unit tests in the same file**: `#[cfg(test)] mod tests { use super::*; ... }`
- **Integration tests in `tests/`**: separate files, test public API only
- **Doc tests**: `///` examples are compiled and run — keep them working
- **Proptest/quickcheck** for property-based testing on numeric/algorithmic code
- **`#[should_panic(expected = "...")]`** for panic tests — include the expected message

## Performance

- **Measure before optimizing** — `cargo bench`, `criterion`, `flamegraph`
- **Release mode for benchmarks** — debug builds are 10-30x slower
- **`#[inline]`** only when profiling shows it matters — compiler is usually right
- **Avoid allocations in hot paths** — reuse buffers, use `&str` over `String`
- **`Vec::with_capacity`** when size is known — avoids reallocation

## References

- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- [Rust Design Patterns](https://rust-unofficial.github.io/patterns/)
- [Idiomatic Rust](https://github.com/mre/idiomatic-rust)
- [Clippy Lints](https://rust-lang.github.io/rust-clippy/)
