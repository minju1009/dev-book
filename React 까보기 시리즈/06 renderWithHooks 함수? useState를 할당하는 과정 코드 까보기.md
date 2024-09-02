reconciler의 renderWithHooks() 에서는 무슨 일이?

### 어떻게 useState export 하지?
-  ReactCurrentDispatcher.current에 할당하는 것?
	- nextCurrentHook 이 null O -> mount (HooksDispatcherOnMount)
	- nextCurrentHook이 null X -> update(HooksDispatcherOnUpdate)
- HooksDispatcherOnMount 객체에 할당한 mountState
	- -> nextCurrentHook 알려면 함수 전체 살펴봐야 함

### renderWithHooks
- hook과 함께 render 즉, hook을 주입하는 역할
- 렌더링 : 컴포넌트 후 그 결과가 VDOM에 반영되는 과정
- 컴포넌트 상태가 mount, update일 때 + 다음 컴포넌트가 사용할 수 있게 전역변수 초기화 로직도 포함
- 왜 전역변수? 함수가 다 끝나기 전에 다른 함수를 호출 그 함수가 끝나기 전 함수에서 활용하던 변수를 사용해야함. 전역변수 활용으로 가능


### Component(fiber)와 hook을 연결
- currentlyRenderingFiber 전역변수에 workInProgress 할당
- nextCurrentHook 잘 모르지만 일단 hook 이구나,,, 여기에 
	- current가
		- null 이 아니면 -> current.memoizedState 할당
		- null 이면 -> null 할당
- current는 VDOM 중에서 DOM에 mount된 정보를 가진 fiber
- current.memoizedState에는 hook이 들어있구나 (fiber의 memoizedState -> hook 할당)