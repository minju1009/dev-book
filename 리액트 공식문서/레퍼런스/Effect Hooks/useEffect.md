
# **Reference**

### `useEffect(setup, dependencies?)`

**Parameters** (매개변수)

- `setup` : Effect의 로직이 포함된 함수이다. 셋업 함수는 선택적으로 클린업 함수를 반환할 수도 있다. React 컴포넌트가 DOM에 추가되면 셋업 함수를 실행한다. 의존성이 변경되어 다시 렌더링할 때마다 React는 먼저 이전 값으로 클린업 함수를 실행한 다음, 새 값으로 셋업 함수를 실행한다. 컴포넌트가 DOM에서 제거되면, React는 마지막으로 클린업 함수를 실행한다.
    - DOM에 React 컴포넌트 추가 -> 셋업 함수 실행
    - 의존성 변경시 -> 이전 값으로 클린업 함수 실행 -> 새 값으로 셋업 함수 실행
    - DOM에서 컴포넌트 제거시 -> 마지막으로 클린업 함수 실행
- optional `dependencies`
    - setup 코드 내에서 참조된 모든 반응형 값의 목록
        - 반응형 값은 props, state, 컴포넌트 본문 내부에서 직접 선언한 모든 변수와 함수를 포함한다.
    - React는 각 의존성에 대해 `Object.is`로 이전 값과 비교한다.
    - 의존성을 전혀 지정하지 않으면 컴포넌트를 다시 렌더링할 때마다 Effect가 다시 샐행된다.

**Returns** (반환값)

- useEffect는 undefined를 반환한다.

**Caveats** (주의사항)

- useEffect는 훅이므로 `컴포넌트의 최상위 레벨 또는 자체 훅`에서만 호출할 수 있다. 반복문이나 조건문 안에서 호출할 수 없다.
- `외부 시스템과 동기화하려는 목적이 아니라면` Effect가 필요하지 않을수도 있다.
- Strict 모드가 켜져 있으면 React는 첫 번째 실제 셋업 전에 `개발 전용의 셋업+클린업 사이클`을 한 번 더 실행한다. 이는 클린업 로직이 셋업 로직을 '미러링'하고 셋업이 수행 중인 모든 작업을 중지하거나 취소하는지를 확인하는 스트레스 테스트이다.
- 의존성 중 일부가 컴포넌트 내부에 정의된 객체 또는 함수인 경우 Effect가 필요 이상으로 자주 다시 실행될 위험이 있다. 불필요한 객체 및 함수 의존성을 제거해야 한다.
- React는 브라우저가 Effect를 실행하기 전에 업데이트된 화면을 먼저 그리도록 한다. 만약 Effect가 시각적인 작업을 해야 하고, 지연이 눈에 띄는 경우에는 `useEffect`대신 `useLayoutEffect`를 사용해야 한다.
- Effect는 클라이언트에서만 실행된다. 서버 렌더링 중에는 실행되지 않는다.

# **Usage**

### 외부 시스템에 연결하기

```jsx
import {useEffect} from 'react';
import {createConnection} from './chat.js';

function ChatRoom({roomId}){
	const [serverUrl, setServerUrl] = useState('<https://localhost:1234>');

	useEffect(() => {
		const connection = createConnection(serverUrl, roomId); // 셋업 함수
		connection.connect();

		return () => {                // 클린업 함수
			connection.disconnect();
		};

	}, [serverUrl, roomId]) // 의존성 목록
}

```

- 개발 환경에서는 셋업 -> 클린업 -> 셋업 순서로 호출되며, 상용에서는 셋업이 한 번 호출된다.
- 외부시스템이란 React로 제어되지 않는 코드 조각을 의미한다.
    - setInterval() 및 cleareInterval()로 관리되는 타이머.
    - window.addEventListener(), window.removeEventListener()를 사용하는 이벤트 구독
    - animation.start(), animation.reset()과 같은 API가 있는 타사 애니메이션 라이브러리.

### 커스텀 훅으로 Effect 감싸기

Effect는 `React를 벗어나야 할 때,` 또는 더 나은 빌트인 솔루션이 없을 때 사용하는 `탈출구`이다. Effect를 수동으로 작성해야 하는 경우가 자주 발생한다면, 이는 컴포넌트가 의존하는 일반적인 동작에 대해 커스텀 훅을 추출해야 한다는 신호일 수 있다.

예를 들어 위의 ChatRoom 코드는 다음과 같이 `useChatRoom`이란 커스텀 훅을 만들어 Effect의 로직을 보다 선언적인 API 뒤에 숨겨 사용할 수 있다.

```jsx
function useChatRoom({roomId, serverUrl}){
	useEffect(() => {
		const connection = createConnection(serverUrl, roomId); // 셋업 함수
		connection.connect();

		return () => {                // 클린업 함수
			connection.disconnect();
		};

	}, [serverUrl, roomId]) // 의존성 목록
}

```

