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