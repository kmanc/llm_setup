#### Example struct for with &[T] for single threaded use
```rust

pub struct A {
    b: String,
    c: Vec<[u64]>,
    d: Option<String>,
}

impl Account {
    pub fn new(b: String, c: Vec<u64>, d: Option<String>) -> Self {
        Self { b, c, d }
    }

    pub fn b(&self) -> &str {
        &self.b
    }

    pub fn c(&self) -> &[u64] {
        &self.c
    }

    pub fn d(&self) -> Option<&str> {
        self.d.as_deref()
    }
}
```