그러면 다음과 같이 모든 컴포넌트에서 이렇게 사용할 수 있다.

```jsx
function ChatRoom({roomId}){
	const [serverUrl, setServerUrl] = useState('<https://localhost:1234>');

	useChatRoom({
		roomId, serverUrl
	})
}

```

### React가 아닌 위젯 제어하기

외부 시스템을 컴포넌트의 특정 prop이나 state와 동기화하고 싶을 때가 있다. 예를 들어 React 없이 작성된 타사 맵 위젯이나 비디오 플레이어 컼포넌트가 있는 경우, Effect를 사용하여 해당 state를 React 컴포넌트의 현재 state와 일치시키는 메서드를 호출할 수 있다.

```jsx
import { useRef, useEffect } from 'react';
import { MapWidget } from './map-widget.js';

export default function Map({zoomLevel}) {
	const containerRef = useRef(null);
	const mapRef = useRef(null);

	useEffect(() => {
		if(mapRef.current === null){
			mapRef.current = new MapWidget(containerRef.current);
		}

		const map = mapRef.current;
		map.setzoom(zoomLevel);

	}, [zoomLevel]);

	return(
		<div
			style={{width: 200, height: 200}}
			ref={containerRef}
		/>
	)
}

```

### Effect로 데이터 페칭하기

변수를 사용하면 `조건 경합`이 발생하지 않도록 할 수 있다. 프레임워크를 사용하는 경우, 프레임워크의 데이터 페칭 메커니즘을 사용하는게 좋고, 아니라면 effect를 이용해서 다음과 같이 할 수 있다.

아래 코드에서 `ignore`변수는 `false`로 초기화되고 클린업 중에 `true`로 설정된다. 이렇게 하면 네트워크 응답이 보낸 순서와 다른 순서로 도착하더라도 '조건 경합'이 발생하지 않는다.

```jsx
import { useState, useEffect } from 'react';
import { fetchBio } from './api.js';

export default function Page() {
	const [person, setPerson] = useState('Alice');
	const [bio, setBio] = useState(null);

	useEffect(() => {
		let ignore = false;
		setBio(null);
		fetchBio(person).then(res =>
			if(!ignore){
			setBio(res)
			}
		);

		return () => {
			ignore = true;
		}

	}, [person])
}

```

### 반응형 의존성 지정

Effect의 의존성을 선택할 수 없고, Effect 코드에서 사용되는 모든 `반응형 값`은 의존성으로 선언해야 한다.

### 반응형 값이란 ?

props와 컴포넌트 내부에서 직접 선언된 모든 변수, 함수가 포함된다. 리렌더링시 변경되는 값을 의미한다.

### Effect의 이전 state를 기반으로 state 업데이트하기

Effect의 이전 state를 기반으로 state를 업데이트하려는 경우 문제가 발생할 수 있다.

```jsx
function Counter () {
	const [count, setCount] = useState(0);

	useEffect(() => {
		const intervalId = setInterval(() => {
			setCount(count+1);
		}, 1000)

		return () => clearInterval(intervalId)l

	}, [count]);

// ...

}

```

위 코드에서 `count`는 반응형 값이므로 의존성 목록에 지정되어야 한다. 그러나 이로 인해 `count`가 변경될 때마다 effect를 다시 클린없하고 셋업해줘야 한다.

> 이 문제를 해결하려면 setCount에 state 업데이터를 전달하면 된다.

```jsx
function Counter () {
	const [count, setCount] = useState(0);

	useEffect(() => {
		const intervalId = setInterval(() => {
			setCount(c => c+1)
		}, 1000)

		return () => clearInterval(intervalId)l

	}, []);

// ...

}

```

위처럼 만들면 Effect는 더 이상 count에 의존할 필요가 없어지게 되었다. 따라서 이런식으로 업데이터 함수를 사용하면 `count`가 변경될 때마다 interval을 다시 클린업하고 셋업할 필요가 없다.

### 불필요한 객체 의존성 제거하기

Effect가 렌더링 중에 생성된 객체 또는 함수에 의존하는 경우 필요 이상으로 자주 실행될 수 있다. 예를 들어, 아래 코드에서 `options`객체는 각 렌더링 마다 다른 값이므로, 이 Effect는 매 렌더링 시 다시 연결하게 된다. 만약에 해당 컴포넌트에 input이 있다면, input에 새로운 값이 입력될 때마다 state가 바뀌어 재렌더링되므로, 그럴 때마다 계속해서 커넥션이 끊어지고 다시 생길 것이다.

