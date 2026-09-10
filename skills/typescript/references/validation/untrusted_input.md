#### Example of good boundary validation
```typescript

import { z } from "zod";

// Right: one schema is both the runtime check and the source of the type
const UserSchema = z.object({
  name: z.string(),
  email: z.string(),
});

type User = z.infer<typeof UserSchema>;

function parseUser(raw: unknown): User {
  return UserSchema.parse(raw); // Fails here, at the edge, not three frames later
}
```

#### Example of wrong boundary validation
```typescript

// Wrong: claims `item is User`, but only proves the keys exist.
// `{ name: 123, email: null }` passes, then `user.name.trim()` throws far away.
function isUserUnsound(item: unknown): item is User {
  return typeof item === "object" && item !== null && "name" in item && "email" in item;
}
```