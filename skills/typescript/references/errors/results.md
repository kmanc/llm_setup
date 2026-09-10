#### Example of typed errors
```typescript

// Function with Result return type
function parseConfig(input: string): Result<Config, ValidationError> {
  try {
    const data: unknown = JSON.parse(input);
    if (!isValidConfig(data)) {
      return {
        success: false,
        error: new ValidationError("Invalid config", "root", "INVALID_FORMAT"),
      };
    }
    return { success: true, value: data };
  } catch {
    return {
      success: false,
      error: new ValidationError("Parse failed", "root", "PARSE_ERROR"),
    };
  }
}
```