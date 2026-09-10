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
    // At this point, the value of "x" and the length of "a" should be the same
}
```