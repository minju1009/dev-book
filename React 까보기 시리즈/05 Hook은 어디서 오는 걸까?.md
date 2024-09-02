### react 코어
	- React <- ReactHooks <- ReactCurrentDispatcher
	- react core는 react element에 대한 정보만 알고 있음
	- ReactCurrentDispatcher에는 그냥 current라는 프로퍼티만 있다.
		- current: (null | Dispatcher)
	 - react element는 fiber로 확장해야 hook을 포함하게 됨
	 - 확장을 누가 하느냐? VDOM으로 확장을? reconciler가 확장함. 
	 - 즉, reconciler가 hook을 알고 있음?  reconciler -> react 코어로 어떻게 전달?

### react 코어는 hook을 사용하기 위해 외부에서 주입받음(의존성 끊기 위해서 -> 이곳저곳에 사용 가능하도록)
	- React <- ReactHooks <- ReactCurrentDispatcher <- ReactSharedInternals
	- 결국 injection들의 대기소
	- react/ReactSharedInternals.js 는 객체에 property로 외부 모듈을 할당 받음

### shared
- shared는 모든 패키지가 공유하는 공통 패키지
	- shared/ReactSharedInternals.js는 react 코어 패키지에 연결된 출입구

### reconciler
- reconciler는 shared에 hook을 주입(객체에 property로 할당)


### hook 출처 한줄정리
- hook(useState)의 출처를 한줄로 정리하면
	- reconciler -> shared/ReactSharedInternal -> react/ReactsharedInternal -> react/ReactCurrentDispatcher -> react/ReactHooks -> react -> 개발자

