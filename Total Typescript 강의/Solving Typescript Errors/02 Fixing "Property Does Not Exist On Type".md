```tsx
const user = {
	name : 'Matt',
}

user.age = 24; // typescript Error!
```

### 내 답안
```tsx
	type User = {
		name: string;
		age?: number;
	}
	
	const user:User = {
		name: 'Matt',
	}
```

### 방법 1 - Record 이용하여 범위 넓히기
```tsx
const user:Record<string, number|string> = {
	name: 'Matt',
};

user.age = 24;
```

왜 알아서 추론을 안해주냐? 타입스크립트 역할이 이런 잘못된 가능성을 막아주기 위함이기 때문이다!