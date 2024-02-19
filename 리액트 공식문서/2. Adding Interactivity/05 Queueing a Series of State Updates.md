여러 state 업데이트를 큐에 담기

state 변수를 설정하면 다음 렌더링이 큐에 들어간다. 그러나 경우에 따라 다음 렌더링을 큐에 넣기 전에, 값에 대해 여러 작업을 수행하고 싶을 때도 있다. 이를 위해 React가 state 업데이트를 어떻게 배치하면 좋을지 이해하는 것이 도움이 된다.

- 일괄처리(배칭, batching)이란 무엇이며 React가 여러 state 업데이트를 처리하는 방법
- 동일한 state 변수에서 여러 업데이트를 적용하는 방법

### React의 batching에 대하여 (state 업데이트 일괄처리)
- React는 state 업데이트를 하기 전에 이벤트 핸들러의 모든 코드가 실행될 때까지 기다린다. (음식점에서 웨이터가 내 추가주문을 한번에 받아가듯이)
- 다시 말하면 내 UI는 이벤트 핸들러의 모든 코드가 완료될때까지 업데이트 되지 않는다는 건데, 이를 `batching` 이라고 한다. 이 동작이 있기에 React 앱이 빠르게 실행될 수 있다.
- 이렇게 하면 너무 많은 리렌더링을 촉발하지 않고도 여러 컴포넌트에서 나온 다수 state 변수를 업데이트 할 수 있다.
- React는 클릭과 같은 여러 의도적인 이벤트에 대해 일괄처리 하지는 않는다. 한 이벤트에 대해 일괄처리하는 것을 batching이라고 한다.

### 다음 렌더링 전 동일한 state 변수를 여러 번 업데이트 하려면?
-> 업데이터 함수를 사용하여 React가 큐에 변경사항을 추가하도록 하면 된다.
setNumber(number + 1) 대신 이전 state를 기반으로 다음 state를 계산하는 함수를 전달하면 된다. 이는 단순히 state 값을 대체하는 것이 아닌, React에게 "state 값으로 무언가를 하라"고 지시하는 방법이다!

```js
export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(n => n + 1);
        setNumber(n => n + 1);
        setNumber(n => n + 1);
      }}>+3</button>
    </>
  )
}

```

다음 렌더링 중에 useState를 호출하면 React는 큐를 순회한다. 
다음의 코드에서 리액트의 큐에는 5, 그리고 n => n + 1이 저장되어 최종적으로 6이 반환된다.
```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5); // (n => n + 5)와 같다.
        setNumber(n => n + 1);
      }}>Increase the number</button>
    </>
  )
}

```

### Naming Convention
일반적으로 업데이터 함수의 인수 이름은 해당 state 변수의 첫 글자로 지정한다.

```js
setEnabled(e => !e);  

setLastName(ln => ln.reverse());  

setFriendCount(fc => fc * 2);
```