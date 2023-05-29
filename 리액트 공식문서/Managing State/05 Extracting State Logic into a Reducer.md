여러 개의 state 업데이트가 여러 이벤트 핸들러에 분산되어 있는 컴포넌트는 과부하가 걸릴 수 있다. 이런 경우, reducer라고 하는 단일 함수를 통해 컴포넌트 외부의 모든 state 업데이트 로직을 통합할 수 있다.

## reducer로 state로직 통합하기
컴포넌트가 복잡해지면 컴포넌트의 state가 업데이트되는 다양한 경우를 한눈에 파악하기 어려워진다. 예를들어 `TaskApp` 컴포넌트는 state에 tasks 배열을 보유하고 있으며, 세 가지 이벤트 핸들러를 사용하여 task를 추가, 제거 및 수정한다.

```js
import { useState } from 'react';
import AddTask from './AddTask.js';
import TaskList from './TaskList.js';

export default function TaskApp() {
  const [tasks, setTasks] = useState(initialTasks);

  function handleAddTask(text) {
    setTasks([
      ...tasks,
      {
        id: nextId++,
        text: text,
        done: false,
      },
    ]);
  }

  function handleChangeTask(task) {
    setTasks(
      tasks.map((t) => {
        if (t.id === task.id) {
          return task;
        } else {
          return t;
        }
      })
    );
  }

  function handleDeleteTask(taskId) {
    setTasks(tasks.filter((t) => t.id !== taskId));
  }

  return (
    <>
      <h1>Prague itinerary</h1>
      <AddTask onAddTask={handleAddTask} />
      <TaskList
        tasks={tasks}
        onChangeTask={handleChangeTask}
        onDeleteTask={handleDeleteTask}
      />
    </>
  );
}

let nextId = 3;
const initialTasks = [
  {id: 0, text: 'Visit Kafka Museum', done: true},
  {id: 1, text: 'Watch a puppet show', done: false},
  {id: 2, text: 'Lennon Wall pic', done: false},
];

```

각 이벤트 핸들러는 setTasks를 호출한다. 컴포넌트가 커질수록 여기저기 흩어져 있는 state 로직의 양도 늘어난다. 이 때 복잡성을 줄이고 모든 로직을 접근하기 쉽게 한 곳에 모으려면, state 로직을 컴포넌트 외부의 reducer라고 하는 단일 함수로 옮길 수 있다.

Reducer는 state를 관리하는 다른 방법으로, useState에서 useReducer로 마이그레이션하는 방법은 세 단계로 진행된다.
1. state를 설정하는 것에서 action들을 전달하는 것으로 변경하기
2. reducer 함수 작성하기
3. 컴포넌트에서 reducer 사용하기


### 1. state를 설정하는 것에서 action들을 전달하는 것으로 변경하기
- state를 설정하여 React에게 "무엇을 할 지"를 지시하는 대신, 이벤트 핸들러에서 "action"을 전달하여 '사용자가 방금 한 일'을 지정한다. (state 업데이트 로직은 다른 곳에 있다!) 즉, 이벤트 핸들러를 통해 tasks를 설정하는 대신 tasks를 추가/변경/삭제하는 action을 전달하는 것이다. 이런 방식이 사용자의 의도를 더 명확하게 설명한다.

```js
function handleAddTask(text){
	dispatch(
	// 'action' object:
	{
		type: 'added',
		id: nextId++,
		text, text,
	}
	)
}
```

위 함수처럼 dispatch 함수에 넣어준 객체를 `action`이라고 한다.
action 객체는 어떤 형태든 될 수 있다. 그러나 일반적으로 무슨 일이 일어나는지 설명하는 문자열 타입의 `type`을 지정하고 추가적인 정보는 다른 필드를 통해 전달하도록 작성한다. type은 컴포넌트에 따라 다르므로 이 예에서는 added 또는 added_task를 사용하면 된다.

