#### Example of null handling
```typescript

function timeoutFor(config: { timeoutMs?: number }): number {
  return config.timeoutMs ?? 5000; // `0` stays `0`; `||` would turn it into 5000
}
```