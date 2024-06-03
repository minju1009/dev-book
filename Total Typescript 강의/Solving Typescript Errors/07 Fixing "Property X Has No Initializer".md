```tsx
class User {
	private username: string;
}
```
![[Pasted image 20240603220315.png]]

### 해결방법 1  - give initializer
```tsx
class User {
	private username:string = " "; // 값을 이닐셜라이즈 해준다.
}
```


### 해결방법 2 - assign constructor
```tsx
class User {
	private username: string;

	constructor(){
		this.username = "" //  생성자에 할당해 준다.
	}
}

// OR
class User{
	private username? : string;
}
```
