> 앱이 성장함에 따라 state 관리를 확장하는 방법

> Reducer를 사용하면 컴포넌트 state 업데이트 로직을 통합할 수 있다. Context를 사용하면 다른 컴포넌트들에 정보를 전달할 수 있다. Reducer와 context를 함께 사용하여 복잡한 화면의 state를 관리할 수 있다.

## Reducer와 context 결합하기
- Reducer를 사용하면 이벤트 핸들러를 간결하고 명확하게 만들 수 있다. 그러나 앱이 커질수록 다른 어려움에 부딪힐 수 있다. 05에서 봤던 예제처럼 현재 tasks state와 dispatch 함수를 최상위 컴포넌트에서만 사용할 수 있다. 다른 컴포넌트들에서 tasks의 리스트를 읽고 변경하려면 prop을 통해 현재 state와 state를 변경할 수 있는 이벤트 핸들러를 명시적으로 전달해야 한다.
- 이런 경우에 props로 전달, 전달, 전달...하는 props drilling현상이 나타날 수 있는데 반복적인 props drilling 없이 context에 넣어 사용하면 context 하위의 모든 컴포넌트 트리에서 tasks를 읽고 dispatch 함수를 실행할 수 있게 된다.
- 아래와 같이 세 단계로 나누어 reducer와 context를 결합할 수 있다.
1. context 생성
2. State와 dispatch 함수를 context에 넣기
3. 트리 안에서 context 사용하기

### 1. Context 생성하기
트리를 통해 dispatch함수를 함께 전달하려면 다음과 같이 두 개의 별개의 context를 생성해야 한다.
원래 useReducer 훅은 아래와 같이 생겼다.
```js
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
```
- TasksContext는 현재 tasks 리스트를 제공한다.
- TasksDispatchContext는 컴포넌트에서 action을 dispatch하는 함수를 제공한다.

```js
import {createContext} from 'react';

export const TasksContext = createContext(null);
export const TasksDispatchContext = createContext(null);
```
### 2. State와 dispatch 함수를 context에 넣기
이제 최상위 컴포넌트에서 두 context를 모두 불러올 수 있다. useReducer()를 통해 반환된 tasks와 dispatch를 받고 아래 트리 전체에 전달한다.
```js
import {TasksContext, TasksDispatchContext} from './TasksContext.js'

export default function TaskApp(){
	const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
	// ...
	return(
		<TasksContext.Provider value={tasks}>
			<TasksDispatchContext.Provider value={dispatch}>
			...
			</TasksDispatchContext.Provider>
		</TasksContext.Provider>	
	)
}

```
### 3. 트리 안에서 context 사용하기
이렇게 하고 나면 이제 tasks 리스트나 이벤트 핸들러를 트리 아래로 전달할 필요가 없다.
```js
		<TasksContext.Provider value={tasks}>
			<TasksDispatchContext.Provider value={dispatch}>
				<AddTask />  
				<TaskList />
			</TasksDispatchContext.Provider>
		</TasksContext.Provider>	
```

State와 state를 관리하는 useReducer는 여전히 최상위 컴포넌트인 TaskBoard에 있으나 tasks와 dispatch는 하위 트리 커포넌트 어디서나 context를 불러와서 사용할 수 있다.

## 하나의 파일로 합치기
reducer와 context를 모두 하나의 파일에 작성하면 컴포넌트들을 조금 더 정리할 수 있다. Reducer를 같은 파일로 옮기고 TasksProvider 컴포넌트를 새로 선언하여 이 컴포넌트를 통해 모든 것을 하나로 묶으면 좋다.

```js
export function TasksProvider({ children }) {  
	const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);  
		return (  
			<TasksContext.Provider value={tasks}>  
				<TasksDispatchContext.Provider value={dispatch}>  
					{children}  
				</TasksDispatchContext.Provider>  
			</TasksContext.Provider>  
		);  
}
```
```js
import {TasksProvider} from './TasksContext.js'

export default function TaskApp(){
	return(
		<TasksProvider>
			<h1>Day off in Kyoto </h1>
			<AddTask />
			<TaskList />
		</TasksProvider>
	);
}
```
`TasksContext.js`에서 context를 사용하기 위한 use 함수들도 내보낼 수 있다.
```js
export function useTasks(){
	return useContext(TasksContext);
}

export function useTasksDisPatch(){
	return useContext(TasksDispatchContext);
}
```
그리고 그렇게 만들어진 함수를 사용하여 컴포넌트에서 context를 읽을 수 있다.
```js
const tasks = useTasks();
const dispatch = useTasksDispatch();
```
이렇게 하면 동작은 그대로지만, 다음에 context를 더 분리하거나 함수들에 로직을 추가하기 쉬워진다. 모든 context와 reducer는 TasksContext.js 안에 있기 때문이다. 이렇게 컴포넌트들이 데이터를 어디서 가져오는지가 아닌 무엇을 보여줄 것인지에 집중할 수 있도록 깨끗하게 정리할 수 있다.

https://react-ko.dev/learn/scaling-up-with-reducer-and-context#moving-all-wiring-into-a-single-file

