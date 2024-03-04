> 컴포넌트가 특정 정보를 '기억'하도록 하고 싶지만 해당 정보가 새 렌더링을 촉발하지 않도록 하려는 경우 ref를 사용할 수 있다.

- 컴포넌트에 ref를 추가하는 방법
- ref 값을 업데이트하는 방법
- state와 ref의 차이점
- ref를 안전하게 사용하는 방법
## 컴포넌트에 ref 추가하는 방법
React에서 useRef를 가져와 컴포넌트에 추가할 수 있다.
```js
import {useRef} from 'react';

const ref = useRef(0);

// useRef는 다음과 같은 객체를 반환한다.
{
	current : 0 
}
```

React가 추적하지 않는 비밀주머니로, React의 단방향 데이터 흐름에서 '탈출구'가 되어 읽기와 쓰기가 가능하며, `컴포넌트는 ref가 증가할 때마다 리랜더링되지 않는다`.

예를 들어 스톱워치 만드는 곳에 state와 ref를 함께 사용할 수 있다.
Stop을 누르면 now state 변수의 업데이트를 중지하도록 기존 interval을 취소해야 한다. 이 작업은 clearInterval을 호출하여 수행할 수 있지만, 사용자가 시작을 눌렀을 때 이전에 setInterval 호출에서 반환한 interval ID를 제공해야 한다. 다시 말해, interval ID를 어딘가에 보관해야만 한다!

```js
export default function Stopwatch(){
	const [startTime, setStartTime] = useState(null);
	const [now, SetNow] = useState(null);
	const intervalRef = useRef(null);

	function handleStart(){
		setStartTime(Date.now());
		setNow(Date.now());

	// interval ID를 렌더링간 참조하여 clear 해주기 위해 필요
	clearInterval(intervalRef.current);
	intervalRef.current = setInterval(() => {
		setNow(Date.now());
		}, 10);
	}

	function handleStop() {
		cleareInterval(intervalRef.current);
	}

   ....
	}

}
```
## ref와 state의 차이점
| refs                                                                        | state                                                                                         |
| --------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| useRef는 {current:initialValue}를 반환                                      | useState는 [value, initialValue]를 반환                                                       |
| 변경 시 리렌더링을 촉발하지 않음                                            | 변경 시 리렌더링을 촉발                                                                       |
| Mutable - 렌더링 프로세스 외부에서 current 값을 수정하고 업데이트할 수 있음 | Immutable - state setting 함수를 사용하여 state 변수를 수정해 리렌더링을 대기열에 추가해야 함 |
| 렌더링 중에는 current 값을 읽거나 쓰지 않아야 함                            | 언제든지 state를 읽을 수 있음. 각 렌더링에는 변경되지 않는 자체 state snapshot이 있음         |
| 

## ref는 내부에서 어떻게 작동하나?
ref는 다음과 같이 원칙적으로 useState위에 구현될 수 있다.
```js
function useRef(initialValue){
	const [ref, unused] = useState({current: initialValue});
	return ref;
}
```
위 코드엑서 보면 state setter가 사용되지 않았다. ref를 설정자가 없는 일반 state 변수라고 생각하면 되며, 객체지향 프로그래밍에서의 인스턴스 필드로 생각할 수 있는데, `this.something`대신 `somethingRef.current`를 사용하면 된다.

## Ref를 사용해야 하는 경우
일반적으로 ref는 컴포넌트가 React로부터 '외부로 나가서' 외부 API, 즉 컴포넌트의 형상에 영향을 주지 않는 브라우저 API 등과 통신해야 할 때 사용한다. 
- timeout ID 저장
- 다음 챕터에서 다룰 DOM elements 저장 및 조작
- JSX를 계산하는 데 필요하지 않은 다른 객체 저장


## Best practices for refs
- ref는 외부 시스템이나 브라우저 API로 작업할 때 유용하다. 애플리케이션 로직과 데이터 흐름의 대부분이 ref에 의존하는 경우 접근 방식을 재고해봐야 한다.
- 렌더링 중에는 ref.current를 읽거나 쓰지 말아라. 렌더린 중에 일부 정보가 필요한 경우 state를 사용하는게 좋고, React는 ref.current가 언제 변경되는지 알지 못하기 때문에, 렌더링 중에 읽어도 컴포넌트의 동작을 예측하기가 어렵다. (유일한 예외는 첫 번째 렌더링 중에 ref를 한 번만 설정하는 if(!ref.current) ref.current = new Thing()과 같은 코드이다.)

## Ref와 DOM
ref는 모든 값을 가리킬 수 있으나, 가장 일반적인 사용 사례는 DOM 요소에 액세스 하는 것이다. 예를 들어, 프로그래밍 방식으로 input에 focus를 맞추고자 할 때 유용하다. `<div ref={myRef}>`와 같이 JSX의 ref 어트리뷰트에 ref를 전달하면 React는 해당 DOM 엘리먼트를 myRef.current에 넣는다.