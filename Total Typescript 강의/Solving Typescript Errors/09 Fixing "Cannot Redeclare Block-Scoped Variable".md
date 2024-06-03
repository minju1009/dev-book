```tsx
const name = "Matt";


```

만약 어떤 파일이 import, export가 없다면 typescript는 해당 파일을 global scope로 인식한다. 따라서 이미 전역 스코프에 있는 name이란 변수랑 충돌하기 때문에 해당 문제가 일어나고 있는 것이다.

### 해결방법 1 - import/export문 더해서 모듈이란 것을 알려주기

```tsx
const name = "Matt";

export {}
```

### 해결방법 2 - tsconfig 설정 변경
import/export문이 없더라도 모든 파일을 하나의 모듈로 인식하도록 하는 tsconfig 설정을 해준다.
```ts
"moduleDetection": "force"
```