#### Example of unknown usage
```typescript

function parseJson(text: string): unknown {
  return JSON.parse(text); // `any` here would infect every caller
}

const data = parseJson('{"name": "test"}');
if (isUser(data)) {
  data.name; // Safe - type narrowed
}
```