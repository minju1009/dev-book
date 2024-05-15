### Hooks API 소개
- HOC : 컴포넌트 로직을 재사용하기 위한 React의 고급기술
###  함수 컴포넌트와 클래스 컴포넌트의 차이점
- 가장 큰 차이점은 함수의 재사용 관점이다. 클래스 컴포넌트에서 코드를 재사용하려면 컴포넌트를 감싸고 또 감싸는 고차 컴포넌트의 방식으로 사용하게 되는데, 래퍼 헬(wrapper hell)이 발생하기 쉽다. 래퍼 헬은 데이터를 추적하기 어렵고, 재사용이 어려우며, 컴포넌트를 비대하게 만든다. 
- 훅스의 등장으로 코드의 재사용이 쉽게 되었다. 클래스 컴포넌트에서 함수 컴포넌트로 넘어가게 된 계기는 바로 훅스의 등장으로 인해 코드 재사용이 쉽게 되었기 때문이다.

- 그 외 차이점으로,  클래스 컴포넌트는 라이프 사이클이 있고, 함수컴포넌트는 라이프 사이클이 없다.
- 클래스 컴포넌트는 render()안에서 return하고, 함수 컴포넌트는 바로 return한다.
- https://www.youtube.com/watch?v=dpw9EHDh2bM

###  stateful 컴포넌트와 stateless 컴포넌트는 어떤 차이가 있을까?
- stateful 컴포넌트는 컨테이너 컴포넌트로 데이터 로직을 관리하는 컴포넌트이다. 어떤 데이터가 사용자에게 보여질 지에 대해 다루는 컴포넌트이다.
- stateless 컴포넌트는 프레젠테이션 컴포넌트로 뷰를 다루는 컴포넌트이다. 데이터가 어떻게 다뤄져야 하는지에 대해서 다룬다.

### Container/Presentational 패턴과 리액트가 무슨 관련이 있는가?
- SoC - separation of concerns, 관심사를 분리하기 위한 방법으로 Container/Presentational 패턴이 등장했다. 
- 대개 Container/Presentational 패턴은 React Hooks로 대체 가능하다. React에 Hooks가 추가되면서 클래스형 컴포넌트를 사용하지 않고도, 즉 Container 컴포넌트 없이도 stateless 컴포넌트를 쉽게 만들 수 있게 되었다. 

### useEffect() 기명 함수와 함께 사용하기
useEffect를 기명함수와 함께 쓰면 디버깅이 쉬워진다.
```jsx
useEffect (function onPopState() {
	if(navigationType === 'POP'){
		// some logic
	}
}, [navigationType])
```

### 한 가지 역할만 수행하는 useEffect()
- 콜백에 기명 함수를 사용해 보자
- dependency Arrays에 너무 많은 관찰 대상이 들어가고 있는게 아닌가 살펴보자.

### Custom Hooks 반환의 종류
```jsx
function ReturnCustomHooks(){
	// ❌
	const [setValue, value] = useSomeHooks(true);

	// ❌
	const [oneValue] = useSomeHooks();

	// ❌ - 대신 객체 사용하기
	const [firstValue, secondValue, _, thirdValue] = useSomeHooks(true); 

	객체로 만들어서 사용하면 편하다!
}
```

### useEffect 내부의 비동기 함수
```jsx
// ❌
useEffect(async () => {
	// 비동기 작업
	const result = await fetchData();
}, []);


// ✅
useEffect(() => {
	consst fetchData = async() => {
		const result = await someFetch();
	}

	fetchData();
}, [dependency])
```