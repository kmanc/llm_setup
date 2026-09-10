#### Example of as const
```typescript

const MEDAL_PLACES = ["first", "second", "third"] as const;

type MedalPlace = (typeof MEDAL_PLACES)[number]; // "first" | "second" | "third"

// One source of truth: the list you iterate and the type you check are the same
function isMedalPlace(value: string): value is MedalPlace {
  return MEDAL_PLACES.some((place) => place === value);
}
```