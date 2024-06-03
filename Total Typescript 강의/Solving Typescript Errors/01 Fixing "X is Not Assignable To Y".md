```tsx
interface UserProfile {
	id:string;
	preferences: {
		theme:"light"|"dark";
	}
}

let user:UserProfile = {
	id: "123",
	preferences:{
		theme:"blue" // Error
	}
}
```

Assignable -> 결국 어떤 값을 할당 가능하냐! 이 말이다.

### 방법 1 - Union에 추가
```tsx
interface UserProfile {
	id:string;
	preferences: {
		theme:"light"|"dark"|"blue";
	}
}
```

### 방법2 - 범위 넓히기
```tsx
interface UserProfile {
	id:string;
	preferences: {
		theme: string;
	}
}
```
