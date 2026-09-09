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
- Libraries return a concrete error enum built with `thiserror` — callers need to match on variants, and `anyhow::Error` erases them
- Binaries and tests use `anyhow` with `.context()` — nobody matches on the error out of `main`
- Add context at each layer; a bare `?` propagates the error but loses the trail
- Prefer `?` over `match` for propagation
- Never `unwrap()` on anything reachable at runtime. In tests, and after a check the type system cannot see, `expect("why this holds")` is fine — the message states the invariant

#### Example library error type
```rust
use std::path::PathBuf;
use thiserror::Error;

#[derive(Debug, Error)]
pub enum ConfigError {
    #[error("cannot read config at {path}")]
    Read {
        path: PathBuf,
        #[source]
        source: std::io::Error,
    },

    #[error("invalid TOML in {path}")]
    Parse {
        path: PathBuf,
        #[source]
        source: toml::de::Error,
    },
}
```

#### Example binary error handling
```rust
use anyhow::{Context, Result};

fn load_config(path: &str) -> Result<Config> {
    let content = std::fs::read_to_string(path)
        .with_context(|| format!("failed to read config from {path}"))?;
    let config: Config = toml::from_str(&content)
        .with_context(|| format!("failed to parse config from {path}"))?;
    Ok(config)
}
```

## Iterators
- Prefer iterator chains over manual loops — declarative, lazy, composable
- Reach for a plain `for` loop when the body needs early exit, `?`, or side effects; a chain contorted around those is worse than the loop

#### Example iterator chain
```rust
let active_emails: Vec<String> = users.iter()
    .filter(|u| u.is_active)
    .map(|u| u.email.clone())
    .collect();
```

## Concurrency
- Prefer scoped threads from `std::thread` over `tokio` where possible
- Use channels for communicating between threads

#### Example scoped threads
```rust
use std::thread;

// Scoped threads borrow local data; the scope cannot exit until both finish.
fn main() {
    let mut a = vec![1, 2, 3];
    let mut x = 0;
    thread::scope(|s| {
        s.spawn(|| {
            dbg!(&a);
        });
        s.spawn(|| {
            x += a[0] + a[2];
        });
    });
    a.push(4);
    // At this point, the value of x and the length of a should be the same
}
```

## Unsafe
- Default to safe Rust; `unsafe` is a last resort, not a shortcut
- Every `unsafe` block carries a `// SAFETY:` comment naming the invariant that makes it sound
- An `unsafe fn` documents its preconditions under a `# Safety` doc heading

Never reach for `unsafe` to:
- bypass the borrow checker
- transmute between unrelated types
- skip a bounds check that has not been shown to matter
- gain convenience

## Project Layout
- Organize by domain, not by type — a module owns a concept, not a category of file

#### Example layout
```text
my_app/
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── auth/          # Domain module
│   │   ├── mod.rs
│   │   ├── token.rs
│   │   └── middleware.rs
│   ├── orders/        # Domain module
│   │   ├── mod.rs
│   │   ├── model.rs
│   │   └── service.rs
│   └── db/            # Infrastructure
│       ├── mod.rs
│       └── pool.rs
├── tests/             # Integration tests
├── benches/           # Benchmarks
└── Cargo.toml
```

## Tooling
- All code must compile without errors
- All code must pass `cargo clippy` with zero warnings
- All code must pass `cargo fmt --check`
- Suppressions must be specific and justified — `#[allow(clippy::too_many_arguments)] // FFI signature is fixed`, never a blanket `#![allow(warnings)]`, `#[allow(dead_code)]`, or `#[allow(unused_variables)]` to cheat the warning
- `todo!()` is allowed for work-in-progress code, stubs, or other indicators of future work — give its still-unused parameters an underscore prefix (`_user`) rather than reaching for `#[allow(unused_variables)]`

## Tests
- Unit tests live in a `#[cfg(test)] mod tests` beside the code; integration tests in `tests/` exercise the public API only
- Assert on behavior, not on internals
- `#[should_panic]` needs `expected = "..."` — without it the test passes on the wrong panic
- Put doctests on public items; they are compiled, so they cannot rot
- Reach for `proptest` when the input space is wider than the cases worth writing by hand

## Documentation
- When refactoring existing code, take care to update code comments to ensure the comments are still accurate
- Also remember to update any documentation (often a CONTEXT.md and/or README.md) to keep it up-to-date with the code

**Remember**: Push invariants into the type system so the compiler checks them for you and test the behavior that types cannot express.
