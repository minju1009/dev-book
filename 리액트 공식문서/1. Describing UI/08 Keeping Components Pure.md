컴포넌트를 순수하게 작성하면 코드베이스가 커지더라도 당황스러운 버그와 예측할 수 없는 동작을 피할 수 있다. 하지만 이렇게 이점을 얻으려면 몇 가지 규칙을 준수해야 한다.

- What purity is and how it helps you avoid bugs
- How to keep components pure by keeping changes out of the render phase
- How to use Strict Mode to find mistakes in your component

## 순수성: 수식으로서의 컴포넌트
컴퓨터 과학에서 `순수 함수`는 다음과 같은 특성을 지닌다.
	- 자신의 일에만 신경쓴다.
	- same inputs, same outputs.

```javascript
function double(number) {  
	return 2 * number;  
}
```

위의 예는 순수함수이다. 3을 전달하면 언제나 동일하게 6을 반환한다.

React는 이 개념을 중심으로 만들어졌다. React는 내가 작성하는 모든 컴포넌트가 순수 컴포넌트라고 가정한다. 따라서 동일한 prop을 전달했을 때 항상 동일한 출력을 반환해야 한다!!

## 사이드 이펙트: 의도하지 (않은) 결과
순수 컴포넌트여야 한다는 규칙을 어기는 경우를 한 번 살펴보자.
```javascript
let guest = 0;

function Cup() {
  // Bad: changing a preexisting variable!
  // 나쁨: 기존 변수를 변경합니다!
  guest = guest + 1;
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup />
      <Cup />
      <Cup />
    </>
  );
}
```

`<Cup />`이라는 컴포넌트는 외부의 값을 참조하고 있으므로 렌더링 될 때마다 다른 JSX를 반환하게 된다!! 이 문제는 prop으로 전달하여 순수 컴포넌트로 변경할 수 있다. 아래와 같이 변경하면 prop에 따라 항상 동일한 결과를 반환한다.
```javascript
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup guest={1} />
      <Cup guest={2} />
      <Cup guest={3} />
    </>
  );
}

```

Stirct Mode를 사용하면 개발 시에 렌더링을 두 번 하게 하여 이와 같은 버그를 미리 발견할 수 있게 해준다.

- 렌더링은 언제든지 발생할 수 있으므로, 컴포넌트는 서로의 렌더링 순서에 의존해서는 안 된다.
- 컴포넌트가 렌더링에 사용하는 어떠한 입력값도 변이해서는 안 됩니다. 여기에는 props, state 및 context가 포함됩니다. 화면을 업데이트하려면 기존 객체를 변이하는 대신 [“set” state](https://react-ko.dev/learn/state-a-components-memory)를 사용해야 한다.

### 지역 변이: 컴포넌트의 작은 비밀
외부의 변수를 변경하는 것은 순수 컴포넌트를 반환하지 않기 때문에 불가능 하나, 다음과 같이 렌더링 하는 동안 방금 생성한 변수와 객체를 변경하는 일은 괜찮다. 컴포넌트 `내부`에서 `동일한 렌더링`중에 생성했기 때문이다.

```javascript
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaGathering() {
  let cups = [];
  for (let i = 1; i <= 12; i++) {
    cups.push(<Cup key={i} guest={i} />);
  }
  return cups;
}
```


## 사이드 이펙트를 일으킬 수 있는 곳
순수 컴포넌트라 하더라도, 어디에선가는 변경을 일으켜야 한다. 이런 역할을 해 주는 것이 `이벤트 핸들러`이다. 이벤트 핸들러를 통해 해결할 수 없을 때에만 `useEffect`를 사용할 수 있다.

## 왜 리액트는 순수성을 중요시할까?
1. (순수성을 유지하면) 컴포넌트를 다른 환경에서도 렌더링 할 수 있기 때문이다. (예: 서버)
2. (순수성을 유지하면) 입력이 변경되지 않은 컴포넌트는 렌더링 건너뛰기를 통해 성능을 향상시킬 수 있기 때문이다. 순수함수는 언제나 동일한 결과를 반환하기 때문에 이전 데이터를 캐싱해도 안전하기 때문이다.
3. (순수성을 유지하면) 깊은 컴포넌트 트리를 렌더링 하는 도중에 데이터가 변경되면 React가 오래된 렌더링을 완료하기 위해 시간을 낭비하지 않고 미련없이 새로운 렌더링을 시쟉할 수 있게 해준다. 순수성 덕분에 언제든지 계산을 중단해도 안전하다.
4. 데이터 불러오기부터 애니메이션, 성능에 이르기까지, 컴포넌트를 순수하게 유지하면 React 패러다임의 힘을 발휘할 수 있기 떄문이다.