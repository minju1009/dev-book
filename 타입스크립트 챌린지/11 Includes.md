```tsx
type isPillarMen = Includes<['Kars', 'Esidisi', 'Wamuu', 'Santana'], 'Dio'> // expected to be `false`

//답안
type Includes<T extends readonly any[], U> = {

[P in T[number]]: true

}[U] extends true ? true : false;
```


1. **`T[number]`**:
    
    - `T`는 `readonly any[]` 타입을 확장하는 배열 타입입니다.
    - `T[number]`는 인덱스 타입 쿼리입니다. 이는 `T` 배열의 모든 요소 타입의 유니언(union) 타입을 나타냅니다. 예를 들어, `T`가 `[1, 2, 3]`이라면 `T[number]`는 `1 | 2 | 3`이 됩니다.
2. **매핑된 타입 `{ [P in T[number]]: true }`**:

    - 여기서 우리는 `T` 배열의 모든 요소에 대해 매핑된 타입을 생성합니다.
    - `T[number]`는 `T` 배열의 모든 요소 타입의 유니언이므로, `P`는 `T`의 각 요소를 차례로 받습니다.
    - 각 요소에 대해 `true` 값을 할당합니다.
    - 예를 들어, `T`가 `[1, 2, 3]`이라면, `{ [P in T[number]]: true }`는 `{ 1: true, 2: true, 3: true }`가 됩니다.
3. **인덱스 타입 쿼리 `[U]`**:
    
    - 위에서 만든 매핑된 타입에서 `U`를 키로 사용하여 값을 조회합니다.
    - 예를 들어, `U`가 `2`라면, `{ 1: true, 2: true, 3: true }[2]`는 `true`입니다.
    - 만약 `U`가 `4`라면, `{ 1: true, 2: true, 3: true }[4]`는 존재하지 않으므로 `undefined`가 됩니다.
4. **조건부 타입 `extends true ? true : false`**:
    
    - 조회한 값이 `true`인지 확인합니다.
    - 만약 조회한 값이 `true`라면 `Includes` 타입은 `true`가 됩니다.
    - 그렇지 않으면 `false`가 됩니다.

결국, `Includes` 타입은 주어진 요소 `U`가 배열 `T`에 포함되어 있는지를 `true` 또는 `false`로 나타내는 타입입니다.

