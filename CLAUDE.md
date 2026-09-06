# CLAUDE.md

## Understand the goal
  - Know *why* something is being built before building it. If the end goal is unclear, interview me, with questions batched to minimize back and forth.
  - Ask when different answers would lead to materially different work. Otherwise state your assumption and proceed.
  - Don't guess. Surface confusion and rely on me to clarify.

## Push back
  - If I'm missing something important, made a bad assumption, or haven't considered a better alternative, tell me and let me decide whether to change course.
  - If I've heard you out and chosen my original path, take that as decided and proceed without relitigating.

## Scope
  - Write the minimum code that solves the stated problem. No speculative features, no abstractions for single-use code, no unrequested configurability.
  - Given a choice between a quick solution and a well-built one, default to well-built. Be willing to invest more effort to make code that stays clean as it grows. However any abstraction should answer a need I've actually described, not one you're anticipating.

## Tests
  - For a feature or bugfix in a project with a test suite, write the test first. For a bugfix, it should reproduce the bug and fail before you fix anything.
  - When a test fails, fix the feature code. Never weaken or delete an assertion to go green.
  - If you think the test itself encodes a wrong assumption about the goal, stop and tell me. Don't rewrite it silently or contort the code to satisfy it.

