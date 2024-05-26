```tsx
const tuple = ['tesla', 'model 3', 'model X', 'model Y'] as const

  
type result = TupleToObject<typeof tuple> // expected { 'tesla': 'tesla', 'model 3': 'model 3', 'model X': 'model X', 'model Y': 'model Y'}

  

type TupleToObject<T extends readonly string[]> = {
	[P in T[number]] : P
}
```

### step 1
```tsx
	type TupleToObject<T extends readonly string[]> {
		...
	}
```

### step 2
```tsx
type TupleToObject<T extends readonly string[]> {
		[P 어떻게든 T 배열안에 있는 값을 가져오기] : P
	}
```

### step 3
```tsx
type TupleToObject<T extends readonly string[]> {
		[P in T[number]] : P
	}
```
