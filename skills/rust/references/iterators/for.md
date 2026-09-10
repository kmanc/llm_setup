#### Example iterator chain
```rust

use std::num::ParseIntError;

fn find_first_valid_number(items: &[&str]) -> Result<i32, ParseIntError> {
    for item in items {
        // Skip empty entries early with `continue`
        if item.is_empty() {
            continue;
        }

        // Exit the function early once we find a negative number
        if item.starts_with('-') {
            println!("Found a negative number, stopping search: {item}");
            break;
        }

        // Try parsing; `?` propagates the error immediately if parsing fails
        let number: i32 = item.parse()?;

        if number > 10 {
            // Early return with a successful result
            return Ok(number);
        }
    }

    // Fallback if no early return happened
    Ok(0)
}
```
