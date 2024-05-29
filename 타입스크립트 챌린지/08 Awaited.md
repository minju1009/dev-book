```tsx
type ExampleType = Promise<string>

type Result = MyAwaited<ExampleType> // string

```

### 답안
```tsx
/**

* Recursively unwraps the "awaited type" of a type. Non-promise "thenables" should resolve to `never`. This emulates the behavior of `await`.

*/

type Awaited<T> = T extends null | undefined ? T : // special case for `null | undefined` when not in `--strictNullChecks` mode

T extends object & { then(onfulfilled: infer F, ...args: infer _): any; } ? // `await` only unwraps object types with a callable `then`. Non-object types are not unwrapped

F extends ((value: infer V, ...args: infer _) => any) ? // if the argument to `then` is callable, extracts the first argument

Awaited<V> : // recursively unwrap the value

never : // the argument to `then` was not callable

T; // non-object or non-thenable


```

1. 만약 T가 null 또는 undefined라면 T를 그대로 반환한다.
2. T가 then 메서드가 들어있는 객체인지 확인한다.
3. 만약 F가 호출가능한 함수라면, 첫번째 인자인 value에서 값을 가져와서
4. 그 값을 recursively 사용하여 unwrap하고,
5. F가 callable이 아니라면 await의 대상으로 사용할 수 없기에 아무것도 반환하지 않기때문에 never를 반환한다.