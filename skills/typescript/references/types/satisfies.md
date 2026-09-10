#### Example of satisfies
```typescript

type Palette = Record<string, string | [number, number, number]>;

const palette = {
  red: [255, 0, 0],
  green: "#00ff00",
} satisfies Palette;

palette.red[0];              // number — known to be the tuple, not the union
palette.green.toUpperCase(); // string — an annotation would have lost this
```