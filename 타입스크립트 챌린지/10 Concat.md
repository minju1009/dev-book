```tsx
type Result = Concat<[1], [2]> // expected to be [1, 2]

  
// 내 답안
type Concat<V extends number[], T extends number[]> = [...V,...T]
```
### 답안
```tsx
type Concat<T extends Tuple, U extends Tuple> = [...T, ...U];
```

나는 좁혔고, 답안들은 받을 수 있는 타입을 any나 unknown으로 지정하여 타입을 넓혔다. 상황에 따라 조정 가능할 것 같다.