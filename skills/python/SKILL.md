---
name: python
description: Write clean, consistent, and performant Python code. Use this skill when the user asks to design Python packages or modules, or write, review, refactor, or refine Python code, scripts, projects, or applications. Generates self-documenting, polished code based on the following best practices.
---

This skill guides creation of clean, performant, efficient, and maintainable Python code.

## Type Hints
- Annotate every public function signature — parameters and return type
- Use modern syntax: `list[str]`, `dict[str, int]`, `X | None` — not `List`, `Dict`, `Optional`
- Code should pass `mypy --strict` (or `pyright`) cleanly
- Avoid `Any`; when a value is genuinely untyped, take `object` and narrow it
- Use `Protocol` for structural typing instead of demanding a base class

#### Example type hints
```python
from collections.abc import Iterable
from typing import Protocol

class Named(Protocol):
    name: str

def longest_name(items: Iterable[Named]) -> str | None:
    return max((item.name for item in items), key=len, default=None)
```

## Errors
- Catch the narrowest exception that can actually be raised
- Never write a bare `except:` or `except Exception: pass` — an error you swallow is a bug you debug later without a traceback
- Use `raise ... from e` so the original cause survives
- Define module-specific exception types; callers should catch your errors, not `ValueError`
- When skipping an error really is correct, say so explicitly with `contextlib.suppress`

#### Example error handling
```python
class ConfigError(Exception):
    """Raised when a config file is missing or malformed."""

def load_config(path: Path) -> Config:
    try:
        raw = tomllib.loads(path.read_text())
    except OSError as e:
        raise ConfigError(f"cannot read config at {path}") from e
    except tomllib.TOMLDecodeError as e:
        raise ConfigError(f"invalid TOML in {path}") from e
    return Config(**raw)
```

## Data Modeling
- Prefer `@dataclass(frozen=True, slots=True)` over passing dicts and tuples around
- Use `Enum`/`StrEnum` for a fixed set of values — never bare string literals scattered through the code
- `NamedTuple` is fine for a small return value with an obvious order
- Never use a mutable default argument — `def f(items: list[str] = [])` shares one list across every call

#### Example data modeling
```python
from dataclasses import dataclass
from enum import StrEnum

class Status(StrEnum):
    ACTIVE = "active"
    SUSPENDED = "suspended"

@dataclass(frozen=True, slots=True)
class User:
    id: int
    email: str
    status: Status = Status.ACTIVE
    tags: tuple[str, ...] = ()  # not a list, which cannot be a frozen default
```

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
- The tradeoff is single-pass: a generator has no `len()` and cannot be iterated twice. If you need either, build the list

#### Example generator
```python
def error_lines(path: Path) -> Iterator[str]:
    with path.open() as f:
        yield from (line for line in f if "ERROR" in line)
```

## Import Conventions

#### Example import order
```python
# Good: Import order - stdlib, third-party, local
import os
import sys
from pathlib import Path

import requests
from fastapi import FastAPI

from mypackage.models import User
from mypackage.utils import format_name
```

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
