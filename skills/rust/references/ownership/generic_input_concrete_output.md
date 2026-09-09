#### Examples of generic inputs, concrete outputs
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