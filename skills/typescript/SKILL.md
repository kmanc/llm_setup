---
name: typescript
description: Write clean, consistent, and performant TypeScript code. Use this skill when the user asks to design TypeScript modules or projects, or write, review, refactor, or refine TypeScript code, scripts, projects, or applications. Generates self-documenting, polished code based on the following best practices.
---

This skill guides creation of clean, performant, efficient, and maintainable TypeScript code.

## Strict Mode
- Turn on `strict`, then add the checks it does not cover — `strict` is a starting point, not the ceiling
- Every flag below converts a class of runtime bug into a compile error; that trade is always worth it
- Turn a check off only for a specific file with a specific reason, never repo-wide

#### Example tsconfig.json
```json
{
  "compilerOptions": {
    "target": "ESNext",
    "lib": ["ESNext", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "noEmit": true,

    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitOverride": true,
    "noFallthroughCasesInSwitch": true,
    "verbatimModuleSyntax": true
  }
}
```

- `noUncheckedIndexedAccess` — `arr[i]` becomes `T | undefined`, because the index may not be there
- `exactOptionalPropertyTypes` — `foo?: string` stops silently accepting an explicit `undefined`
- `noImplicitOverride` — `override` must be written, so renaming a base method breaks loudly
- `noFallthroughCasesInSwitch` — a missing `break` is a typo far more often than it is intent
- `verbatimModuleSyntax` — imports erase predictably; pairs with `import type`

## Types
- Use `satisfies` to check a value against a type without widening it — an `as` assertion accepts a wrong shape silently, and a plain annotation discards what the compiler had inferred
- Avoid `any`: it disables checking for every expression it touches downstream. When a value is genuinely untyped, take `unknown` and narrow it
- Use `as const` to derive a type from the data instead of maintaining both by hand
- Use discriminated unions over optional fields — optional fields leave illegal combinations representable

#### Example of satisfies
```typescript
type Palette = Record<string, string | [number, number, number]>;

const palette = {
  red: [255, 0, 0],
  green: "#00ff00",
} satisfies Palette;

palette.red[0];              // number — known to be the tuple, not the union
palette.green.toUpperCase(); // string — an annotation would have lost this
```

#### Example of unknown usage
```typescript
function parseJson(text: string): unknown {
  return JSON.parse(text); // `any` here would infect every caller
}

const data = parseJson('{"name": "test"}');
if (isUser(data)) {
  data.name; // Safe - type narrowed
}
```

#### Example of as const
```typescript
const MEDAL_PLACES = ["first", "second", "third"] as const;

type MedalPlace = (typeof MEDAL_PLACES)[number]; // "first" | "second" | "third"

// One source of truth: the list you iterate and the type you check are the same
function isMedalPlace(value: string): value is MedalPlace {
  return MEDAL_PLACES.some((place) => place === value);
}
```

#### Example of discriminated union
```typescript
// One field decides which others exist; `data` cannot be read while loading
type ApiResponse =
  | { status: "loading" }
  | { status: "success"; data: User }
  | { status: "error"; error: string };
```

## Validate at the Boundary
- Parse untrusted input — network responses, files, env vars, anything out of `JSON.parse` — with a schema. A type assertion on unvalidated data is a lie the compiler then propagates everywhere
- A hand-written type predicate must check what it claims: `"name" in item` does not prove `item.name` is a `string`
- Derive the type from the schema so the runtime check and the static type cannot drift apart
- Reserve hand-written predicates for narrowing types you already own

