## Effect는 무엇이며 이벤트와는 어떻게 다른지
- 이벤트 핸들러 : 컴포넌트 내부에 있는 중첩된 함수로, 이를 통해 입력 필드를 업데이트하거나, HTTP POST 요청을 하여 제품을 구매하거나, 사용자를 다른 화면으로 이동할 수 있다. 이벤트 핸들러에는 특정 사용자 작업으로 인해 발생하는 '사이드 이펙트'(프로그램의 state를 변경함)가 포함되어 있다.
- Effect : Effect를 사용하면 특정 이벤트가 아닌 렌더링 자체로 인해 발생하는 사이드 이펙트를 명시할 수 있다. Effect는 화면 업데이트 후 커밋이 끝날 때 실행된다. 이 때가 React 컴포넌트를 일부 외부 시스템(네트워크 또는 서드파티 라이브러리와 같은)과 동기화하기에 좋은 시점이다.

## You might not need and Effect
컴포넌트에 Effect를 추가하고자 서두르지 말길, Effect는 일반적으로 React의 코드에서 벗어나 일부 외부 시스템과 동기화하는 데에 사용된다는 점을 명시해야 한다.

```js
import {useEffect} from 'react';

function MyComponent(){
	useEffect(() => {
		// 여기의 코드는 매 렌더링 후에 실행된다.
	});
	return <div />;
}
```
컴포넌트가 렌더링 될 때마다 React는 화면을 업데이트하고 useEffect 내부의 코드를 실행한다. 즉 `useEffect`는 해당 렌더링이 화면에 반영이 될 때까지 코드 조각의 실행을 '지연'한다.

```js
function VideoPlayer({src, isPlaying}){
	const ref = useRef(null);

	useEffect(() => {
		if(isPlaying){
			ref.current.play();
		}else{
			ref.current.pause();
		}
	});

	return <video ref={ref} src={src} loop playsInline />

}
```
DOM 업데이트를 Effect로 감싸면, React가 먼저 화면을 업데이트 하도록 할 수 있다. 그 다음 Effect가 실행된다.

### 주의! 잘못 사용하면 무한 루프를 생성할 수 있다.
```js
const [count, setCount] = useState(0);
useEffect(() => {
	setCount(count+1);
})
```

## Effect에 의존성 지정하기
기본적으로 Effect는 매번 렌더링 후에 실행되지만, 이를 원치 않는 경우가 있다.
이럴 때에는 의존성 배열을 사용해 주면 된다!
> 의존성 배열은 여러 개의 의존성을 포함할 수 있다. React는 지정한 모든 의존성의 값이 이전 렌더링 때와 정확히 동일한 경우에만 Effect의 재실행을 건너뛴다. React는 `Object.is` 비교를 사용해 의존성 값을 비교한다. 

```js
useEffect(() => {  

// This runs after every render  
// 렌더시마다 실행됩니다.  

});  

  

useEffect(() => {  

// This runs only on mount (when the component appears)  
// 오직 마운트시(컴포넌트가 나타날 때)에만 실행됩니다.  

}, []);  

  

useEffect(() => {  

// This runs on mount *and also* if either a or b have changed since the last render  
// 마운트시 뿐만 아니라 a 또는 b가 직전 렌더와 달라졌을 때에도 실행됩니다.  

}, [a, b]);
```

## 필요한 경우 클린업 추가하기
컴포넌트가 '마운트'될 때, 즉 화면에 처음 나타날 때만 어떤 코드를 실행하도록 React에 지시할 수 있다. 
```js
useEffect(() => {
	const connection = createConnection();
	connection.connect();
},[])
```
위와 같이 코드를 작성했을 때, 콘솔에서 mounting이 한번만 찍힐 것으로 예상하지만 실제 찍어보면 두 번이 찍힌다. 그 이유는 React가 개발 과정에서 코드에 버그가 있는지 검사하기 때문에 연결/해제 호출 쌍이 추가되기 때문이다.

개발 환경에서만 한번 디버깅을 위해 한번 더 호출되고, 상용환경에서는 mount된 이후 한번만 호출되기 때문에 신경쓰지 않아도 된다. 

## Effect를 사용하는 경우
- 이벤트 구독하기
- 애니메이션 촉발하기
- 데이터 페칭하기

## Effect에서 데이터를 페칭하는 것의 대안은?
- Effects는 서버에서 실행되지 않는다. 즉, 초기 서버에서 렌더링되는 HTML에는 데이터가 없는 로딩 state만 포함된다. 클라이언트 컴퓨터는 모든 JavaScript를 다운로드하고 앱을 렌더링하고 나서야 비로소 데이터를 로드해야 한다는 사실을 발견할 텐데, 이는 효율적이지 않다.
- Effect에서 호출하면 '네트워크 워터폴'이 만들어지기 쉽다. 상위 컴포넌트 렌더링시, 상위 컴포넌트가 일부 데이터를 페치하고, 하위 컴포넌트를 렌더링하고, .... 네트워크가 매우 빠르지 않다면, 모든 데이터를 병렬로 페치하는 것보다 훨씬 느리다.
- Effect에서 직접 페치하는 것은 일반적으로 데이터를 미리 로드하고 캐시하지 않음을 의미한다. 컴포넌트가 마운트 해제되었다가 다시 마운트되면, 데이터르르 다시 페치할 것이다.
- 프레임워크를 사용하는 경우 빌트인 데이터 페칭 메커니즘을 사용하여라.
- 그렇지 않으면 클라이언트측 캐시를 사용하거나 직접 구축하는 것을 고려해라. 오픈 솔루션으로는 React Query, useSWR 등이 있다.