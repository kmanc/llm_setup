---
name: typescript
description: Use this skill when the user asks to write, review, refactor, refine, or organize Typescript code, modules, scripts, projects, or applications.
---

## Config
- Target `ESNext`
- Turn on `strict` mode, then add the checks it does not cover
  - `noUncheckedIndexedAccess` — `arr[i]` becomes `T | undefined`, because the index may not be there
  - `exactOptionalPropertyTypes` — `foo?: string` stops silently accepting an explicit `undefined`
  - `noImplicitOverride` — `override` must be written, so renaming a base method breaks loudly
  - `noFallthroughCasesInSwitch` — a missing `break` is a typo far more often than it is intent
  - `verbatimModuleSyntax` — imports erase predictably; pairs with `import type`
  - Load [tsconfig.md](references/strict/tsconfig.md) for examples of configs
- Turn a check off only for a specific file with a specific reason, never repo-wide, and only after getting explicit approval from me


## Types
- Use `satisfies` to check a value against a type without widening it — an `as` assertion accepts a wrong shape silently, and a plain annotation discards what the compiler had inferred
  - Load [satisfies.md](references/types/satisfies.md) for examples of using `satisfies`
- Avoid `any`: it disables checking for every expression it touches downstream. When a value is genuinely untyped, take `unknown` and narrow it
  - Load [unknown.md](references/types/unknown.md) for examples of using `unknown` 
- Use `as const` to derive a type from the data instead of maintaining both by hand
  - Load [as_const.md](references/types/as_const.md) for examples of using `as const`
- Use discriminated unions over optional fields because optional fields leave illegal combinations representable
  - Load [discriminated_unions.md](references/types/discriminated_unions.md) for examples of using discriminated unions


## Validate at the boundary
- Parse untrusted input — network responses, files, env vars, anything out of `JSON.parse`, etc — with a schema. A type assertion on unvalidated data is a lie the compiler then propagates everywhere
  - Load [untrusted_input.md](references/validation/untrusted_input.md) for examples of validating data
- A hand-written type predicate must check what it claims: `"name" in item` does not prove `item.name` is a `string`
- Derive the type from the schema so the runtime check and the static type cannot drift apart
- Reserve hand-written predicates for narrowing types you already own


## Errors
- Return a `Result` for outcomes the caller is expected to handle — the type forces them to
  - Load [results.md](references/errors/results.md) for examples of using results
- `throw` for bugs and unrecoverable states; making a caller handle what it cannot fix is noise
- Give errors a type. A `catch` that receives `unknown` and stringifies it loses everything the thrower knew
  - Load [typed.md](references/errors/typed.md) for examples of typed errors
- Attach the context the caller needs to act: which field, which code, which file

## Async
- Never leave a promise floating. An unhandled rejection crashes the process on the server and vanishes silently in the browser. `await` it, `return` it, or attach a `.catch`
- Independent work belongs in `Promise.all`; a sequential `await` loop is right only when each step feeds the next
  - Load [concurrent.md](references/async/concurrent.md) for examples of concurrent functions
  - Load [sequential.md](references/async/sequential.md) for examples of sequential functions
- Use `Promise.allSettled` when one failure must not cancel the rest
- Never pass an `async` function to `new Promise`. A throw inside it rejects a promise nobody holds

## Null and Undefined
- Use `??`, not `||` — `||` treats `0`, `""`, and `false` as absent and substitutes the default
  - Load [null.md](references/existence/null.md) for examples of using `??`
- Use `?.` to reach through a value that is legitimately optional, not to paper over one that should never be null
  - Load [undefined.md](references/existence/undefined.md) for examples of using `?`
- Under `noUncheckedIndexedAccess`, every index read is `T | undefined`; check it rather than asserting it away with `!`

## Safety Patterns
- Make the compiler prove a switch is complete, so adding a variant breaks the build instead of falling through
  - Load [switches.md](references/safety/switches.md) for examples of exhaustive switches
- Prefer immutability: `readonly` fields and `readonly T[]` parameters document that a function does not mutate its input, and the compiler holds you to it
  - Load [immutability.md](references/safety/immutability.md) for examples of `readyonly` fields
- Avoid magic numbers. A named constant explains the value once, in the place someone will look for it


## Modules
- Prefer named exports to default exports — a default is renamed by hand at every import site, and rename refactors cannot follow it
- Use `import type` for type-only imports so the import disappears at runtime and cannot create a cycle
- Avoid circular imports; if two modules need each other, the shared piece belongs in a third
- Organize by domain, not by file category — a directory owns a concept, not a kind of file
  - Load [layout.md](references/structure/layout.md) for examples of project layout


## Tooling
- Code must pass `bunx tsc --noEmit` with zero errors
- Code must pass `eslint .` with zero warnings and `prettier --check .` clean
- Use `@ts-expect-error` with a reason on the line above, never `@ts-ignore` — `@ts-expect-error` fails once the error is gone, so a stale suppression cannot survive
- Suppressions must be specific, justified, and approved by me
  - Never put a bare `/* eslint-disable */` at the top of a file

## Tests
- Use `bun test`; assert on behavior through the public surface, not on internals
- Use `test.each` instead of copy-pasting a test body per case
- Do not mock what you can construct directly — a real object exercises the real types
- Test the boundary parsers against malformed input, which is the input they exist for

## Documentation
- When refactoring existing code, update code comments to ensure the comments are still accurate
- Also update any documentation (often a CONTEXT.md and/or README.md) to keep it up-to-date with the code

**Remember**: A type is a claim, and only validation at the boundary makes it true. Inside that boundary, let the compiler carry the proof.
