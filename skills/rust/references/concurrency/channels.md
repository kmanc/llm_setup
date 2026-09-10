#### Example scoped threads
```rust

use std::sync::mpsc;
use std::thread;
use std::time::Duration;

fn main() {
    // Create a channel: tx = sender, rx = receiver
    let (tx, rx) = mpsc::channel();

    // Spawn several producer threads, each cloning the sender
    for id in 0..3 {
        let tx = tx.clone();
        thread::spawn(move || {
            for i in 0..3 {
                let message = format!("Thread {id} says {i}");
                if let Err(e) = tx.send(message) {
                    eprintln!("Thread {id} failed to send: {e}");
                    break;
                }

                thread::sleep(Duration::from_millis(50));
            }
        });
    }

    // Drop the original sender so the channel closes once all
    // clones (owned by the spawned threads) are dropped.
    drop(tx);

    // Receive messages on the main thread until all senders are gone.
    loop {
        match rx.recv() {
            Ok(received) => println!("Got: {received}"),
            Err(_) => {
                // All senders have been dropped; channel is closed.
                break;
            }
        }
    }

    println!("All threads finished, channel closed.");
}
```