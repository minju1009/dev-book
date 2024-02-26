> Props 드릴링 없이 정보를 전달하는 방법

>일반적으로 부모 컴포넌트에서 자식 컴포넌트로 props를 통해 정보를 전달합니다. 하지만 중간에 여러 컴포넌트를 거쳐야 하거나 앱의 여러 컴포넌트가 동일한 정보를 필요로 하는 경우 props를 전달하면 장황하고 불편해질 수 있습니다. _Context_를 사용하면 부모 컴포넌트가 props를 통해 명시적으로 전달하지 않고도 깊이 여부와 무관하게 그 아래 트리의 모든 컴포넌트에서 일부 정보를 사용할 수 있습니다.

## prop 전달의 문제
prop 전달은 UI 트리를 통해 데이터를 사용하는 컴포넌트로 명시적으로 연결할 수 있는 좋은 방법이나, 트리 깊숙이 prop을 전달해야 하거나 많은 컴포넌트에 동일한 prop이 필요한 경우 prop 전달이 장황하고 불편해질 수 있다. 또한 state를 높이 끌어올리면 prop drilling이라고 불리는 상황이 발생할 수 있다.
![[Pasted image 20230529032530.png]]

그러면 props를 전달하지 않고도 트리에서 데이터를 필요한 컴포넌트로 전달하는 방법이 있을까? context 기능을 사용하면 된다!

## Context: props 전달의 대안
> Context를 사용하면 상위 컴포넌트가 그 아래 전체 트리에 데이터를 제공할 수 있다. context는 다양한 용도로 사용된다. 

```js
export default function Page() {
  return (
    <Section>
      <Heading level={1}>Title</Heading>
      <Section>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Section>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Section>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```

아래처럼 Section에서 동일한 level을 전달할 수 있도록 하면 참 좋겠다!
```js
<Section level={3}>  
	<Heading>About</Heading>  
	<Heading>Photos</Heading>  
	<Heading>Videos</Heading>  
</Section>
```

이럴 때 context를 사용할 수 있다.
1. context를 만들고 (여기서는 level에 대한 컨텍스트이므로 이름을 `LevelContext`라고 지을 것이다)
2. 데이터가 필요한 컴포넌트에서 해당 context를 사용한다. (Heading에서 LevelContext를 사용)
3. 데이터를 지정하는 컴포넌트에서 해당 context를 제공한다. (Section은 LevelContext를 제공)

### 1. Context 만들기
```js
import {createContext} from 'react';

export const LevelContext = createContext(1);
```

### 2. Context 사용하기
```js
import {useContext} from 'react';
import {LevelContext} from './LevelContext.js';

export default function heading({children}){
	const level = useContext(LevelContext);
}

```
### 3. Context 제공하기
```js
import { LevelContext } from './LevelContext.js';  

export default function Section({ level, children }) {  

return (  
	<section className="section">  
		<LevelContext.Provider value={level}>  
		{children}  
		</LevelContext.Provider>  
	</section>  
);  
}
```

이는 React에게 이 Section 안에 있는 컴포넌트가 LevelContext를 요청하면 이 level을 제공하라고 지시한다. 컴포넌트는 그 위에 있는 UI 트리에서 가장 가까운 `LevelContext.Provider`의 값을 사용한다.

아래처럼 작성하면 level값을 초기값으로부터 +1한 값으로 받아온다. 

```js
import { useContext } from 'react';  
import { LevelContext } from './LevelContext.js';  


export default function Section({ children }) {  
const level = useContext(LevelContext);  
	return (  
		<section className="section">  
			<LevelContext.Provider value={level + 1}>  
				{children}  
			</LevelContext.Provider>  
		</section>  
	);  
}
```

## Context는 중간 컴포넌트들을 통과한다.
context를 제공하는 컴포넌트와 context를 사용하는 컴포넌트 사이에 원하는 만큼의 컴포넌트를 삽입할 수 있다. 즉, Context를 사용하면 "주변 환경에 적응"하고 렌더링되는 위치(context)에 따라 다르게 표시되는 컴포넌트를 작성할 수 있다.

context가 작동하는 방식은 CSS 속성 상속처럼 동작하며, 그렇기에 React에서 위에서 오는 context를 재정의하는 유일한 방법은 children을 다른 값으로 context provider로 감싸는 것이다.

또한 React context도 override하지 않기 때문에 하나의 컴포넌트가 여러개의 다양한 context를 사용하거나 제공할 수 있다.

## context를 사용하기 전에 (Before you context)
context를 남용해서는 안 된다. props를 몇 단계 깊이 전달해야 한다고 해서 꼭 해당 정보를 context에 넣어야 한다는 의미는 아니다.
1. props 전달로 시작하자. props전달을 사용했을 때, 어떤 컴포넌트가 어떤 데이터를 사용하는지 매우 명확해지며, 코드를 유지 관리하는 사람은 props를 사용해 데이터 흐름을 더 잘 파악할 수 있다.
2. 컴포넌트를 추출하고 JSX를 children으로 전달하자. 일부 데이터를 해당 데이터를 사용하지 않는 중간 컴포넌트의 여러 레이어를 거쳐 전달한다면, 종종 그 과정에서 일부 컴포넌트를 추출하는 것을 잊었다는 것을 의미한다. 

위 두 가지를 먼저 고려해 보고 context를 고려하자.

## Context의 사용 사례
- 테마 : 다크모드 - 라이트모드 처럼 앱 상단에 context provider를 배치하고 시각적 모양을 조정해야 하는 컴포넌트에서 사용
- 현재 계정 : 많은 컴포넌트에서 현재 로그인한 사용자를 알아야 하는 경우
- 라우팅 : 아래에 있는 많은 멀리 떨어진 컴포넌트에서 라우팅을 변경하고 싶을때, context와 함께 reducer를 사용하여 복잡한 state를 관리하고 번거로움 없이 멀리 떨어진 컴포넌트에 전달할 수 있다.

## 기억해야할 점
context는 정적인 값과 components를 모두 업데이트 한다. 다음 렌더링에서 다른 값을 전달하면 React는 아래에서 이를 읽는 모든 컴포넌트를 업데이트 한다!! 이것이 context가 state와 함께 자주 사용되는 이유다.
