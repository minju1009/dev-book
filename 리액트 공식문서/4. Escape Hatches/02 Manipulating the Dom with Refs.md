> React는 렌더링 출력과 일치하도록 DOM을 자동으로 업데이트하므로 컴포넌트가 DOM 을 직접 조작할 일은 자주 없을 것이다. 그러나 때로는 노드에 focus하거나 스크롤하거나, 크기와 위치를 측정하기 위해 React가 관리하는 DOM 요소에 접근해야 할 수도 있다. React에는 이러한 작업을 수행할 수 있는 빌트인 된 방법이 없으므로 DOM 노드에 대한 ref가 필요하다.

- `ref` 어트리뷰트로 React가 관리하는 DOM 노드에 접근하는 방법
- `ref` JSX 속성이 `useRef` 훅과 관련되는 방법
- 다른 컴포넌트의 DOM 노드에 접근하는 방법
- 어떤 경우에 React가 관리하는 DOM을 수정해도 안전한지

## ref 어트리뷰트로 React가 관리하는 DOM 노드에 접근하는 방법
```js
import {useRef} from 'react';

const myRef = useRef(null);

<div ref={myRef}>
```
`useRef`훅은 `current`라고 하는 프로퍼티가 포함된 객체를 반환한다. 처음에는 null을 저장하고 있다가 React가 `div`에 대한 DOM 노드를 생성하면, React는 이 노드에 대한 참조는 `myRef.current`에 넣는다. 그런 다음 이벤트 핸들러에서 이 DOM 노드에 액세스하고 여기에 정의된 빌트인 브라우저 API를 사용할 수 있다.
```js
// 모든 브라우저 API를 사용할 수 있다.
myRef.current.scrollIntoView();
```

## ref 콜백을 사용하여 refs 목록을 관리하는 방법
한 컴포넌트에 여러개의 ref가 필요할 때가 있을 수 있다. 항목 수를 미리 안다면 ref를 그에 맞게 잡아두고 사용할 수 있지만, 예를 들어 데이터를 받아와 list를 map을 통해 그려주는 경우, 얼마나 많은 ref가 필요할지 알 수 없는 경우가 발생한다. 
```js
<ul>
	{items.map((item) => {
		// 작동하지 않는다!!
		const ref = useRef(null);
		return <li ref={ref} />;
	})}
</ul>
```
이는 훅은 컴포넌트의 최상위 레벨에서만 호출해야 하고, 반복문 또는 map() 내부에서는 useRef를 호출할 수 없기 때문이다. 이 문제를 해결할 수 있는 방법 중 하나로는 부모 엘리먼트에 대한 단일 ref를 가져온 다음 `querySelectorAll`과 같은 DOM 조작 메서드를 사용하여 개별 하위 노드를 '찾는'것이다. 그러나 이 방법은 DOM 구조가 변경되면 깨질 수 있다.

또 다른 해결책은 `ref`속성에 함수를 전달하는 것이다. 이를 `ref 콜백`이라고 한다. 리액트는 ref를 설정할 때가 되면 DOM 노드로 콜백을 호출하고, 지울 때가 되면 null로 ref 콜백을 호출할 것이다. 이를 통해 사용자가 자신만의 배열이나 Map을 유지 관리하고, 인덱스나 일종의 ID로 모든 ref에 접근할 수 있다.

https://codesandbox.io/s/1dwe75?file=%2FApp.js&utm_medium=sandpack

```js
<ul>
	{catList.map(cat => (
		<li
			key={cat.id}
			ref={(node) => {
				const map = getMap();
				if (node) {
					map.set(cat.id, node);
				} else {
					map.delete(cat.id);
				}
		}}>
...
```


이 예제에서 ref는 DOM노드로의 Map을 보유한다. 이렇게 하면 나중에 Map에서 개별 DOM 노드를 읽을 수 있다.

## 다른 컴포넌트의 DOM 노드에 접근하기
<input />과 같은 브라우저 엘리먼트를 출력하는 빌트인 컴포넌트에 ref를 넣으면, React는 해당 ref의 current 프로퍼티를 해당 DOM 노드(예: 브라우저의 실제 <input />)으로 설정한다.

