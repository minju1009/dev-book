- 컴포넌트에서 불필요한 Effect를 제거하는 이유와 방법
- Effect 없이 값비싼 계산을 캐시하는 방법
- Effect 없이 컴포넌트 state를 리셋하고 조정하는 방법
- 이벤트 핸들러 간에 로직을 공유하는 방법
- 이벤트 핸들러로 이동되어야 하는 로직
- 부모 컴포넌트에 변경 사항을 알리는 방법

## 불필요한 Effect를 제거하는 방법

- 렌더링을 위해 데이터를 변환하는 경우 Effect는 필요하지 않다.
- 사용자 이벤트를 처리하는 데에 Effect는 필요하지 않다.

## props 또는 state에 따라 state 업데이트하기

fullName을 구하는 것을 굳이 state를 만들 필요 없다. state를 만들 경우 오히려 waterfall 현상때문에 느려지기만 하고, 프로그램이 복잡해진다.

```
function Form() {
	const [firstName, setFirstName] = useState('Taylor');
	const [lastName, setLastName] = useState('Swift');

	// 좋습니다~!! 렌더링 과정 중에 계산
	const fullName = firstName + ' ' + lastName

}

```

## 고비용 계산 캐싱하기

연산이 무겁지 않다면 렌더링 중에 계산하도록 하고, 무겁다면 useMemo로 감싸 캐싱하도록 한다. useMemo로 감싸면 의존성 배열에 있는 값이 변경되지 않는 한 내부 함수가 다시 실행되지 않기를 원한다는 것을 React에게 알릴 수 있다.

계산이 비싼지 안비싼지 확인하려면 console.time을 통해서 해당 계산이 얼마나 시간이 걸리는지 확인해 볼 수 있다.

```
console.time('filster array');
const visibleTodos = getFilteredTodos(todos, filter);
console.timeEnd('filter array');

```

일반적으로 기록된 시간이 1ms이상 정도에 해당한다면 해당 계산은 메모해 두는 것이 좋을 수 있다.

useEffect안에서 데이터 페칭을 했을때, 앞에 불렀던 호출이 뒤에 불렀던 호출의 응답 이후에 도착할 수 있다. 이를 '경쟁 조건'이라고 하는데, 서로 다른 두 요청이 서로 '경쟁'하여 예상과 다른 순서로 도착하는 경우를 의미한다.

> 경쟁 조건을 수정하기 위해서는 오래된 응답을 무시하도록 클린업 함수를 추가해야 한다.

## prop이 변경되면 모든 state 재설정하기

내려받은 prop을 effect를 통해 변경하기보다 key를 이용하여 다른 state로 인식할 수 있도록 해 주어 초기화를 해주는게 좋다.

```jsx
export default function ProfilePage({ userId }) {

const [comment, setComment] = useState('');

// 🔴 Avoid: Resetting state on prop change in an Effect
// 🔴 이러지 마세요: prop 변경시 Effect에서 state 재설정 수행

useEffect(() => {
	setComment('');
}, [userId]);

// ...

}

```

이런 식으로 만들면, userId가 바뀔때에, 일단 오래된 state로 먼저 렌더링한 다음 새로운 값으로 다시 렌더링하기 때문에 비효율적이다. 그 대신, 명시적인 키를 전달해 컴포넌트를 둘로 나누고 바깥쪽에서 안쪽 컴포넌트로 `key`속성을 전달하면 좋다.

```jsx
export default function ProfilePage({ userId }) {
	return (
		<Profile
			userId={userId}
			key={userId}
		/>

	);
}

function Profile({ userId }) {
// ✅ This and any other state below will reset on key change automatically
// ✅ key가 변하면 이 컴포넌트 및 모든 자식 컴포넌트의 state가 자동으로 재설정됨
const [comment, setComment] = useState('');
// ...

}

```

## props가 변경될 때 일부 state 조정하기

prop이 변경될 때, state의 일부를 조정하고 싶을 수 있다. 이 때 useEffect를 이용해 prop에 따라서 변경되도록 하기보다는, 차라리 렌더링 중에 계산하도록 하는게 효율적일 수 있다. effect를 이용하여 위 경우와 마찬가지로 오래된 state에 대해서 먼저 렌더링이 일어나기 때문이다.

```jsx
function List({ items }) {
const [isReverse, setIsReverse] = useState(false);
const [selection, setSelection] = useState(null);

// Better: Adjust the state while rendering
// 더 나음: 렌더링 중에 state 조정
const [prevItems, setPrevItems] = useState(items);
if (items !== prevItems) {
	setPrevItems(items);
	setSelection(null);
}

// ...

}

```

