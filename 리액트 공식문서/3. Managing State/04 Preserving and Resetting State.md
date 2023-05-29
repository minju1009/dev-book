> state의 보존 또는 재설정 여부를 제어하는 방법

> State는 컴포넌트 간에 격리(isolated)된다. React는 UI 트리에서 어떤 컴포넌트가 어떤 state에 속하는지를 추적한다. 사용자는 state를 언제 보존하고 언제 초기화할지를 제어할 수 있다.

## UI 트리
DOM이나 CSSOM처럼 React또한 트리 구조를 사용하여 사용자가 만든 UI를 관리하고 모델링한다. React는 JSX로부터 UI트리를 만든다. 그 다음 React DOM은 해당 UI트리와 일치하도록 브라우저 DOM 엘리먼트를 업데이트 한다.

![[Pasted image 20230529020628.png]]

## State는 트리의 한 위치에 묶인다.
컴포넌트에서 state를 부여할 때, state가 컴포넌트 내부에 '존재'한다고 생각할 수 있다. 그러나 state는 실제로 React 내부에서 유지된다. React는 UI 트리에서 해당 컴포넌트가 어디에 위치하는지에 따라 보유하고 있는 각 state를 올바른 컴포넌트와 연결한다.

따라서 React가 컴포넌트를 제거하면, 그 state도 함께 사라진다. 그러다가 다시 한번 동일한 컴포넌트를 렌더링하면 state가 처음부터 초기화되고 DOM에 추가된다.

결론적으로, `React는 컴포넌트가 UI트리의 해당 위치에서 렌더링되는 동안 컴포넌트의 state를 유지한다. 컴포넌트가 제거되거나 같은 위치에 다른 컴포넌트가 렌더링되면 React는 해당 컴포넌트의 state를 삭제`한다.

## 동일한 위치의 동일한 컴포넌트는 state를 유지한다.
아래 컴포넌트를 예시로 보자.
```js
export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <Counter isFancy={true} /> 
      ) : (
        <Counter isFancy={false} /> 
      )}
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
```

리액트 관점에서 위 코드의 Counter는 항상 그 자리에 위치한다. 따라서 Counter내의 state는 재설정되지 않는다. isFancy가 true이든 false이든 루트 App 컴포넌트에서 반환된 div의 첫 번째 자식에는 항상 <Counter />가 있다.

![[Pasted image 20230529022000.png]]

> 같은 위치에 있는 같은 컴포넌트이므로 React 관점에서 보면 같은 카운터이다.

## 동일한 위치의 다른 컴포넌트는 state를 초기화한다.
아래 예제에서 p와 Counter는 동일한 위치이지만 다른 컴포넌트이기 때문에 React는 UI에서 Counter를 제거하고 그 state를 소멸시킨다!
```js
export default function App() {
  const [isPaused, setIsPaused] = useState(false);
  return (
    <div>
      {isPaused ? (
        <p>See you later!</p> 
      ) : (
        <Counter /> 
      )}
      <label>
        <input
          type="checkbox"
          check
      
      ...
      
```

![[Pasted image 20230529022358.png]]

또한 같은 위치에 다른 컴포넌트를 렌더링하면 전체 하위 트리의 state가 재설정된다. 
![[Pasted image 20230529022526.png]]
Counter가 동일한 위치에 자식으로 있다고 하더라도, section이 div로 바뀌면서 DOM에서 제거되면 그 아래의 전체 트리도 함께 제거된다.

따라서 리렌더링 사이에 state를 유지하려면 트리의 구조가 '일치'해야 한다. 구조가 다르면 React는 트리에서 컴포넌트를 제거할 때 state를 파괴하기 때문이다.

> 따라서 컴포넌트 함수를 중첩하면 안 된다. 매번 새로운 DOM이 생기기 때문에 이는 같은 위치에 다른 컴포넌트를 렌더링하는 것이고, 따라서 React는 아래의 모든 state를 초기화한다. 이로 인해 버그와 성능 문제가 발생하므로, 항상 컴포넌트 함수를 최상위 수준에서 선언하고 정의를 중첩하지 말아야 한다.

## 동일한 위치에서 state 재설정하기
#### 방법 1. 컴포넌트를 다른 위치에 렌더링하기
아래 처럼 할 수 있으나 이 예시에서는 두 개만 있으므로 JSX에서 두 컴포넌트를 별도로 렌더링하는 것이 번거롭지 않다.
```js
export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA &&
        <Counter person="Taylor" />
      }
      {!isPlayerA &&
        <Counter person="Sarah" />
      }
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
...
```
#### 방법 2. key로 state 재설정하기
다음과 같이 key를 부여할 수 있다. 테일러와 사라 사이를 전환해도 state가 유지되지 않는다. 동일한 위치에 있으나 각각에 다른 key를 부여했기 때문이다.

```js
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter key="Taylor" person="Taylor" />
      ) : (
        <Counter key="Sarah" person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}

```

> key를 지정하면 React가 부모 내 순서가 아닌 key 자체를 위치처럼, 위치의 일부로 사용하도록 지시한다. 그렇기 때문에 JSX에서 같은 위치에 렌더링하더라도 React의 관점에서 보면 두 카운터는 서로 다른 카운터이기 때문에 결과적으로 state를 공유하지 않게 되어 카운터가 화면에 나타날 때마다 state가 생성되고, 카운터가 제거될 때마다 그 state는 소멸된다.

## 키로 form 재설정하기
> 키로 state를 재설정하는 것은 form을 다룰 때 특히 유용하다.

예를 들어 채팅앱 같은 것을 만들때, 동일한 UI에서 state변경에 따라 값을 초기화 할 수 있다.
![[Pasted image 20230529024251.png]]