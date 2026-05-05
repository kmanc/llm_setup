---
name: rust
description: Write clean, consistent, and performant Rust code. Use this skill when the user asks to write or refine Rust code, scripts, projects, or applications. Generates self-documenting, polished code based on the following best practices.
license: There is no license; I wrote this for me but if you like it, feel free to use it.
---

This skill guides creation of clean, performant, production-grade Rust code.

The user provides code requirements: a project idea, an end goal, a list of inputs and outputs, a description of a function, or an entire existing codebase. They may include context about the purpose, architecture, running environment, or technical constraints.

# Rust

## Structs
- Always create structs via a `new()` method (or similarly named constructor)
- Fields must never be public; expose them only through named methods in an `impl` block
  - Accessor methods follow the field name exactly (e.g. field `foo` → method `fn foo(&self)`)
- Return references like `&str` (not `&String`), `Arc<[T]>` (not `Vec<T>`), and `Option<&T>` (not `&Option<T>`)

#### Example struct
```rust
pub struct A {
    b: String,
    c: Arc<[u64]>,
    d: Option<bool>
}

impl A {
    pub fn new(b: String, c: Arc<[u64]>, d: Option<bool>) -> Self {
        A {b, c, d}
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
```

## Collections
- Prefer `Arc<[T]>` over `Vec<T>` for storing collections of data

## Concurrency
- Prefer scoped threads from `std::thread` over `tokio` where possible

## Code Quality
- All code must compile without errors
- All code must pass `cargo clippy` with zero warnings
