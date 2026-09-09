#### Example struct with Arc<[T]> for multi threaded use
```rust
use std::sync::Arc;

pub struct A {
    b: String,
    c: Arc<[u64]>,
    d: Option<String>,
}

impl A {
    pub fn new(b: String, c: Vec<u64>, d: Option<String>) -> Self {
        Self { b, c: Arc::from(c), d }
    }

    pub fn b(&self) -> &str {
        &self.b
    }

    pub fn c(&self) -> Arc<[u64]> {
        Arc::clone(&self.c)
    }

    pub fn d(&self) -> Option<&str> {
        self.d.as_deref()
    }
}
```