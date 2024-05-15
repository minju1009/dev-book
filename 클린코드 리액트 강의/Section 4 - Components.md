### Self Closing Tags
HTML의 void element와 헷갈리면 안된다.
결론은 JSX와 HTML을 잘 구분해야 한다.

### Fragment 지향하기
- React v16.2 에서 출시
- map을 돌릴 때에도 React.Fragment를 사용할 수 있다.

```jsx
{items.map(({id, term, description}): Element => (
		   <React.Fragment key={id}>
			   <dt>item</dt>
		   </React.Fragment>	   
))}
```

- babel 버전이 너무 낮으면 또 이런 <></> shortcut을 활용할 수 없기때문에, 리액트 버전과 babel 버전을 함께 확인해줘야 한다.

### Fragment 지양하기
- 불필요한 Fragment 굳이 사용하지 않기
- StringRender시에는 Fragment없이 반환해도 된다.
	```jsx
	function StringRender(): Element{
		✅ return 'Clean Code React';
		❌ return <>Clean Code React</>
	}
```

### 알아두면 좋은 컴포넌트 네이밍
- 일반적으로 컴포넌트는 PascalCase
- 기본 HTML 요소는 lower case
- next js - route file name은 kebeb-case일 수 있음. 그러나 내부 파일에서는 동일하게 PascalCas로 JSX를 반환한다는 표시를 해주면 좋다.

### JSX 컴포넌트 함수로 반환
```jsx
return(
	{renderMain()}
)
```
위와 같이 함수로 return하는 경우 다음과 같은 단점이 있을 수 있다.
1. scope를 알아보기 어렵다.
2. 반환 값을 바로 알기 어렵다.
3. props 전달 등 일반적인 패턴이 아니다.

### 컴포넌트 내부에 컴포넌트 선언
1. 결합도가 증가한다.
	- 구조적으로 스코프적으로 종속된 개발이 된다.
	- 나중에 확장성이 생겨서 분리될때 굉장히 힘들어진다.
2. 성능 저하
	- 상위 컴포넌트 리렌더 ? => 하위 컴포넌트 재생성

### displayName
devtool을 사용할 때 어떤 컴포넌트인지 알아보기 쉬워 디버깅이 쉬워진다.
```jsx
const InputText = forwardRef((props, ref): Element => {
	return <input type="text" ref={ref} />;						 
});

InputText.displayName = 'InputText';
```

```jsx
const withRouter = (Component): {(props:any): Element} => {

	const WithRouter = (props): Element => {
		
	return (
		<Component
			{...props}
			location={location}
		/>	
	)}
WithRouter.displayName = Component.displayName ?? Component.name ?? 'WithRouterComponent';

return WithRouter;
}
```

### 컴포넌트 구성하기
```jsx
// 변하지 않을 상수
const DEFAULT_COUNT = 100;

interface SomeComponentProps{

}

const SomeConponent = ({props1,props2} : SomeComponentProps) => {
	let isHold = false; // flag성

	const ref = useRef(null);

	const location = useLocation();
	const queryClient = useQueryClient();
	const state = useSelector((state) => state);

	const state = useCustomHooks((state) => state);

	const [state, setState] = useState("someState");

	const onClose = () => handleClose();

	// Early Return JSX
	if(isHold){
		return <div>데이터가 존재하지 않습니다.</div>
	}

	// Main JSX와 가장 가까운 곳에 위치
	useEffect(() => {
	}, []);

	// JSX 반환은 항상 사전에 개행을 동반
	return(
		<div></div>
	)

}
```