#### Example of discriminated union
```typescript

// One field decides which others exist; `data` cannot be read while loading
type ApiResponse =
  | { status: "loading" }
  | { status: "success"; data: User }
  | { status: "error"; error: string };
```