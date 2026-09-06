---
name: rust
description: Write clean, consistent, and performant Rust code. Use this skill when the user asks to design crate structure or module layout, or write, review, refactor, or refine Rust code, scripts, projects, or applications. Generates self-documenting, polished code based on the following best practices.
---

This skill guides creation of clean, performant, efficient, and maintainable Rust code.

## Structs
- Construct via `new()` (or a named constructor) so invariants are established in one place
- Prefer private fields exposed through named accessors — the accessor matches the field name exactly (field `foo` → `fn foo(&self)`)
- Exception: plain data aggregates — config structs, DTOs, `#[derive(Deserialize)]` payloads — may expose fields directly. An accessor that only returns `&self.x` is noise, and serde needs the fields visible anyway. The rule exists to protect invariants; a struct with none has nothing to protect
- Return borrows, not clones: `&str` over `&String`, `&[T]` over `Vec<T>`, `Option<&T>` over `&Option<T>`. Hand out `Arc::clone` only when the caller must keep the data alive independently
- Store `Arc<[T]>` rather than `Vec<T>` for shared immutable data — cloning is a refcount bump, not a copy

#### Example struct
```rust
use std::sync::Arc;

pub struct Account {
    email: String,
    scores: Arc<[u64]>,
    nickname: Option<String>,
}

impl Account {
    pub fn new(email: String, scores: Vec<u64>, nickname: Option<String>) -> Self {
        Self { email, scores: Arc::from(scores), nickname }
    }

    pub fn email(&self) -> &str {
        &self.email
    }

    // Borrow for reading; hand out `Arc::clone(&self.scores)` only when the
    // caller needs to keep the data alive independently.
    pub fn scores(&self) -> &[u64] {
        &self.scores
    }

    pub fn nickname(&self) -> Option<&str> {
        self.nickname.as_deref()
    }
}
```

## Modeling State
- Make illegal states unrepresentable — what the compiler rejects, no test has to catch
- Use a marker type with `PhantomData` when the *API surface* changes between states: the methods that do not apply simply do not exist
- Use an enum when the *data* changes between states — each variant carries only the fields that state actually has
- Transitions that consume `self` cannot be applied twice

#### Example marker-type state machine
```rust
use std::marker::PhantomData;

struct Grounded;
struct Launched;

struct Rocket<Stage = Grounded> {
    fuel_kg: f64,
    stage: PhantomData<Stage>,
}

impl Rocket<Grounded> {
    fn new(fuel_kg: f64) -> Self {
        Self { fuel_kg, stage: PhantomData }
    }

    // Takes `self` by value: a grounded rocket cannot be launched twice,
    // and `accelerate` does not exist until it has been.
    fn launch(self) -> Rocket<Launched> {
        Rocket { fuel_kg: self.fuel_kg, stage: PhantomData }
    }
}

impl Rocket<Launched> {
    fn accelerate(&mut self) {
        self.fuel_kg -= 1.0;
    }
}

impl<Stage> Rocket<Stage> {
    fn fuel_kg(&self) -> f64 {
        self.fuel_kg
    }
}
```

#### Example enum state
```rust
// Impossible states are unrepresentable
enum ConnectionState {
    Disconnected,
    Connecting { attempt: u32 },
    Connected { session_id: String },
    Failed { reason: String, retries: u32 },
}
```

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

## Ownership and Borrowing
- Accept generics, return concrete types — flexible for callers, and no type parameters leak into your public signatures
- Use `Cow` when a function usually borrows and only sometimes needs to own
- Use the newtype pattern to turn an argument mixup into a compile error

#### Example generic input, concrete output
```rust
use std::fmt::Display;
use std::io::Read;

fn read_all(reader: &mut impl Read) -> std::io::Result<Vec<u8>> {
    let mut buf = Vec::new();
    reader.read_to_end(&mut buf)?;
    Ok(buf)
}

// Trait bounds for multiple constraints
fn process<T: Display + Send + 'static>(item: T) -> String {
    format!("processed: {item}")
}
```

#### Example Cow
```rust
use std::borrow::Cow;

fn normalize(input: &str) -> Cow<'_, str> {
    if input.contains(' ') {
        Cow::Owned(input.replace(' ', "_"))
    } else {
        Cow::Borrowed(input) // Zero-cost when no mutation needed
    }
}
```

#### Example newtype
```rust
use anyhow::Result;

// Distinct types prevent mixing up arguments
struct UserId(u64);
struct OrderId(u64);
struct Order;

// `get_order(order, user)` is a compile error at the call site.
// Unused parameters in a `todo!()` stub take an underscore prefix, not an `#[allow]`.
fn get_order(_user: UserId, _order: OrderId) -> Result<Order> {
    todo!()
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

**Remember**: Push invariants into the type system so the compiler checks them for you — then test the behavior the types cannot express.
