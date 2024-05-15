### JSX에서의 공백처리
- 공백은 {" "}로 처리할 수 있다.

### 0은 JSX에서 유효한 값
```jsx
❌ {items.length && item.map...}
✅ {items.length > 0 && items.map ...}
```

### 리스트 내부에서의 Key
- 리액트 입장에서는 virtual DOM을 사용하기 때문에, 고유의 구분자가 없으면 리액트 입장에서 실제 DOM을 구분하기가 어렵다.
- `<li key={new Date().toString()}>{item}</li>`이런 식으로 인라인으로 넣는 것은 최악이다. 매 렌더링마다 다른 key가 생성되기 때문이다.
- 렌더링 전에 고유한 random ID를 생성하는 것이 좋은 방법이다.
```jsx
function KeyInList({list}): Elemnt{

	const handleAddItem = value : void => {
		setItems((prev) => [
			...prev, {
			id: crypto.randomUUID}
		])
		
	}
}
```

### 안전하게 Raw HTML 다루기
- HTML Sanitizer API
- DOMPurify 라이브러리
- eslint-plugin-risxx를 사용하면 XSS 공격의 위험을 막을 수 있다.ㄷ