#### Example type hints
```python

from collections.abc import Iterable
from typing import Protocol

class Named(Protocol):
    name: str

def longest_name(items: Iterable[Named]) -> str | None:
    return max((item.name for item in items), key=len, default=None)
```