#### Example generator
```python

def error_lines(path: Path) -> Iterator[str]:
    with path.open() as f:
        yield from (line for line in f if "ERROR" in line)
```