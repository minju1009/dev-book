# 흐름
Scheduler가 적절한 때에 Task를 실행하여 WORK를 전달
-> WORK를 통해 VDOM 재조정 작업 진행할 차례
이를 분석하기 위해
	- performConcurrentWorkOnRoot
	- performSyncWorkOnRoot
위 함수들 까보기
그 전에 아래 흐름에 대한 배경지식이 필요
JSX -> React element -> fiber(VDOM에 올라가기 위해 필요한 정보들을 포함)

# 배경지식
- element : DOM tree 생성에 필요한 정보를 담은 객체, 아래 두 종류로 나누어짐
	- React DOM element `<button>button</button>` `<div>`
	- React component element (React.createElement로 반환되는 엘리먼트)
- component: props를 argument로 받아서 react element를 return하는 함수

# React element의 종류
- host component(React DOM elements)
	- div, input, button 등 HTML element에 대응하는 component
- custom component(React component elements)
	- 개발자들이 선언한 클래스, 함수형 컴포넌트
- static component
	- Fragment, lazy, context, memo 등


# JSX -> React.createElement()
