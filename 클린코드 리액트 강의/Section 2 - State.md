## 연관된 상태 단순화하기
Keep It Simple Stupid - KISS
리액트의 상태를 만들 때 `연관된 것들끼리 묶어서 처리`하면 에러를 방지하고 코드가 간결해진다.

```jsx
// ❌
const [isLoading, setIsLoading] = useState(false);
const [isFinish, setIsFinish] = useState(false);


// ✅
const PROMISE_STATE = {
	PENDING : 'pending',
	LOADING : 'loading',
	FINISH: 'finish',
	ERROR:'error
}

const [promiseState, setPromiseState] = useState(PROMISE_STATE.PENDING);

```

## 연관된 상태 객체로 묶어내기
한 가지 상태로 묶어서 관리. 무조건 1 state : 1 useState일 필요 없다. 연관된 것들은 객체로 묶으면 불필요한 state를 줄일 수 있다.
```jsx
// ❌
const [isLoading, setIsLoading] = useState(false);
const [isFinish, setIsFinish] = useState(false);

// ✅

setFecthState((prevState) => {...prevState, isLoading: true})

const [fetchState, setFetchState] = useState({
	isLoading: false,
	isFinish: false,
	isError: false,
})

if(fetchState.isLoading) return <LoadingState />
```

## useState에서 useReducer로 리팩터링
```jsx
// 구조화된 상태를 원한다면! useReducer
// 복잡한 상태를 관리할 때 잘 추상화되어 있으면 사용하기 쉽다.

const INIT_STATE = {
	isLoading: false,
	isSuccess: false,
	isFail: false
} 

const reducer = (state, action) => {
	switch(action.type){
		case 'FETCH_LOADING':
			return {isLoading: true, isSuccess: false, isFail : false}
		case 'FETCH_SUCCESS':
			return {isLoading: false, isSuccess: true, isFail : false}
		case 'FETCH_FAIL':
			return {isLoading: false, isSuccess: false, isFail : true}		

		default:
			return INIT_STATE
	}
}

function StateToReducer(){
	const [state, dispatch] = useReducer(reducer, INIT_STATE)

	dispatch({type: 'FETCH_SUCCESWS}); // 이런 식으로 작성
	// 내부 로직이 모두 추상화 되어 있기 때문에 상태 변경에 따른 관리를 간단하게 할 수 있다.
}
```

## 상태 로직  Custom Hooks로 뽑아내기
로직만 별도로 빼서 커스텀훅으로 만들 수 있다.
```jsx
const useFetchData = (url) => {
 	const [state, dispatch] = useReducer(reducer, INIT_STATE);

	const fetchData = () => {
		// fetch ...
	}

	return state;
}
```

## 이전 상태 활용하기
```jsx
// ❌
setAge (age + 1);

// ✅
setAge((prevAge) => prevAge + 1);

function updaterFunction () {
	setAge((prevAge) => prevAge + 1); // 42 => 43
	setAge((prevAge) => prevAge + 1); // 43 => 44
	setAge((prevAge) => prevAge + 1); // 44 => 45
}

// 이는 객체로 된 상태이트를 업데이트할 떄도 마찬가지! 이전 상태를 활용해야 예상치 못한 버그를 줄일 수 있다.

```
