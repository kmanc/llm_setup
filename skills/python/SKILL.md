---
name: python
description: Write clean, consistent, and performant Python code. Use this skill when the user asks to write or refine Python code, scripts, projects, or applications. Generates self-documenting, polished code based on the following best practices.
---

This skill guides creation of clean, performant, efficient, and maintainable Python code.

## When to Activate

- Writing new Python code
- Reviewing Python code
- Refactoring existing Python code
- Designing Python packages/modules

## Dictionaries
- Prefer `.get()` to direct key access
- Use `defaultdict`, `Counter`, `OrderedDict`, and other datatypes from the collections library liberally; never rely on dictionary ordering from the standard library

## File access
- Use the `with open()` pattern when accessing files

## Memory management
- Generators and lazy loading save RAM, take advantage of this where it makes sense

## Import Conventions

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
- Lambdas are usually not the best way to solve anything
- List and dictionary comprehensions are ok, but don't try to do too much in one line
- The zen of python is a fantastic set of guiding principals

## Documentation
- When refactoring existing code, take care to update code comments to ensure the comments are still accurate
- Also remember to update any documentation (often a CONTEXT.md and/or README.md) to keep it up-to-date with the code

#### Zen of Python
```
Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```
