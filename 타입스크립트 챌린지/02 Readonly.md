```tsx
interface Todo {
	title: string
	description: string
}

const todo: MyReadonly<Todo> = {
	title: "Hey",
	description: "foobar"
}

// 정답
type MyReadonly<T> = {	
	readonly [P in keyof T] : T[P]
}

todo.title = "Hello" // Error: cannot reassign a readonly property
todo.description = "barFoo" // Error: cannot reassign a readonly property
```

readonly 키워드를 통해 타입으로 받은 T 객체의 key를 돌면서(in) 그 key를 가져와(keyof) readonly이므로 동일한 값을 반환하도록 한다.4

### [Typescript readonly and const 참고](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-func.html#readonly-and-const)
- 자바스크립트에서는 const를 통해 참조값이 불변성을 가지도록 할 수 있으나, 가변성이 디폴트이다. 따라서 아래와 같은 참조형 데이터의 변경은 여전히 가능하다.
- In JavaScript, mutability is the default, although it allows variable declarations with `const` to declare that the _reference_ is immutable. The referent is still mutable:
  ```js
  const a = [1, 2, 3];
  a.push(102); // a = [1,2,3,102]
  a[0] = 101; // a = [101,2,3,102]
```


#### readonly
- 타입스크립트에서는 프로퍼티를 변경할 수 없도록 프로퍼티 앞에 붙이는 `readonly`타입이 있다.
```tsx
interface Rx {
	readonly x : number;
}

let rx: Rx = {x : 1};
rx.x = 12;  // error
```
![[Pasted image 20240522224813.png]]


#### `Readonly<T>` && `ReadonlyArray<T>`
Readonly는 객체의 모든 키를 한 번에 전부 readonly로 변경해주고,
ReadonlyArray는 배열에 추가적인 변경을 하지 못하도록 배열을 readonly로 변경한다.
```tsx
let abc: ReadonlyArray<number> = [1, 2, 3];
	abc.push(5); // Error!
	abc[0] = 2; // Error!
```

#### const assertion
동일한 효과를 위해 `as const`도 사용할 수 있다. 배열과 객체 리터럴에서 사용할 수 있다.
```tsx
let a = [1, 2, 3] as const;
a.push(102); // Error!
a[0] = 101; // Error!
```




