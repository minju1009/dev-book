```tsx
interface Todo {
	title: string
	description: string
	completed: boolean
}

type TodoPreview = MyPick<Todo, 'title' | 'completed'>


const todo: TodoPreview = {
	title: 'Clean room',
	completed: false,
}


🌟 정답
type MyPick<T, K extends keyof T> = {
	[key in K] : T[key]
}
```

todo 객체는 Todo 인터페이스에 'title', 'completed'값만 가지고 와서 만들어지는 객체이다. 따라서 TodoPreview라는 타입은 Todo중에서 title, completed만 key로 가지게 되는 타입으로 만들어줘야 한다.

따라서 MyPick은 Todo라는 객체와, 그 객체의 키들을 타입으로 받은 후, Todo 객체에서 K 키와 값들을 가져와 쓸 수 있게 된다.

실제로 Pick이란 타입을 확인해보면 그 정의는 아래와 같다.
```tsx
/**
* From T, pick a set of properties whose keys are in the union K
*/

type Pick<T, K extends keyof T> = {
	[P in K]: T[P];
};
```

### Keyof Type Operator (Keyof 연산자)
`keyof`연산자는 객체 타입을 받아 그 객체의 키의 string또는 number의 리터럴 유니온 타입을 반환한다. 예를 들어 아래에서 타입 P는 `type P = 'x' | 'y'`와 같다.

```tsx
type Point = {x: number, y: number};
type P = keyof Point;
// type P = "x" | "y"와 같음
```

만약 객체에 string또는 number 인덱스 시그니처를 가진다면, `keyof`는 받은 인덱스 타입을 반환할 것이다.
```tsx
type Arrayish = { [n: number]:unknown};
type A = keyof Arrayish;
// type A = number

type Mapish = { [k: string]: boolean};
type M = keyof Mapish;
// type M = string|number
```

위의 두 번 째 예시에서 M 은 `string|number`타입을 가지는데 자바스크립트 객체 키는 항상 문자형으로 강제되기 때문이다. `obj[0] 과 obj["0"]`은 동일하기 때문이다.
```javascript
let user = {
    0: "Violet",
    '1': 30
  };

console.log(user[0]) // "Violet"
console.log(user['0']) // "Violet"

```

### [`in` operator narrowing](https://www.typescriptlang.org/docs/handbook/2/narrowing.html#the-in-operator-narrowing)

객체나 그 프로토타입 체인에 그 이름을 가진 프로퍼티가 있는지 확인할 수 있는 연산자이다.
타입스크립트에서 이 연산자를 사용하여 사용가능한 타입을 좁히는데 사용할 수 있다.

예를 들어, `"value" in x` 라는 코드가 있을 때, `true`인 상황에서는 optional 혹은 required인 key를 반환하며, `false`인 상황에서는 optional혹은 x에 없는 프로퍼티를 반환하는 경우가 된다.

```tsx
type Fish = { swim : () => void };
type Bird = { fly: () => void };

function move(animal: Fish | Bird){
	if ("swim" in animal) {
		return animal.swim(); // 이걸 리턴하지 않으면 아래 animal.fly에서 타입스크립트 오류가 난다.
	}

	return animal.fly()
}
```

optional은 narrowing할 때 둘 다에 속한다는 것을 다시한번 짚고 넘어갈 필요가 있다.
```tsx
type Fish = { swim : () => void };
type Bird = { fly: () => void };
type Human = { swim? : () => void, fly? : () => void}

  
function move(animal: Fish | Bird | Human){
	if ("swim" in animal) {
	return animal
	// animal : Fish | Human
}

	return animal
	// animal : Bird | Human
}
```