```tsx
type X = { 
  x: { 
    a: 1
    b: 'hi'
  }
  y: 'hey'
}

type Expected = { 
  readonly x: { 
    readonly a: 1
    readonly b: 'hi'
  }
  readonly y: 'hey' 
}

type Todo = DeepReadonly<X> // should be same as `Expected`

// 답안 1
type Readonly<T> = keyof T extends never? 
	T 
	: {readonly [k in keyof T]: DeepReadonly<T[k]};

// 답안 2
type DeepReadonly<T> = {
  readonly [k in keyof T]: T[k] extends Record<any, any>
    ? T[k] extends Function
      ? T[k]
      : DeepReadonly<T[k]>
    : T[k]
}

										

```

## 답안 1
### step 1
T가 객체가 아닌 경우 recursion인 중지된다.
```tsx
keyof T extends never ?  T 
```

#### never
1) 절대 (never) 어떤 값도 리턴하지 않는 경우 반환되는 타입이다.
	`에러를 던지는 경우나 프로그램이 종료되는 경우` 사용된다.
```tsx
function fail(msg: string): never {
	throw new Error(msg);
}
```
2) 또는 union 타입 사용시 남아있는 타입이 없을 때 사용된다.
```tsx
function fn(x: string | number) {
	if (typeof x === "string") {
		// do something
	} else if (typeof x === "number") {
		// do something else
	} else {
		x; // has type 'never'!
	}
}
```

### step 2
DeepReadonly 타입에 다시 객체의 키 안의 값을 넣어 recursive 하도록 만든다.
```tsx
{ readonly [k in keyof T]: DeepReadonly<T[k]> }
```