```jsx
const serverUrl = '<https://localhost:1234>';

function ChatRoom({ roomId }) {
const [message, setMessage] = useState('');

const options = { // 🚩 매 리렌더마다 options 객체가 새롭게 생성된다.
	serverUrl: serverUrl,
	roomId: roomId
};

useEffect(() => {
	const connection = createConnection(options); // 여기서 사용되었다.
	connection.connect();
	return () => connection.disconnect();
}, [options]); // 🚩 매 렌더마다 다른 값이 들어간다.
// ...

```

따라서 기억하자,

[v] 렌더링 중에 생성된 객체를 의존성으로 사용하면 안된다! 대신 Effect 내에서 객체를 생성해야 한다.

```jsx
const serverUrl = '<https://localhost:1234>';

function ChatRoom({ roomId }) {
const [message, setMessage] = useState('');

useEffect(() => {
	const options = { // 🚩 roomId에 따라서 생성된다.
		serverUrl: serverUrl,
		roomId: roomId
	};

	const connection = createConnection(options);
	connection.connect();
	return () => connection.disconnect();
}, [roomId]); // 🚩 이제 input에 새로운 입력으로 인해 렌더링이 계속 일어나도, 커넥션 끊기지 않음
// ...

```

### 불필요한 함수 의존성 제거하기

Effect가 렌더링 중에 생성된 객체 또는 함수에 의존하는 경우 필요 이상으로 자주 실행될 수 있다. 예를 들어, 아래 코드에서 `createOptions`함수가 `렌더링할 대마다 다르기 때문에` 이 Effect는 렌더링 할 때마다 다시 연결된다.

```jsx
const serverUrl = '<https://localhost:1234>';

function ChatRoom({ roomId }) {
const [message, setMessage] = useState('');

const createOptions =  () => { // 🚩 매 리렌더마다 함수가 새롭게 생성된다.
	serverUrl: serverUrl,
	roomId: roomId
};

useEffect(() => {
	const options = createOptions();
	const connection = createConnection(options); // 여기서 사용되었다.
	connection.connect();
	return () => connection.disconnect();
}, [createOptions]); // 🚩 매 렌더마다 다른 값이 들어간다.
// ...

```

여기서도 객체와 같은 맥락으로 Effect안에 함수를 정의함으로써 해결하면 좋다.

```jsx
useEffect(() => {
	function createOptions(){
		return{
			serverUrl, roomId
		};
	}

	const options = createOptions();
	...

},[roomId])

```

### Effect에서 최신 props 및 state 읽기

> 2024.03.06 기준 아직 안정된 버전의 React로 출시되지 않은 실험적인 API에 대한 설명임

기본적으로 Effect에서 반응형 값을 읽을 때에는 이를 의존성으로 추가해야 한다. 이렇게 하면 Effect가 해당 값의 모든 변경에 '반응'하도록 할 수 있기 때문이다.

**그러나 때로는 Effect에 '반응'하지 않고도 Effect에서 최신 props와 state를 읽고 싶을 때가 있다.** 예를 들어, 페이지 방문 시마다 장바구니에 있는 품목의 수를 기록한다고 가정해 보자.
```jsx
function Page({url, shoppingCart}){
	useEffect(() => {
		logvisit(url, shoppingCart.length);
	}, [url, shoppingCart])
}
```
url이 변경될 때마다 새 페이지 방문을 기록하되 shoppingCart만 변경되는 경우는 기록하지 않으려면 어떻게 해야 하나 ? 반응성 규칙을 위반하지 않으면서 shoppingCart를 의존성에서 제외할 수는 없다. 그러나 코드가 Effect 내부에서 호출되더라도 변경 사항에 '반응'하지 않도록 표현할 수 있다. `useEffectEvent`훅을 활용하여 Effect Event를 선언하고 shoppingCart를 읽는 코드를 그 안으로 이동시킨다.

```jsx
function Page({url, shoppingCart}){
	const onVisit = useEffectEvent(visitedUrl => {
		logVisit(visitedUrl, shoppingCart.length)
		});

	useEffect(() => {
		onVisit(url);
	}, [url])
}
```

**Effect Event는 반응형이 아니므로 항상 Effect의 의존성에 제외해야 한다.** 이를 통해 반응형이 아닌 코드를 그 안에 넣을 수 있다. 예를 들면, `onVisit`내부에서 `shoppingCart`를 읽으면 `shoppingCart`가 Effect를 다시 실행하지 않도록 할 수 있다. 

### 서버와 클라이언트에 서로 다른 콘텐츠 표시하기
서버 렌더링을 사용하는 앱의 경우, 컴포넌트는 두 가지 다른 환경에서 렌더링된다. 서버에서는 초기 HTML을 생성하기 위해 렌더링된다. 클라이언트에서 React는 렌더링 코드를 다시 실행하여 이벤트 핸들러를 해당 HTML에 붙일 수 있도록 한다. 그렇기 때문에 hydration이 작동하려면 클라이언트와 서버의 첫 렌더링 결과가 동일해야 한다. 

