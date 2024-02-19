컴포넌트는 상호 작용의 결과로 화면의 내용을 변경해야 하는 경우가 많다. React에서는 이런 종류의 컴포넌트별 메모리를 state라고 한다.

- How to add a state variable with the [`useState`](https://react-ko.dev/reference/react/useState) Hook
- What pair of values the `useState` Hook returns
- How to add more than one state variable
- Why state is called local

### When a regular variable isn't enough
컴포넌트 안에서 일반적으로 정의된 지역변수를 함수를 통해 아무리 바꿔도 화면에 바뀌는 값이 보이지 않는다. 다음 두 가지 이유 때문이다.
1. 지역변수는 렌더링 간에 유지되지 않는다.
2. 지역 변수를 변경해도 리렌더링을 발생시키지 않는다.

따라서 반대로, 새로운 데이터로 컴포넌트를 업데이트 할 때 다음 두 가지 동작이 일어나야 한다.
1. 렌더링 간에 데이터를 유지해야 하며
2. 리렌더링을 발생시켜야 한다.

`useState` 훅은 이 두 가지를 제공한다.
1. 렌더링 사이에 데이터를 유지하기 위한 state 변수.
2. 변수를 업데이트하고 React가 컴포넌트를 다시 렌더링하도록 촉발하는 state설정자 함수.

### Meet your first Hook
`use`로 시작하는 훅스는 항상 최상위에 놓여야 한다. 모듈을 import해오는 것과 같이 생각하면 좋다. 조건문, 반복문, 또는 기타 중첩된 함수 내부에서는 훅을 호출할 수 없다.
훅은 리액트가 렌더링 중일 때만 사용할 수 있는 특별한 함수이다.

### React는 어떤 state를 반환할지 어떻게 알 수 있을까요?
- **동일한 컴포넌트의 모든 렌더링에서 안정적인 호출 순서에 의존합니다.**
- 아래 코드에서 `componentHooks = [[a, setA], [b, setB]]`  처럼 pair로 state가 저장된다.
- https://react-ko.dev/learn/state-a-components-memory#how-does-react-know-which-state-to-return
- https://codesandbox.io/s/hqrhcm?file=/index.js&utm_medium=sandpack
- 따라서 조건문이나 반복문 안에서 훅스를 호출할 경우 순서를 보장할 수 없기 때문에, 컴포넌트의 상단에 작성하라고 하는 것이다.
### State는 격리되고 private하다.
- 동일한 컴포넌트를 두 번 사용했다고 하더라도 각각의 컴포넌트 안에 있는 state는 다른 컴포넌트 안의 state에게 영향을 미칠 수 없다. 만약 서로 다른 컴포넌트에서 동일하게 값을 변화시키려면 그 둘의 부모에서 state를 만들어 props로 내여줘야 한다.