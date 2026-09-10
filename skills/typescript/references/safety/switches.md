#### Example of exhaustive switches
```typescript

function handleStatus(status: "active" | "inactive" | "pending"): string {
  switch (status) {
    case "active":
      return "Active";
    case "inactive":
      return "Inactive";
    case "pending":
      return "Pending";
    default: {
      // Adding a fourth status makes this assignment a compile error
      const _exhaustive: never = status;
      throw new Error(`Unhandled status: ${String(_exhaustive)}`);
    }
  }
}
```