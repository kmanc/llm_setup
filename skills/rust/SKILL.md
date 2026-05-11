---
name: rust
description: Write clean, consistent, and performant Rust code. Use this skill when the user asks to write or refine Rust code, scripts, projects, or applications. Generates self-documenting, polished code based on the following best practices.
---

This skill guides creation of clean, performant, efficient, and maintainable Rust code.

## When to Activate

- Writing new Rust code
- Reviewing Rust code
- Refactoring existing Rust code
- Designing crate structure and module layout

## Structs
- Always create structs via a `new()` method (or similarly named constructor)
- Fields must never be public; expose them only through named methods in an `impl` block
  - Accessor methods follow the field name exactly (e.g. field `foo` → method `fn foo(&self)`)
- Return references like `&str` (not `&String`), `Arc<[T]>` (not `Vec<T>`), and `Option<&T>` (not `&Option<T>`)
- Use `PhantomData` to design structs that cannot be misused

#### Example structs
```rust
// Generic demonstration of constructor method, private fields, public methods, and returning references
pub struct A {
    b: String,
    c: Arc<[u64]>,
    d: Option<bool>
}

impl A {
    pub fn new(b: String, c: Vec<[u64]>, d: Option<bool>) -> Self {
        Self {
            b,
            c: Arc::from(c),
            d,
        }
    }

    pub fn b(&self) -> &str {
        &self.b
    }

    pub fn c(&self) -> Arc<[u64]> {
        Arc::clone(&self.c)
    }

    pub fn d(&self) -> Option<&bool> {
        self.d.as_ref()
    }
}

// Rocket example from "Rust for Rustaceans" demonstrating PhantomData
struct Grounded;
struct Launched;

struct Rocket<Stage = Grounded> {
    stage: std::marker::PhantomData<Stage>,
}

impl Default for Ricjet<Grounded> {}

impl Rocket<Grounded> {
    pub fn launch(self) -> Rocket<Launched> { }
}

impl Rocket<Launched> {
    pub fn accelerate(&mut self) { }
    pub fn decelerate(&mut self) { }
}

impl<Stage> Rocket<Stage> {
    pub fn color(&self)-> Color { }
    pub fn weight(&self)-> Kilograms { }
}
```

## When Not a Marker, Model States as Enums

```rust
// Impossible states are unrepresentable
enum ConnectionState {
    Disconnected,
    Connecting { attempt: u32 },
    Connected { session_id: String },
    Failed { reason: String, retries: u32 },
}
```

## Concurrency
- Prefer scoped threads from `std::thread` over `tokio` where possible

## Code Quality
- All code must compile without errors
- All code must pass `cargo clippy` with zero warnings

## Use `Cow` for Flexible Ownership

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

## Use `Result` and `?` — Never `unwrap()` in Production

```rust
// Propagate errors with context
use anyhow::{Context, Result};

fn load_config(path: &str) -> Result<Config> {
    let content = std::fs::read_to_string(path)
        .with_context(|| format!("failed to read config from {path}"))?;
    let config: Config = toml::from_str(&content)
        .with_context(|| format!("failed to parse config from {path}"))?;
    Ok(config)
}
```

## Accept Generics, Return Concrete Types

```rust
// Generic input, concrete output
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

## Newtype Pattern for Type Safety

```rust
// Distinct types prevent mixing up arguments
struct UserId(u64);
struct OrderId(u64);

fn get_order(user: UserId, order: OrderId) -> Result<Order> {
    // Can't accidentally swap user and order IDs
    todo!()
}
```

## Prefer Iterator Chains Over Manual Loops

```rust
// Declarative, lazy, composable
let active_emails: Vec<String> = users.iter()
    .filter(|u| u.is_active)
    .map(|u| u.email.clone())
    .collect();
```

## When Unsafe Is NOT Acceptable

```rust
// Bad: Using unsafe to bypass borrow checker
// Bad: Using unsafe for convenience
// Bad: Using unsafe without a Safety comment
// Bad: Transmuting between unrelated types
```

## Organize by Domain, Not by Type

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

**Remember**: If it compiles, it's probably correct — but only if you avoid `unwrap()`, minimize `unsafe`, and let the type system work for you.