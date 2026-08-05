---
name: typescript
description: Write clean, consistent, and performant Typescript code. Use this skill when the user asks to write or refine Typescript code, scripts, projects, or applications. Generates self-documenting, polished code based on the following best practices.
---

This skill guides creation of clean, performant, efficient, and maintainable Typescript code.

## When to Activate

- Writing new Typescript code
- Reviewing Typescript code
- Refactoring existing Typescript code
- Architecting and organizing a Typescript project

## Strict mode
- Enable Strict Mode with All Checks
 
#### Example tsconfig.json
```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitOverride": true,
    "target": "ES2026"
  }
}
```

## Types
- Use satisfies instead of type assertions
- Avoid any as best as possible. If required, use unknown instead of any
- Use discriminated unions over optional fields
- Use type predicates over type assertions
- Use the type system for error handling

#### Example of satisfies
```typescript
const config = {
  port: 3000,
  host: "localhost",
} satisfies Config;

config.port.toFixed(); // TypeScript knows port is number
```

#### Example of unknown usage
```typescript
function parseJson(text: string): unknown {
  return JSON.parse(text);
}

const data = parseJson('{"name": "test"}');
if (isUser(data)) {
  data.name; // Safe - type narrowed
}
```

#### Example of discriminated union
```typescript
type ApiResponse =
  | { status: "loading" }
  | { status: "success"; data: User }
  | { status: "error"; error: string };
```

#### Example of type predicates
```typescript
function isUser(item: unknown): item is User {
  return typeof item === "object" && item !== null && "name" in item && "email" in item;
}

function processItem(item: unknown) {
  if (isUser(item)) {
    console.log(item.name); // Safe - narrowed to User
  }
}
```

#### Example of typed errors
```typescript
// Result type for recoverable errors
type Result<T, E = Error> =
  | { success: true; value: T }
  | { success: false; error: E };

// Typed error classes
class ValidationError extends Error {
  constructor(
    message: string,
    readonly field: string,
    readonly code: string
  ) {
    super(message);
    this.name = "ValidationError";
  }
}

// Function with Result return type
function parseConfig(input: string): Result<Config, ValidationError> {
  try {
    const data = JSON.parse(input);
    if (!isValidConfig(data)) {
      return {
        success: false,
        error: new ValidationError("Invalid config", "root", "INVALID_FORMAT"),
      };
    }
    return { success: true, value: data };
  } catch {
    return {
      success: false,
      error: new ValidationError("Parse failed", "root", "PARSE_ERROR"),
    };
  }
}
```

## Safety patterns
- Use exhaustive switches with never
- Variables with specific sets of valid values should be forced correct with types
- Prefer immutability where possible
- Avoid magic numbers. Define variables with explicit, easily understood names in configs or global constants instead

#### Example of exhaustive switches
```typescript
function handleStatus(status: "active" | "inactive" | "pending") {
  switch (status) {
    case "active":
      return "Active";
    case "inactive":
      return "Inactive";
    case "pending":
      return "Pending";
    default: {
      const _exhaustive: never = status;
      throw new Error(`Unhandled status: ${_exhaustive}`);
    }
  }
}
```

#### Example of type-enforced safety
```typescript
type MedalPlaces = 'First' | 'Second' | 'Third';
```

## Use `Cow` for Flexible Ownership

```rust
use std::borrow::Cow;

fn normalize(input: &str) -> Cow<'_, str> {
    if input.contains(' ') {
        Cow::Owned(input.replace(' ', "_"))
    } else {
        Cow::Borrowed(input) // Zero-cost when no mutation needed
    }
}
```

## Design patterns
- Structure code for readability and maintainability
- Tests are as important as the feature itself
- Name imports

## Code Quality
- For code to be considered "done", errors and warnings should not exist

## Documentation
- When refactoring existing code, take care to update code comments to ensure the comments are still accurate
- Also remember to update any documentation (often a CONTEXT.md and/or README.md) to keep it up-to-date with the code
