---
name: python
description: Use this skill when the user asks to write, review, refactor, refine, or organize Python code, modules, scripts, packages, projects, or applications
---


## Type Hints
- Annotate every public function signature - parameters and return type - with hints
  - Load [hints.md](references/types/hints.md) for examples of type hints
- Use modern syntax: `list[str]`, `dict[str, int]`, `X | None` — not `List`, `Dict`, `Optional`
- Code should pass `mypy --strict` (or `pyright`) cleanly
- Avoid `Any`; when a value is genuinely untyped, take `object` and narrow it
- Use `Protocol` for structural typing instead of demanding a base class


## Errors
- Catch the narrowest exception that can actually be raised
- Never write a bare `except:` or `except Exception: pass` — an error you swallow is a bug you debug later without a traceback
- Use `raise ... from e` so the original cause survives
  - Load [raise.md](references/errors/raise.md) for examples of raising errors
- Define module-specific exception types; callers should catch your errors, not `ValueError`
- When skipping an error really is correct, say so explicitly with `contextlib.suppress`


## Data modeling
- Prefer `@dataclass(frozen=True, slots=True)` over passing dicts and tuples around
  - Load [class.md](references/data/class.md) for examples of dataclasses
- Use `Enum`/`StrEnum` for a fixed set of values — never bare string literals scattered through the code
  - Load [enum.md](references/data/enum.md) for examples of enums
- `NamedTuple` is fine for a small return value with an obvious order
- Never use a mutable default argument — `def f(items: list[str] = [])` shares one list across every call


## Dictionaries
- Use `d[key]` when the key is required — a `KeyError` at the point of the bug beats a `None` that fails three frames later
- Use `.get()` only when absence is a real, handled case, and pass an explicit default
- Dicts preserve insertion order (guaranteed since 3.7) — rely on it
- Reach for `OrderedDict` only when you need `move_to_end`, `popitem(last=False)`, or order-sensitive `==`
- Use `defaultdict` and `Counter` liberally

## File access
- Use `pathlib.Path`, not `os.path` string munging
- Use the `with open()` pattern when streaming a file
- For whole-file reads and writes, `Path.read_text()` / `Path.write_text()` is clearer

## Memory management
- Generators and lazy loading save RAM; take advantage of this where it makes sense
  - Load [generators.md](references/memory/generators.md) for examples of generators
- The tradeoff is single-pass: a generator has no `len()` and cannot be iterated twice. If you need either, build the list


## Import Conventions
- Import from stdlib first, then third-party, then local
  - Load [order.md](references/imports/order.md) for examples of import organization


## Code Quality
- Never assign a lambda to a name (PEP 8 E731) — use `def`. As a `key=` argument a lambda is fine; anything with branching or more than one expression is a `def`
- Comprehensions should hold one `for` and at most one `if`. Past that, use a loop or a named generator function
- Use f-strings, not `%` or `.format()`
- Use `logging`, not `print`, in anything that will be imported
- Guard scripts with `if __name__ == "__main__":`

## Tooling
- Code must pass `ruff check` and `ruff format` with zero findings
- Code must pass `mypy` (or `pyright`) with zero errors
- Suppressions must be specific and justified — `# noqa: E501  # URL cannot be wrapped`, never a bare `# noqa` or a blanket `# type: ignore`

## Tests
- Use `pytest` with plain `assert` — no `unittest` boilerplate
- Use `@pytest.mark.parametrize` instead of copy-pasting a test body
- Use the built-in `tmp_path` and `monkeypatch` fixtures rather than hand-rolling temp dirs or patching globals
- Assert on behavior, not on internals

## Documentation
- When refactoring existing code, take care to update code comments to ensure the comments are still accurate
- Also remember to update any documentation (often a CONTEXT.md and/or README.md) to keep it up-to-date with the code

**Remember**: Let the type checker and narrow exceptions do the work. If a line needs a comment to explain what it does, rewrite the line.
