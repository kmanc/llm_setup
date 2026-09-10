#### Example of when to use sequential
```typescript

// Sequential: multiple round trips end to end. Correct because each depends on the last
async function getOrders(id: string): Promise<Dashboard> {
  const user = await fetchUser(id);
  const orders = await fetchOrders(user);
  return { user, orders };
}

```