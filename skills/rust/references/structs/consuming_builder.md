#### Example struct with consuming builder pattern for optional / many field structs
```rust

pub struct A {
    b: String,
    c: String,
    d: Option<String>,
    e: Option<String>,
    f: Option<String>,
    g: Option<String>,
}

impl A {
    pub fn new(b: String, c: String) -> Self {
        Self { 
            b,
            c,
            d: None,
            e: None,
            f: None,
            g: None,
        }
    }

    pub fn with_d(mut self, d: Option<String>) -> Self {
        self.d = d
        self
    }

    pub fn with_e(mut self, e: Option<String>) -> Self {
        self.e = e
        self
    }

    pub fn with_d(mut self, f: Option<String>) -> Self {
        self.f = f
        self
    }

    pub fn with_d(mut self, g: Option<String>) -> Self {
        self.g = g
        self
    }

    pub fn b(&self) -> &str {
        &self.b
    }

    pub fn c(&self) -> &str {
        &self.c
    }

    pub fn d(&self) -> Option<&str> {
        self.d.as_deref()
    }

    pub fn e(&self) -> Option<&str> {
        self.e.as_deref()
    }

    pub fn f(&self) -> Option<&str> {
        self.f.as_deref()
    }

    pub fn g(&self) -> Option<&str> {
        self.g.as_deref()
    }
}
```