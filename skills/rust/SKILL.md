---
name: rust
description: Use this skill when the user asks to write, review, refactor, refine, or organize Rust code, crates, modules, scripts, projects, or applications.
---


## Types as a correctness mechanism
- Use the newtype pattern to turn an argument mixups into compile errors
  - Load [newtype.md](references/types/newtype.md) for examples of leveraging the type system to make argument ordering mistakes impossible


## Ownership and Borrowing
- Accept generics and return concrete types when possible
  - Load [generic_input_concrete_output.md](references/ownership/generic_input_concrete_output.md) for examples of functions that take generic inputs and return concrete outputs
- Use `Cow` when a function usually borrows and only sometimes needs to own
  - Load [cow.md](references/ownership/cow.md) for examples of functions that take generic inputs and return concrete outputs


## Structs
- Construct via `new()` (or a named constructor) so invariants are established in one place
- Prefer private fields exposed through named accessors — the accessor matches the field name exactly (field `foo` → `fn foo(&self)`)
  - Exception: plain data aggregates — config structs, DTOs, `#[derive(Deserialize)]` payloads — may expose fields directly. An accessor that only returns `&self.x` is noise, and serde needs the fields visible anyway. The rule exists to protect invariants; a struct with none has nothing to protect
- Return borrows, not clones: `&str` over `&String`, `&[T]` over `Vec<T>`, `Option<&T>` over `&Option<T>`
  - Load [single_thread.md](references/structs/single_thread.md) for examples of structs for general purpose use
- Store `Arc<[T]>` rather than `Vec<T>`, and return clones of them, for fields that will be shared across threads
  - Load [multi_thread.md](references/structs/multi_thread.md) for examples of structs for a multi-threaded application
- Use the consuming builder pattern for structs that have lots of fields, especially if they're optional
  - Load [consuming_builder.md](references/structs/consuming_builder.md) for examples of structs that uses a consuming builder


## Modeling State
- Make illegal states unrepresentable — what the compiler rejects, no test has to catch
- Use a marker type with `PhantomData` when the *API surface* changes between states: the methods that do not apply simply do not exist
  - Create transitions that consume `self` for cases where they cannot be applied twice
  - Load [state_machine.md](references/state/state_machine.md) for examples of structs whose API surface changes between states
- Use an enum when the *data* changes between states — each variant carries only the fields that state actually has
  - Load [data_variants.md](references/state/data_variants.md) for examples enums creation and use


## Errors
- Libraries return a concrete error enum built with `thiserror` so that callers can match on variants
  - Load [library.md](references/errors/library.md) for examples of errors built in libraries
- Binaries and tests use `anyhow` with `.context()` — nobody matches on the error out of `main`
  - Load [binary.md](references/errors/binary.md) for examples of errors built in binaries
- Add context at each layer; a bare `?` propagates the error but loses the trail
- Prefer `?` over `match` for propagation
- Never `unwrap()` on anything reachable at runtime. In tests, and after a check the type system cannot see, `expect("why this holds")` is fine because the message states the invariant


## Iterators
- Prefer iterator chains over manual loops
  - Load [chain.md](references/iterators/chain.md) for examples of using iterator chains
- Reach for a plain `for` loop when the body needs early exit, `?`, or side effects
  - Load [for.md](references/iterators/for.md) for examples of using for loops on iteators


## Concurrency
- Prefer scoped threads from `std::thread` over `tokio` where possible
  - Load [threads.md](references/concurrency/threads.md) for examples of how to write concurrent code
- Use channels for communicating between threads
  - Load [channels.md](references/concurrency/channels.md) for examples of how to send data between threads

## Unsafe
- Default to safe Rust; `unsafe` is a last resort, not a shortcut
  - Never reach for `unsafe` to:
    - bypass the borrow checker
    - transmute between unrelated types
    - skip a bounds check that has not been shown to matter
    - gain convenience
- Include a `// SAFETY:` comment for every `unsafe` block naming the invariant that makes it sound
- Documents `unsafe fn` preconditions under a `# Safety` doc heading


## Project Layout
- Organize by domain, not by type — a module owns a concept, not a category of file
  - Load [layout.md](references/structure/layout.md) for examples of how to structure a project


## Tooling
- All code must compile without errors
- All code must pass `cargo clippy` with zero warnings
- All code must pass `cargo fmt --check`
- Do not use suppressions without explicit approval
- `todo!()` is allowed for work-in-progress code, stubs, or other indicators of future work
  - Give its still-unused parameters an underscore prefix (`_user`) rather than `#[allow(unused_variables)]`


## Tests
- Put unit tests in a `#[cfg(test)] mod tests` beside the code; integration tests in `tests/` exercise the public API only
- Assert on behavior, not on internals
- `#[should_panic]` needs `expected = "..."` — without it the test passes on the wrong panic
- Put doctests on public items; they are compiled, so they cannot rot
- Use `proptest` when the input space is wider than the cases worth writing by hand


## Documentation
- When refactoring existing code, update related code comments to reflect the new reality
- Also update any related documentation (often a CONTEXT.md and/or README.md) to keep it up-to-date with the code

**Remember**: Push invariants into the type system so the compiler checks them for you and test the behavior that types cannot express.
