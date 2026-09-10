#### Example iterator chain
```rust

let active_emails: Vec<String> = users.iter()
    .filter(|u| u.is_active())
    .map(|u| u.email().to_string())
    .collect();
```