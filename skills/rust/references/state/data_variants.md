#### Example enum state
```rust

struct ConnectionAttempt(u32);
struct SessionId(String);
struct FailureReason(String);
struct RetryAttempt(u32);

// Impossible states are unrepresentable
enum ConnectionState {
    Disconnected,
    Connecting { attempt: ConnectionAttempt },
    Connected { session_id: SessionId },
    Failed { reason: FailureReason, retries: RetryAttempt },
}
```