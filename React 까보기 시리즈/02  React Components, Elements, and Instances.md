- 관련 문서 : https://withboaz.medium.com/react-components-elements-and-instances-%EB%B2%88%EC%97%AD%EA%B8%80-b5744930846b
- 유튜브 링크 : https://www.youtube.com/watch?v=QSJUTS9PScY

## 기존 UI 모델 (OOP)
- 각 인스턴스를 제 때 remove하고 append 해야해서 관리해줘야 할 게 많아진다.
- 부모와 그에 붙어있는 요소를 분리하기가 어렵다.

## Elements
React는 element를 활용하는 것이 기존 UI model 과의 차이점이다.
- 엘레멘트는 plain object이다.
- 인스턴스가 아니면서, React에게 뭘 그려야 하는지 말해주는 것
- `{type: string|ReactClass, props:object}`
- Dom elements일 때 string, props에는 attributes
- Component elements일 때는 ReactClass, props는 props
- React Components와 Dom elements가 같은 위계로 다룰 수 있게 되었다. 
- 완전히 mixed, nested한 구조가 가능해진다. 이 말인즉슨!! `컴포넌트들 끼리 decoupled가 가능해진다는 것이다!!!`
- Element tree안에 렌더링 되어야 할 정보들이 encapsulate 되어 있는 것이다.

## Components Encapsulate Element Trees
React가 type 값이 class or functional인 element를 만나면
1. type 값을 보고, 해당 component 함수에게 element 를 return 받는다.
2. return받은 element의 type값이 tag name인 element(DOM element)를 만날 때까지 1번으로 돌아감
모든 element가 가진 DOM elements를 알게 됨 -> React가 해당 DOM elements들을 적절한 때에 create, update, destroy.

> element는 DOM tree에 전달할 정보를 가지고 있는 자바스크립트 객체이다.
> element가 element tree를 이루게 되는데, React가 이를 알고 있어서 알아서 create, destroy등을 수행한다.

## Component Can Be Classes or Functions
elements를 return하는 역할을 하는 것. encapsulate해서 가지고 있다.

## Top-Down Reconciliation
- Component -> element를 만날 때까지 쭉 하위로 내려가면서 순서로 return문을 진행한다.
- React call reconciliation -> reconciliation이 끝나면 React는 이제 DOM tree를 갖게 된다. 
- React class component는 class를 직접 호출하지 않고 React가 인스턴스를 만들어줌

