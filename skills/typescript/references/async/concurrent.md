#### Example of when to use concurrent
```typescript

// Concurrent: independent calls overlap, so the total cost is the cost of the slowest one
async function getOrders(id: string): Promise<Dashboard> {
  const [user, orders] = await Promise.all([fetchUser(id), fetchOrders(id)]);
  return { user, orders };
}
```