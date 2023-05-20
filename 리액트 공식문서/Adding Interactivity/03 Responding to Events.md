```js

export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('You clicked on the toolbar!');
    }}>
      <Button onClick={() => alert('Playing!')}>
        Play Movie
      </Button>
      <Button onClick={() => alert('Uploading!')}>
        Upload Image
      </Button>
    </div>
  );
}
```

- 내용이 간단하여 Recap부분만 간단하게 요약

## Recap
1. <button>과 같은 요소에 함수를 prop으로 전달하여 이벤트를 처리할 수 있다.
2. 이벤트 핸들러는 호출이 아니라 전달해야 한다. onClick={handleClick}, not onClick={handleClick()}
3. 이벤트 핸들러 함수를 개별적으로 정의해도 되고, inline으로 정의해 사용해도 된다.
4. 이벤트 핸들러는 컴포넌트 내부에 정의되므로 props에 액세스 할 수 있다.
5. 부모 컴포넌트에서 이벤트 핸들러를 생성하고 자식 컴포넌트에게 prop으로 전달할 수 있다.
6. e.stopPropagation() 을 호출하여 이벤트 버블링을 방지할 수 있다.

### 나눌 내용
1. 다음과 같이 어떤 동작을 하는 것인지 naming하고 prop으로 받아와 onClick등에 붙여주면 의미 이해가 쉬운 것 같다.
```js
export default function ColorSwitch({
  onChangeColor
}) {
  return (
    <button onClick={onChangeColor}>
      Change color
    </button>
  );
}
```

2. React에서 `onScroll` 이벤트를 제외한 모든 이벤트는 전파된다.

3. 모든 이벤트는 전파되기 때문에 분석을 위해 로그를 매 클릭마다 남겨줘야 한다든지, router를 만들든지 하는 경우에는 `onClickCapture`을 사용하면 된다. 이벤트가 발생할때 onClickCapture이 먼저 불리고 그 안의 이벤트를 호출한 엘리먼트가 불리고 마지막으로 이벤트가 전파된다.
```js
	<div onClickCapture={() => { /* this runs first */ }}>  
		<button onClick={e => e.stopPropagation()} />  
		<button onClick={e => e.stopPropagation()} />  
	</div>
```