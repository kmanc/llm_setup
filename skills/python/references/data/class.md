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