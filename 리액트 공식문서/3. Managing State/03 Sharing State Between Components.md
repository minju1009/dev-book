> 컴포넌트 간에 state를 공유할 수 있도록 '끌어올리는' 방법

> 두 컴포넌트의 state가 항상 함께 변경되기를 원한다면, 각각의 컴포넌트에서 state를 제거하고 가장 가까운 공통 부모로 이동한 다음 props를 통해 전달하면 된다. 이를 `state 끌어올리기`라고 한다.

- How to share state between components by lifting it up
- What are controlled and uncontrolled components
## 예제로 알아보는 state 끌어올리기
```js
function Panel({ title, children }) {
  const [isActive, setIsActive] = useState(false);
  return (
    <section className="panel">
      <h3>{title}</h3>
      {isActive ? (
        <p>{children}</p>
      ) : (
        <button onClick={() => setIsActive(true)}>
          Show
        </button>
      )}
    </section>
  );
}

export default function Accordion() {
  return (
    <>
      <h2>Almaty, Kazakhstan</h2>
      <Panel title="About">
        패널 설명
      </Panel>
      <Panel title="Etymology">
        패널 설명 2
      </Panel>
    </>
  );
}
```

현 상황에서는 패널 두 개각 각각 독립적인 컴포넌트이며, 한 패널의 버튼을 눌러도 다른 패널에 영향을 주지 않는다.

그러나, 한 번에 하나의 패널만 열리도록 변경하려면? 
다은 세 단계에 걸쳐 부모 컴포넌트로 state를 끌어올려야 한다.
1. 자식 컴포넌트에서 state를 제거
2. 공통 부모 컴포넌트에 하드 코딩된 데이터를 전달
3. 공통 부모 컴포넌트에 state를 추가하고 이벤트 핸들러와 함께 전달

### 1. 자식 컴포넌트에서 state제거
부모 컴포넌트에 Panel의 isActive를 제어할 수 있는 권한을 부여한다. 즉, 부모 컴포넌트가 `isActive`를 `Panel`에 prop으로 대신 전달하게 된다. 
```
function Panel({title, children, isActive}){
... // 여기 있던 state인 isActive를 부모에게서 받아오도록 한다.
}
```

### 2. 공통 부모 컴포넌트에 하드 코딩된 데이터를 전달
isActive값에 true를 주고 원하는 값 대로 동작하는지 확인

### 3. 공통 부모에 state 추가
이 경우 한 번에 하나의 패널만 활성화되어야 한다. 즉, 공통 부모 컴포넌트인 Accordion은 어떤 패널이 활성화된 패널이 추적해야 하기 때문에 boolean대신, 활성화된 Panel의 인덱스를 나타내는 숫자를 state 변수로 사용한다.
```js
const [activeIndex, setActiveIndex] = useState(0);
```

그 후, 이벤트 핸들러를 Panel에 함께 전달하며 activeIndex 값을 바꿀 수 있도록 해야 한다.
```js
<>  

<Panel  
isActive={activeIndex === 0}  
onShow={() => setActiveIndex(0)}  
>  
...  
</Panel>  

<Panel  
isActive={activeIndex === 1}  
onShow={() => setActiveIndex(1)}  
>  
...  
</Panel>  
</>
```

이제 다음과 같이 하나의 state로 각각의 컴포넌트를 제어할 수 있게 되었다!
![[Pasted image 20230529015511.png]]

## 제어 및 비제어 컴포넌트
### 비제어 컴포넌트(uncontrolled components)
- 일부 로컬 state를 가진 컴포넌트를 '비제어 컴포넌트'라고 부른다. 
- 위 예시에서 맨 첫번째 상태, 즉 각각의 컴포넌트가 isActive라는 state를 가지고 있던 버전에 대해 비제어 컴포넌트라고 부른다.
- 비제어 컴포넌트는 설정이나 구성이 덜 필요하기 때문에 상위 컴포넌트 내에서 사용하기가 쉬우나, 함께 통합하려는 경우 유연성이 떨어진다.

### 제어 컴포넌트(controlled components)
- 반대로, 컴포넌트의 중요한 정보가 자체 로컬 state가 아닌 props에 의해 구동되는 경우 컴포넌트가 '제어' 된다고 말할 수 있다. 이렇게 하면 부모 컴포넌트가 그 동작을 완전히 지정할 수 있다.
- 제어 컴포넌트는 최대한의 유연성을 제공하지만 부모 컴포넌트가 props를 사용하여 완전히 구성해야 한다.

### SSOT 각 state의 단일 진실 공급원
(A single source of truth for each state)
- 각 고유한 state들에 대해 해당 state를 '소유'하는 컴포넌트를 선택하게 되는데, 이 원칙은 SSOT라고 불리기도 한다. 모든 state가 한 곳에 있다는 의미가 아니라, `각 state마다 해당 정보를 소유하는 특정 컴포넌트가 있다`는 뜻이다. 컴포넌트 간에 공유할 state를 복제해서 사용하기 보다, 공통으로 공유하는 부모로 끌어올려서 필요한 자식에게 전달한다.


왜 ref를 쓰는가 querySelector는 class명이 들어가는데 리렌더링될때, 나타날지 안나타날지 모르기때문에. 안정성때문에! 결국에는 runtime에러가 발생할 수 있기 때문에 

querySelectorAll은 DOM이 없을때에도 실행될 수 있으나, ref.current commit 단계에서 DOM 노드를 잡기 때문에 조금 더 안정성을 보장할 수 있다. 

useEffect(() => {}, []) 
반응형 변수를 전부다 넣어줘야 하나 린트에서 난리 친다. 
state

- 네트워크 워터폴  / Effect가 서버사이드 렌더링 할 때, 서버에서 실행되지 않는다. 위에꺼 끝나야지만 밑에거를 
- 조건 경합 :  어떤 처리가 병렬로 일어나거나 동시에 일어날 떄 데이터까 꼬이는 현상, 자바스크립트에서는 비동기 할 때 주로 많이 사용된다. 
- Suspense
- useMemo란? 값을 캐싱하는 것, 연산비용이 높을 때 재연산을 하지 않아도 되니깐 