그러나 이것도 최선의 방법이 아니다. 어떻게 하든 props나 state를 바탕으로 state를 조정하면 데이터 흐름을 이해하고 디버깅 하기가 어려워진다. 항상 key로 모든 state를 재설정하거나 렌더링 중에 모두 계산할 수 있는지를 확인해야 한다. 예를 들어, item 전체를 저장하는 대신, 선택한 item의 ID 를 저장할 수 있다.

```jsx
function List({ items }) {

const [isReverse, setIsReverse] = useState(false);
const [selectedId, setSelectedId] = useState(null);
// ✅ Best: Calculate everything during rendering
// ✅ 가장 좋음: 렌더링 중에 모든 값을 계산

const selection = items.find(item => item.id === selectedId) ?? null;

// ...

}

```

이렇게 하면 선택한 ID를 가진 항목이 목록에 있으면 state가 그대로 유지된다. 이 방식은 items에 대한 변경과 무관하게, selection 항목은 그대로 유지되므로 대체로 더 나은 방법이다.

## 이벤트 핸들러 간 로직 공유
두 군데에서 같은 로직을 불러야 돼서 effect를 사용하고 싶을 수도 있지만, 좋은 생각이 아니다! 컴포넌트가 사용자에게 표시되었기(렌더) 때문에 실행되어야 하는 코드에만 Effect를 사용해야 한다.

## POST요청 보내기
무언가 이벤트가 발생했을 때 발생하는 거라면 무조건 이벤트 핸들러에 넣어야 한다.

## 연쇄 계산
때로는 다른 state를 바탕으로 또다른 state를 조정하는 Effect를 연쇄적으로 사용하고 싶을 수도 있다.
그러나 이렇게 하면 비효율적이다. 계속해서 렌더링이 연쇄적으로 발생하기 때문이다. 따라서 이렇게 하는 것보다는 렌더링 과정에서 계산 가능한 것은 계산을 하고, 이벤트 핸들러에서 state를 조정하는 것이 좋다.

## 애플리케이션 초기화하기
일부 로직은 앱이 로드될 때 한 번만 실행되어야 한다. 이 때, 최상위 컴포넌트의 Effect에 배치하고 싶을 수도 있다.
```jsx
function App() {  

// 🔴 Avoid: Effects with logic that should only ever run once  
// 🔴 이러지 마세요: 한 번만 실행되어야 하는 로직이 포함된 Effect  

useEffect(() => {  
	loadDataFromLocalStorage();  
	checkAuthToken();  
}, []);  

// ...  

}
```
그러나 이 함수가 개발 중에 두 번 실행될텐데, 인증 토큰이 무효화 되는 듯 문제가 발생할 수 있다. 상용환경에는 이런 문제가 발생하지 않을 수도 있지만, 모든 컴포넌트에서 동일한 제약 조건을 따르면 코드를 이동하고 재사용하기가 더 쉬워진다. 만약 일부 로직이 마운트당 한 번이 아니라, **앱 로드당 한 번** 실행되어야 하는 경우, 최상위 변수를 추가하여 이미 실행되었는지 여부를 추적하면 좋다.
```jsx
let didInit = false;

function App(){
	useEffect(() => {
		if(!didInit){
			didInit = true;
			loadDataFromLocalStorage();
			checkAuthToken();
		}
	
	}, [])

}
```

## 부모에게 데이터 전달하기
React에서 데이터는 부모 컴포넌트에서 자식 컴포넌트로 흐른다. 이렇게 되어야만 데이터 흐름을 추적하기 쉽다. 따라서 자식 컴포넌트가 Effect에서 부모 컴포넌트의 state를 업데이트하면, 데이터 흐름을 추적하기가 매우 어려워진다. 자식과 부모 컴포넌트 모두 동일한 데이터가 필요하므로, 대신 부모 컴포넌트가 해당 데이터를 페치해서 자식에게 전달하도록 하는게 좋다.

## 외부 스토어 구독하기
외부 스토어를 구독해야 할 때가 올 수도 있다. 그럴 땐 Effect보다는 `useSyncExternalStore`라는 훅을 사용하면 좋다.

## 데이터 페칭하기
검색어를 입력하여 이를 기반으로 effect에서 fetching을 한다고 했을 때, 서로 다른 두 요청이 "경쟁"하여 예상과 다른 순서로 데이터가 호출될 수 있다. 이를 수정하기 위해서는 오래된 응답을 무시하도록 클린업 함수를 추가해야 한다.