#### Example of boundary validation
```typescript
import { z } from "zod";

// Wrong: claims `item is User`, but only proves the keys exist.
// `{ name: 123, email: null }` passes, then `user.name.trim()` throws far away.
function isUserUnsound(item: unknown): item is User {
  return typeof item === "object" && item !== null && "name" in item && "email" in item;
}

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

## Errors
- Return a `Result` for outcomes the caller is expected to handle — the type forces them to
- `throw` for bugs and unrecoverable states; making a caller handle what it cannot fix is noise
- Give errors a type. A `catch` that receives `unknown` and stringifies it loses everything the thrower knew
- Attach the context the caller needs to act: which field, which code, which file

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
    const data: unknown = JSON.parse(input);
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

## Async
- Never leave a promise floating — an unhandled rejection crashes the process on the server and vanishes silently in the browser. `await` it, `return` it, or attach a `.catch`
- Independent work belongs in `Promise.all`; a sequential `await` loop is right only when each step feeds the next
- Use `Promise.allSettled` when one failure must not cancel the rest
- Never pass an `async` function to `new Promise` — a throw inside it rejects a promise nobody holds

#### Example of concurrent vs sequential
```typescript
// Sequential: three round trips end to end. Correct only if each depends on the last.
async function loadSlowly(id: string): Promise<Dashboard> {
  const user = await fetchUser(id);
  const orders = await fetchOrders(id);
  return { user, orders };
}

// Concurrent: independent calls overlap, so the cost is the slowest one
async function load(id: string): Promise<Dashboard> {
  const [user, orders] = await Promise.all([fetchUser(id), fetchOrders(id)]);
  return { user, orders };
}
```

## Null and Undefined
- Use `??`, not `||` — `||` treats `0`, `""`, and `false` as absent and substitutes the default
- Use `?.` to reach through a value that is legitimately optional, not to paper over one that should never be null
- Under `noUncheckedIndexedAccess`, every index read is `T | undefined`; check it rather than asserting it away with `!`

#### Example of nullish handling
```typescript
function timeoutFor(config: { timeoutMs?: number }): number {
  return config.timeoutMs ?? 5000; // `0` stays `0`; `||` would turn it into 5000
}

function firstTrimmed(items: string[]): string | undefined {
  const first = items[0]; // string | undefined — the array may be empty
  return first?.trim();
}
```

## Safety Patterns
- Make the compiler prove a switch is complete, so adding a variant breaks the build instead of falling through
- Prefer immutability: `readonly` fields and `readonly T[]` parameters document that a function does not mutate its input, and the compiler holds you to it
- Avoid magic numbers. A named constant explains the value once, in the place someone will look for it

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

## Modules
- Prefer named exports to default exports — a default is renamed by hand at every import site, and rename refactors cannot follow it
- Use `import type` for type-only imports so the import disappears at runtime and cannot create a cycle
- Avoid circular imports; if two modules need each other, the shared piece belongs in a third
- Organize by domain, not by file category — a directory owns a concept, not a kind of file

#### Example layout
```text
src/
├── auth/            # Domain module
│   ├── index.ts     # Public surface of the module
│   ├── token.ts
│   └── session.ts
├── orders/          # Domain module
│   ├── index.ts
│   ├── model.ts
│   └── service.ts
└── db/              # Infrastructure
    └── client.ts
```

## Tooling
- Code must pass `bunx tsc --noEmit` with zero errors
- Code must pass `eslint .` with zero warnings and `prettier --check .` clean
- Use `@ts-expect-error` with a reason on the line above, never `@ts-ignore` — `@ts-expect-error` fails once the error is gone, so a stale suppression cannot survive
- Suppressions must be specific and justified — never a bare `/* eslint-disable */` at the top of a file

## Tests
- Use `bun test`; assert on behavior through the public surface, not on internals
- Use `test.each` instead of copy-pasting a test body per case
- Do not mock what you can construct directly — a real object exercises the real types
- Test the boundary parsers against malformed input, which is the input they exist for

## Documentation
- When refactoring existing code, take care to update code comments to ensure the comments are still accurate
- Also remember to update any documentation (often a CONTEXT.md and/or README.md) to keep it up-to-date with the code

**Remember**: A type is a claim, and only validation at the boundary makes it true. Inside that boundary, let the compiler carry the proof.