### 2. reducer 함수 작성하기
reducer 함수에 state 로직을 둘 수 있다. 이 함수는 두 개의 매개변수를 가지는데, 하나는 현재 state이고 하나는 action 객체이다. 그리고 이 함수가 다음 state를 반환한다.
```js
function yourReducer(state, action){
	// return next state for React to set
}
```
이렇게 state를 설정하는 로직을 이벤트 핸들러에서 reducer 함수로 옮기기 위해 다음과 같이 하면 된다!
1) 현재의 state(tasks)를 첫 번째 매개변수로 선언
2) action 객체를 두 번째 매개변수로 선언
3) 다음 state를 reducer 함수에서 반환

```js
function tasksReducer(tasks, action){
	if(action.type === 'added'){
		return [
			...tasks,
			{
				id: action.id,
				text: action.text,
				done: false,
			},
		];
	} else if (action.type === 'changed'){
	 return tasks.map((t) -> {
		 if(t.id === action.task.id){
			 return action.task;
		 } else {
			 return t;
		 }
	 });
	} else if ...
}
```

보다시피, reducer 함수는 state(tasks)를 매개변수로 갖기 때문에, 컴포넌트 밖에서 reducer 함수를 선언할 수 있다. 이렇게 하면 들여쓰기 단계도 줄이고 가독성도 높아진다. 

위 처럼 if else 구분으로 쓰기보다 switch 문으로 쓰는게 더 일반적이며, case 블럭을 모두 중괄호 {와 }로 감싸는 걸 추천한다. 이렇게 하면 다양한 case들 안에서 선언된 변수들이 서로 충돌하지 않는다. 

```js
function tasksReducer(tasks, action) {  
	switch (action.type) {  
		case 'added': {  
		return [  
			...tasks,  
		{  
			id: action.id,  
			text: action.text,  
			done: false,  
		},  
		];  
	}  
case 'changed': {  
	return tasks.map((t) => {  
		if (t.id === action.task.i) {  
		return action.task;  
		} else {  
		return t;  
		}  
		});  
	}  
case 'deleted': {  
		return tasks.filter((t) => t.id !== action.id);  
	}  
default: {  
	throw Error('Unknown action: ' + action.type);  
	}  
	}  
}
```

배열 메서드 중 reduce() 연산이 배열을 가지고 많은 값들을 하나의 값으로 '누적'하듯이 지금까지의 결과와 현재의 아이템을 가지고 다음 결과를 반환하기는 React reducer도 같은 맥락이다. React reducer도 지금까지의 state와 action을 가지고 다음 state를 반환한다. 이런 방식으로 시간이 지나면서 action들을 state로 모으게 된다. 

### 3. 컴포넌트에서 reducer 사용하기
마지막으로, 컴포넌트에 `tasksReducer`을 연결해야 한다. React에서 useReducer Hook을 import한다.
```js
import {useReducer} from 'react';

// useState대신 useReducer사용
const [tasks, setTasks] = useState(initialTasks);
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);
```
이로써 관심사가 분리되었다! 이렇게 관심사를 분리하면 컴포넌트 로직을 더 쉽게 읽을 수 있다. 이제 이벤트 핸들러는 action을 전달하여 무슨 일이 일어났는지만 지정하고, reducer 함수는 action에 대한 응답으로 state가 어떻게 변경되는지를 결정한다.

## useState vs useReducer
- 코드 크기 : 일반적으로 useState를 사용하는게 미리 작성할 코드가 줄지만, 많은 이벤트 핸들러가 비슷한 방식으로 state를 업데이트 하는 경우 useReducer를 사용하면 코드를 줄이는데 도움을 줄 수 있다.
- 가독성 : useState로 간단한 state를 업데이트 하는 경우 가독성이 좋다. 
- 디버깅 : useReducer를 사용하면, reducer에 콘솔 로그를 추가하여 모든 state 업데이트와 왜 버그가 발생했는지 찾기 쉽다.
- Testing : reducer는 순수한 함수로 별도로 분리해서 내보내거나 테스트할 수 있다. 

> Reducer는 반드시 순수해야 하고, 각 action은 여러 데이터가 변경되더라도, 하나의 사용자 상호작용을 설명해야 한다. 