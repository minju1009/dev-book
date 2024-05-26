```tsx
type arr1 = ['a', 'b', 'c']
type arr2 = [3, 2, 1]

type head1 = First<arr1> // expected to be 'a'
type head2 = First<arr2> // expected to be 3

// 내 답안  
type First<T extends string[]|number[]> = T[0]
```

내 답안도 맞았으나 아래 답안들도 가능하다!
```tsx
//answer1
type First<T extends any[]> = T extends [] ? never : T[0]

//answer2
type First<T extends any[]> = T['length'] extends 0 ? never : T[0]

//answer3
type First<T extends any[]> = T extends [infer A, ...infer rest] ? A : never
```

### answer 1
만약 T가 빈 배열이라면 아무것도 리턴하지 않고(never), 아닐 경우 첫번째 아이템을 반환한다.

### answer 2
T가 빈 배열라면, 다시 말해, 길이가 0이라면, 아무것도 반환하지 않고, 빈 배열이 아니라면 첫번재 아이템을 반환한다.

### answer 3
infer 키워드를 활요해서 A를 그대로 꺼낸다!!!
T가 첫번째 요소가 무조건 있는 배열이라면, 첫 번째 요소의 타입을 그대로 리턴하고, 배열이 아닐 경우 아무것도(never) 리턴하지 않는다.
