state는 자바스크립트 변수처럼 읽고 쓸수 있게 생겼지만, 스냅샷의 개념으로 접근해야 한다. state 변수를 새로 settting해도 이미 가지고 있는 state 변수는 변경되지 않고, 대신 리렌더링이 실행된다.

- state 설정으로 리렌더링이 촉발되는 방식
- state 업데이트 시기 및 방법
- state를 설정한 직후에 state가 업데이트되지 않는 이유
- 이벤트 핸들러가 state의 ‘스냅샷’에 액세스하는 방법

## State를 설정하면 렌더링이 촉발된다.
예를 들어, state를 변경하는 버튼을 클릭하면,
1. 이벤트 핸들러가 실행되고,
2. setState 함수에 따라 새로운 state가 설정되며, 새 렌더링을 큐에 대기시킨다.
3. React는 새로운 state에 따라 컴포넌트를 다시 렌더링한다.

## 렌더링은 그 순간의 스냅샷을 찍는다!
- '렌더링' 이란 React가 내 컴포넌트, 즉 함수를 다시 호출하는 과정이다. prop, 이벤트 핸들러, 지역 변수는 모두 **렌더링 당시의 state를 사용해** 계산된다.

- 렌더링이 일어날 당시에 JSX 스냅샷을 반환받는데, React는 이 스냅샷과 일치하도록 화면을 업데이트하고 이벤트 핸들러를 연결한다.

- React가 컴포넌트를 재렌더링할 때,
	- React가 함수를 다시 호출한다.
	- 함수가 새로운 JSX 스냅샷을 반환하면
	- React가 반환한 스냅샷과 일치하도록 화면을 업데이트한다.

다음은 이것이 어떻게 작동하는지 보여주는 간단한 실험이다.

```js

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  )
}

```

위 코드를 통해 스냅샷을 찍는다는 것의 의미를 알 수 있다. 버튼을 클릭하면 number는 3이 아니라 1로 변경이된다! 왜냐하면 state를 설정하면 다음 렌더링 때 값이 변경되기 때문이다! 첫 렌더링 이후 한번 클릭하는 시점에 number는 세번 다 0으로 유지되며,  그 다음 렌더링때 값이 변경된다! 즉, 아래 코드와 같다.

```js
<button onClick={() => {  
	setNumber(0 + 1);  
	setNumber(0 + 1);  
	setNumber(0 + 1);  
}}>+3</button>
```

시간이 지나도 그 시점의 스냅샷은 유지된다. 따라서 다음과 같은 비동기 함수를 처리할 때 참조하는 state값은 그 시점의 state를 참조한다는 것을 잊어서는 안된다.

```js
import { useState } from 'react';

export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 5);
        setTimeout(() => {
          alert(number);             // 처음 클릭시 5가 아닌, 0이 호출된다.
        }, 3000);
      }}>+5</button>
    </>
  )
}

```