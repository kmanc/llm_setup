#### Example newtype
```rust

use anyhow::Result;

// Distinct types prevent mixing up arguments
struct UserId(u64);
struct OrderId(u64);

#[derive(Debug)]
struct Order {
    id: u64,
    owner: u64,
}


// `get_order(order, user)` is a compile error at the call site
fn get_order(user: UserId, order: OrderId) -> Result<Order> {
    Ok(Order {
        id: order.0,
        owner: user.0,
    })
}
```