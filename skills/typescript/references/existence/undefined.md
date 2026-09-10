#### Example of undefined handling
```typescript

function firstTrimmed(items: string[]): string | undefined {
  const first = items[0]; // string | undefined — the array may be empty
  return first?.trim();
}
```