그러나 `<MyInput />`과 같은 내가 만든 컴포넌트에 ref를 넣으려고 하면 기본적으로 `null`이 반환된다. 

아래 코드는 잘 작동하지 않는다.
```js
function MyInput(props){
	return <input {...props} />;
}

export default function MyForm(){
	const inputRef = useRef(null);

	function handleClick(){
		inputRef.current.focus();
	}

	return (
		<>
			<MyInput ref={inputRef} />
			<button onClick={handleClick}>
				Focus the input
			</button>
		</>
	)

}
```
이는 기본적으로 React가 컴포넌트가 다른 컴포넌트의 DOM 노드에 접근하는 것을 허용하지 않기 때문이다. 다른 컴포넌트의 DOM 노드를 수동으로 조작하면 코드가 취약해지기 때문이다. 대신, DOM 노드를 노출하길 원하는 컴포넌트에 해당 동작을 설정해야 한다. 컴포넌트는 자신의 ref를 자식 중 하나에 '전달'하도록 지정할 수 있다.

```js
const MyInput = forwardRef((props, ref) => {
	return <input {...props} ref={ref} />;
})

export default function Form(){
	const inputRef = useRef(null);

	return (
		<>
			<Myinput ref={inputRef} />
			...
	)

}
```
1. `<MyInput ref={inputRef} />`는 React에게 해당 DOM 노드를 inputRef.current에 넣으라고 지시한다. 그러나 이를 선택할지는 `MyInput`컴포넌트에 달려 있으며, 기본적으로 그렇지 않다.
2. `MyInput`컴포넌트를 `forwardRef`를 사용하여 선언하면, `props`다음 두 번째 ref 인수에 위의 inputRef를 받도록 설정된다.
3. 수신한 ref를 내부의 input으로 전달한다.

## 명령형 핸들로 API의 하위 집합 노출하기
위의 예시에서 MyInput은 원본 DOM input 엘리먼트를 노출한다. 이를 통해 부모 컴포넌트가 이 요소에 focus()를 호출할 수가 있다. 그런데 이렇게 하면 부모 컴포넌트가 다른 작업(예: CSS 스타일 변경)을 할 수도 이다. 드문 경우지만 노출되는 기능을 제한하고 싶을 수도 있다. 그럴 땐 `useImperativeHandle`을 사용하면 된다.

```js
import {forwardRef, useRef, useImperativeHandle} from 'react';

const MyInput = forwardRef((props, ref) => {
	const realInputRef = useRef(null);
	useImperativeHandle(ref, () => ({
		// focus만 가능하도록 노출하고 싶을 때,
		focus() {
			realInputRef.current.focus();
		}
	}));

	return <input {...props} ref={realInputRef} />

})
```
`realInputRef`는 실제 DOM 노드를 보유하지만 `useImperativeHandle`은 부모 컴포넌트에 대한 ref 값으로 고유한 특수 객체를 제공하도록 React에게 지시한다. 따라서 `Form` 컴포넌트 내부의 `inputRef.current`에는 `focus`메서드만 있게 된다. 이 경우 ref '핸들'은 DOM 노드가 아니라 `useImperativeHandle()`내부에서 생성한 사용자 정의 객체이다.

## FlushSync를 사용하여 동기식으로 업데이트하기
https://react-ko.dev/learn/manipulating-the-dom-with-refs#when-react-attaches-the-refs
```js
setTodos([...todos, newTodo]);
listRef.current.lastChild.scrollIntoView(); // 마지막 전의 아이템으로 스크롤됨
```
React에서 이 동작을 했을 때 state 업데이트가 큐에 등록되고 setTodos가 DOM을 즉시 업데이트 하지 않기 때문이다. 이 문제를 해결하기 위해서 react-dom에서 flushSync를 import하고 state업데이트를 flushSync호출로 감싸면 동기적으로 업데이트 하도록 지시할 수 있다.

```js
flushSync(() => {
	setTodos([...todos, newTodo]);
});
listRef.current.lastChild.scrollIntoView();
```
이렇게 하면 `flushSync`로 감싼 코드가 실행된 직후 React가 DOM을 동기적으로 업데이트하도록 지시한다. 그 결과 스크롤을 시도할 때 DOM에 이미 마지막 할 일이 있을 것이다.

