리액트가 UI 업데이트 하는 과정을 빗대어서 이해해보자!
React는 고객들의 요청을 받고 Components에게 전달하고, Components가 만든 요리를 다시 고객에게 전달하는 웨이터이다. 이 과정에는 UI를 요청하고 제공하는 세 가지 단계가 있다.

1. 렌더링 촉발 (손님의 주문을 받고 주방으로 전달)
2. 컴포넌트 렌더링 (주방에서 받은 주문으로 요리만들기)
3. DOM에 커밋(손님에게 주문한 음식을 가져다 주기)

Trigger(렌더링 촉발) - Render(컴포넌트 호출) - Commit(DOM에 변경사항 반영)
### Step 1 : Trigger a render
컴포넌트 렌더링이 일어나는 두 가지 이유는 다음과 같다.
1) 컴포넌트의 첫 렌더링일 경우
2) 컴포넌트의 state 또는 상위 요소의 state가 업데이트된 경우

#### 첫 렌더링
다음과 같이 creeateRoot와 root.render 함수를 통해 이미지가 렌더링 된다.
```js
import Image from './Image.js';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'))
root.render(<Image />);

```

#### state가 업데이트되면 리렌더링한다.
고객이 처음에 주문을 넣고 추가주문을 하듯이 set function을 통해 추가로 렌더링을 발생시킬 수 있다.

### Step 2 : React renders your components
- 렌더링을 촉발시키면, React는 컴포넌트를 호출하여 화면에 표시할 내용을 파악한다. `렌더링은 React에서 컴포넌트를 호출하는 것이다.`
- **첫 렌더링에서** React는 루트 컴포넌트를 호출한다. 이 때 컴포넌트에 있는 태그에 대한 DOM 노드가 생성된다.
- **리렌더링하는 동안** React는 state 업데이트에 의해 렌더링이 발동된 함수 컴포넌트를 호출한다. 이 때 React는 이전 렌더링 이후 변경된 속성을 계산하나, 다음 단계인 커밋 단계까지는 해당 정보로 아무 작업도 수행하지 않는다.

#### 성능 최적화와 관련하여
- 만약 컴포넌트들이 중첩되어 있다면 상위 컴포넌트가 업데이트되면 하위의 컴포넌트들도 전부다 다시 업데이트된다. 이 점을 고려하여 프로그래밍 필요

### Step 3 : React commits changes to the DOM
리액트가 DOM에 변경사항을 커밋

컴포넌트를 렌더링(호출)한 후 React는 해당 정보로 DOM을 수정한다. 이 단계를 커밋이라고 한다.
- 초기 렌더링의 경우 React는 appendChild() DOM API를 사용하여 생성한 모든 DOM 노드를 화면에 표시한다.
- 리렌더링의 경우에는 필요한 최소한의 작업만 적용하여 DOM이 최신 렌더링 출력과 일치하도록 한다. (모든 노드가 다시 그려지는게 아니라 변경 필요한 만큼만 대시 그려지는 것이다.)

### Browser Pain
렌더링이 완료되고 DOM을 업데이트한 후 브라우저는 화면을 다시 그린다. 이 단계를 '브라우저 렌더링'이라고 하며, 문서에서는 '페인팅'이라고 부를 것이다.