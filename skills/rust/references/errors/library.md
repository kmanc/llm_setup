#### Example library error type
```rust

use std::path::PathBuf;
use thiserror::Error;

#[derive(Debug, Error)]
pub enum ConfigError {
    #[error("cannot read config at {path}")]
    Read {
        path: PathBuf,
        #[source]
        source: std::io::Error,
    },

    #[error("invalid TOML in {path}")]
    Parse {
        path: PathBuf,
        #[source]
        source: toml::de::Error,
    },
}
```