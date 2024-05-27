   ```tsx
type Result = MyExclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'

// 내 답!
type MyExclude<T, U> = T extends U? never : T;
```

만약 U라는 값이 T안에 있다면 아무것도 반환하지 않도록 제외하고, U라는 값이 없는 T만 반환한다!

### Distributive Conditional Types
https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types

When conditional types act on a generic type, they become _distributive_ when given a union type. For example, take the following:

조건부 타입이 제너릭으로 활용될 때, 유니언 타입이 주어지면 '분배'가 된다.
예를 들어,

```tsx
type ToArray<Type> = Type extends any ? Type[] : never;


type StrArrOrNumArr = ToArray<string | number>;

type StrArrOrNumArr = string[] | number[]
```

위에서 ToArray에 Type 대신 `<string | number>`란 유니언 타입이 주어졌을때, 다음과 같이 `ToArray<string> | ToArray<number>;` 유니언 타입의 각각 하나씩이 Type 대신에 들어가게 되고, 결국 `string[]|number[]`가 남게 되는 것이다.