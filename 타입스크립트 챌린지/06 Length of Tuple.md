```tsx
type tesla = ['tesla', 'model 3', 'model X', 'model Y']
type spaceX = ['FALCON 9', 'FALCON HEAVY', 'DRAGON', 'STARSHIP', 'HUMAN SPACEFLIGHT']

type teslaLength = Length<tesla>  // expected 4
type spaceXLength = Length<spaceX> // expected 5

// 나의 답
type Length<T extends Array<string> = T['length'];

// 다른 사람 답
type Length<T extends readonly any[]> = T['length']
```

1) 다른 사람 답이 더 다양한 타입의 배열에 대해 대응할 수 있고! 

2) 어차피 매번 길이를 반환하는 배열이기 때문에 readonly로 제한해 줌으로써 배열이 바뀌지 않는 다는 것을 더 잘 알 수 있게 해준다!