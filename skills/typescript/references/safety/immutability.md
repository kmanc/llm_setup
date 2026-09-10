#### Example of immutability
```typescript

interface Account {
  readonly id: string;
  readonly tags: readonly string[];
}

// Cannot accidentally sort in place: `sort` does not exist on a readonly array
function tagCount(account: Account): number {
  return account.tags.length;
}
```