드물지만 클라이언트에 다른 콘텐츠를 표시해야 하는 경우가 있다. 예를 들어, 앱이 `localStorage`에서 일부 데이터를 읽는 경우 서버에서는 이를 수행할 수 없다. 일반적으로 이를 구현하는 방법은 아래와 같다.
```jsx
function MyComponent(){
	const [didMount, setDidMount] = useState(false);

	useEffect(() => {
		setDidMount(true);
	})

	if(didMount){
		// ...return client-only JSX ...
	} else {
		// ... return initial JSX ...
	}
}
```

이렇게 할 수는 있지만 이 패턴의 사용을 되도록 아끼는게 좋다. 연결 속도가 느린 사용자는 초기 컨텐츠를 꽤 오랜 시간 동안 보게 되므로, 컴포넌트 모양이 갑작스럽게 변경되는걸 원하지 않을 것이다. 

# **Troubleshooting**
### 컴포넌트가 마운트될 때 내 Effect가 두 번 실행됩니다
- Stirct Mode가 켜져 있으면, 개발환경에서 React는 실제 셋업 전에 셋업 및 클린업을 한 번 더 실행한다.
- 개발환경에서는 셋업 -> 클린업 -> 셋업 순서로 실행되어 스트레스 체크를 한다는 것을 잊지 말자.

### 내 Effect는 리렌더링할 때마다 실행됩니다.
- 의존성 배열을 잊은 건 아닌지 확인해보자
- 의존성 배열을 콘솔에 찍은 다음, 브라우저 콘솔을 이용해 전역 변수로 저장하고, Object.is로 비교해보면 두 배열의 의존성이 동일한지 확인할 수 있다.

![[Pasted image 20240307115203.png]]
- 다시 렌더링할 때마다 다른 의존성을 발견하면 일반적으로 다음 방법 중 하나로 해결할 수 있다.
	- Effect 이전 state를 기반으로 state 업데이트하기
	- 불필요한 함수, 객체 의존성 제거하기
	- Effect에서 최신 props 및 state 읽기
- 만약 위 방법들로 해결되지 않는다면, 최후의 수단으로 `useMemo` 혹은 함수의 경우 `useCallack`으로 감싸라.

### 내 Effect가 무한히 재실행됩니다
이런 경우 다음 두 가지가 모두 참임을 의미한다.
- Effect가 일부 state를 업데이트 하고 있다.
- 이 state는 리렌더링을 일으키며, 이로부터 Effect의  의존성이 변경된다.

문제 해결 전에 먼저, Effect가 외부 시스템에 연결되어 있는지 확인해보자. Effect에 state를 설정해야 하는 이유가 무엇인가? 특정 state를 외부 시스템과 동기화하나? 아니면 애플리케이션의 데이터 흐름을 관리하려고 하나?
- 외부 시스템이 없는 경우 Effect를 완전히 제거하면 로직이 단순화되는지 고려해보자.
- 외부 시스템에 실제로 동기화 하는 경우 Effect가 state를 업데이트해야 하는 이유와 조건에 대해 생각해 보자. 렌더링에 사용되지 않는 일부 데이터를 추적해야 하는 경우, 리렌더링을 촉발하지 않는 ref가 더 적합할 수 있다. Effect가 필요 이상으로 state를 업데이트하지 않는지 확인할 필요가 있다.

마지막으로, Effect가 적시에 state를 업데이트하고 있지만 여전히 루프가 발생한다면,  해당 state 업데이트로 인해 Effect의 의존성 중 하나가 변경되기 때문일 것이다.

### 컴포넌트가 마운트 해제되지 않았는데도 클린업 로직이 실행됩니다.
클린업 기능은 마운트 해제시 뿐만 아니라 변경된 의존성과 함께 다시 렌더링하기 전에 매번 실행된다. 
클린업 코드는 있는데 그에 대응하는 셋업 코드는 없다면, 일반적으로 문제가 있는 코드이다.

```jsx
useEffect(() => {

	return () => {
		doSomething();
	}
}, [])
```
클린업 로직은 셋업 로직과 '대칭'이어야 하며, 셋업이 수행한 모든 작업을 중지하거나 취소해야 한다.

### 내 Effect가 시각적인 작업을 수행하는데, 실행되기 전에 깜박거립니다
Effect로 인해 브라우저가 화면을 그리는 것을 차단해야 하는 경우, useEffect를 `useLayoutEffect`로 바꿔라. 대부분의 Effect에는 이 기능이 필요하지 않다. 오직 브라우저 페인팅 전에 Effect를 실행하는 것이 중요한 경우에만 필요할 것이다. (예: 사용자가 보기 전에 툴팁의 위치를 미리 측정하고 배치하고자 할 때)

