- 이벤트 핸들러와 Effect 중에서 선택하는 방법
- Effect는 반응형이고 이벤트 핸들러는 반응형이 아닌 이유
- Effect 코드의 일부가 반응하지 않기를 원할 때 해야 할 일
- Effect Event란 무엇이며 Effect에서 추출하는 방법
- Effect Event를 사용하여 Effect에서 최신 props 및 state를 읽는 방법

## 이벤트 핸들러와 Effect 중 선택하기
코드가 실행되어야하는 이유를 생각해봐야 한다.

이벤트 핸들러는 특정 상호 작용에 대한 응답으로 실행된다.
Effect는 동기화가 필요할 때마다 실행된다.

## 반응형 값 및 반응형 로직
컴포넌트 본문 내부에 선언된 props, state, 변수를 `반응형 값`이라고 한다. 다음과 같은 코드에서 serverUrl은 반응형 값이 아니지만, roomId, message는 반응형 값이다. 이들은 렌더링 데이터 흐름에 참여한다.
```jsx
const serverUrl = 'https://localhost:1234';  

function ChatRoom({ roomId }) {  
const [message, setMessage] = useState('');  


// ...  

}
```

- 이벤트 핸들러 내부의 로직은 반응형이 아니다. 사용자가 동일한 상호작용(예: 클릭)을 수행하지 않는 한 다시 실행되지 않기 때문이다.
- 그러나 Effects 내부의 로직은 반응형이다. 

## Effect에서 비반응형 로직 추출하기
반응형 로직과 비반응형 로직을 함께 사용하려는 경우를 살펴보자.

예를 들어, 사용자가 채팅에 연결할 때 알림을 표시하고 싶다고 가정해 보자. props에서 현재 테마(dark or light)를 읽어 올바른 색상으로 알림을 표시한다.

아래 코드에는 문제가 있다! theme을 바꿀 때에도 계속해서 채팅이 다시 연결된다. 이렇게 Effect안에 있더라도 반응형 Effect가 되지 않도록 하려면 어떻게 해야 하는가?

```jsx
function ChatRoom({ roomId, theme }) {
  useEffect(() => {
    const connection = createConnection(serverUrl, roomId);
    connection.on('connected', () => {
      showNotification('Connected!', theme);
    });
    connection.connect();
    return () => connection.disconnect();
  }, [roomId, theme]);
```

### Effect Event 선언하기
`useEffectEvent`라는 특수한 Hook을 사용할 수 있다.
```jsx
function ChatRoom({ roomId, theme }) {  

const onConnected = useEffectEvent(() => {  
	showNotification('Connected!', theme);  
});  

  

useEffect(() => {  
	const connection = createConnection(serverUrl, roomId);  
	connection.on('connected', () => {  
		onConnected();  
	});  

	connection.connect();  

	return () => connection.disconnect();  

}, [roomId]); // ✅ All dependencies declared  

// ...
```

Effect Event는 이벤트 핸들러와 매우 유사하다고 생각할 수 있다. 가장 큰 차이점은 이벤트 핸들러는 사용자 상호작용에 대한 응답으로 실행되는 반면, Effect Event는 Effect에서 사용자가 촉발한다는 점이다. Effect Event를 사용하면 Effect의 반응성과 반응형으로 동작해서는 안 되는 코드 사이의 “사슬을 끊을 수 있다.

## Effect Event로 최신 props 및 state 읽기
다음과 같은 코드에서 장바구니에 담긴 물품의 숫자를 나타내는 numberOfItems는 의존성 배열에 넣으면 안되지만, url을 이동할 때마다 함께 로그에 찍히기를 원한다.

```jsx
function Page({ url }) {  

const { items } = useContext(ShoppingCartContext);  

const numberOfItems = items.length;  

  
useEffect(() => {  
	logVisit(url, numberOfItems);  
	}, [url]); // 🔴 React Hook useEffect has a missing dependency: 'numberOfItems'  

// ...  

}
```

그럴 때 이 Effect event를 사용해서 다음과 같이 작성할 수 있다.
```jsx
function Page({ url }) {  
	
const { items } = useContext(ShoppingCartContext);  

const numberOfItems = items.length;  


const onVisit = useEffectEvent(visitedUrl => {  
	logVisit(visitedUrl, numberOfItems);  
});  

  
useEffect(() => {  
	onVisit(url);  
}, [url]); // ✅ All dependencies declared  

// ...  

}
```

