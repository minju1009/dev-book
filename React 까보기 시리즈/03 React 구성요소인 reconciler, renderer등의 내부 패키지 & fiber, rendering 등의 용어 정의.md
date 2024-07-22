## 구성 요소
### react 코어
- component를 정의하는 역할을 함
- 다른 패키지에 의존성이 없으므로 다양한 플랫폼(브라우저, 모바일)에 올려서 사용 가능

### renderer
- react-dom, react-native-renderer 등 호스트 렌더링 환경에 의존
- 호스트와 react를 연결 즉, 웹에서는 DOM 조작을 하는 역할을 한다.
- reconciler와 legacy-events 패키지 의존성

### event(legacy-events)
- SyntheticEvent라는 이름의 내부적으로 개발된 이벤트 시스템
- 기존 웹에서의 event를 wrapping하여 추가적인 기능을 수행할 수 있게 한다.

### scheduler
- react는 Task를 비동기로 실행함.
- 이 Task를 실행하는 타이밍을 알고 있는 패키지

### reconciler
- fiber architecture에서 VDOM 재조정 담당
- 컴포넌트를 호출하는 곳

## 용어
### 렌더링
- 컴포넌트를 호출하여 return react element -> VDOM에 적용(재조정)하는 과정
- 전체 과정
	- 컴포넌트 호출 
	- VDOM 재조정 작업 `(여기까지가 렌더링)`
	- renderer가 컴포넌트 정보를 DOM에 삽입(mount)
	- 브라우저가 DOM을 paint
### react element
- 컴포넌트 호출시 return 하는 것 (JSX -> babel을 통해 react.createElement() 호출)
- 컴포넌트의 정보(결국 DOM에 삽입될 내용)을 담은 객체
	- type, key props, ref등의 정보

### fiber
- VDOM의 노드 객체(architecture 이름과 동일)
- react element의 내용이 DOM에 반영되기 위해, 먼저 VDOM에 추가되어야 하는데, 이를 위해 확장한 객체 
	- 컴포넌트의 상태, life cycle, hook이 관리됨 (결국 얘도 JS 객체)