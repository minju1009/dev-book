```tsx
type Result = Push<[1, 2], '3'> // [1, 2, '3']
```

```tsx
type Push<T extends unknown[], U> = [...T, U]
```

왜 any대신에 unknown을 사용하는가 ?
https://www.typescriptlang.org/docs/handbook/2/functions.html#unknown
The unknown type represents any value. This is similar to the any type, but is safer because it’s not legal to do anything with an unknown value:

