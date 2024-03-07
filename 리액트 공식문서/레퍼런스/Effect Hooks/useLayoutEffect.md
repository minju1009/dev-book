> `useLayoutEffect`는 성능을 저하시킬 수 있다. 가급적이면 `useEffect`를 사용해야 한다.

`useLayoutEffect`는 브라우저가 화면을 다시 채우기 전에 실행되는 버전의 `useEffect`이다.
```jsx
useLayoutEffect(setup, dependencies?)
```

# **Reference**
브라우저가 화면을 다시 그리기 전에 `useLayoutEffect`를 호출하여 레이아웃을 측정한다.

```jsx
import { useState, useRef, useLayoutEffect } from 'react';

function Tooltip() {
	const ref = useRef(null);
	const [tooltipHeight, setTooltipHeight] = useState(0);

	useLayoutEffect(() => {
		const {height} = ref.current.getBoundingCliendRect();
		setTooltipHeight(height);
	}, [])

}
```

**Parameters**
`useEffect`와 똑같이 setup함수와 dependency를 받는다.

**Returns**
`undefined`를 반환한다.

**Caveats**
- 훅이므로 컴포넌트 최상위 레벨에서만 호출할 수 있다.
- Strict Mode 에서 개발전용 셋업+클린업 함수가 한 번 더 실행된다.
- 불필요한 객체, 함수 의존성을 제거해야 한다.
- 과도하게 사용하면 앱이 느려진다. 가급적 `useEffect`를 사용해야 한다.


# **Usage**
### 브라우저에서 화면을 다시 그리기 전 레이아웃 측정하기
대부분의 컴포넌트는 무엇을 렌더링할지 결정하기 위해 화면에서의 위치와 크기를 알 필요가 없다. 대부분의 컴포넌트는 JSX를 반환할 뿐이고, 브라우저가 해당 컴포넌트의 레이아웃을 계산하고 화면을 다시 그린다.

그런데, 때론 이것만으로 충분하지 않을 수 있다. 예를 들어 마우스오버 시 요소 옆에 툴팁을 표시하는 것을 상상해 보라. 공간이 충분하면 툴팁이 요소 위에 표시되어야 하지만, 공간이 충분하지 않으면 아래에 표시되어야 한다. 툴팁을 올바른 최종 위치에 렌더링하려면 툴팁의 높이를 알아야 한다.

이렇게 하려면 두 번의 패스로 렌더링해야 한다.
	1. 툴팁을 원하는 위치에 렌더링한다(위치가 잘못된 경우에도)
	2. 높이를 측정하고 툴팁을 배치할 위치를 결정한다.
	3. 올바른 위치에 툴팁을 다시 렌더링한다.
이 모든 작업은 브라우저가 화면을 다시 그리기 전에 이루어져야 한다. 브라우저가 화면을 다시 그리기 전에 `useLayoutEffect`를 호출하여 레이아웃 측정을 수행한다.

```jsx
function Tooltip(){
	const ref = useRef(null);
	const [tooltipHeight, setTooltipHeight] = useState(0);

	useLayoutEffect(()=>{
		const {height} = ref.getBoundClientRect();
		setTooltipHeight(height); // 실제 높이를 알았으니 이제 리렌더링 한다.
	
	},[])

	// 아래에 작성될 렌더링 로직에 tooltipHeight를 사용한다.
}
```

단계별 작동방식은 아래와 같다.
	1. `Tooltip`은 초기 `tooltipHeight = 0`으로 렌더링된다. (따라서 툴팁의 위치가 잘못 지정될 수 있음)
	2. React는 이를 DOM에 배치하고 `useLayoutEffect`에서 코드를 실행한다.
	3. `useLayoutEffect`는 툴팁 콘텐츠의 높이를 측정하고 즉시 다시 렌더링을 촉발한다.
	4. `Tooltip`이 실제 `tooltipHeight`로 다시 렌더링된다.(따라서 툴팁이 올바르게 배치된다)
	5. React가 DOM에서 이를 업데이트하면 브라우저에 툴팁이 최종적으로 표시된다.

# **Troubleshooting**
### 오류가 발생합니다: “`useLayoutEffect`가 서버에서 아무것도 수행하지 않습니다ˮ
`useLayoutEfffect`의 목적은 컴포넌트가 렌더링에 레이아웃 정보를 사용하도록 하는 것이다.
1. 초기 콘텐츠를 렌더링한다.
2. 브라우저가 화면을 다시 칠하기 전에 레이아웃을 측정한다.
3. 읽은 레이아웃 정보를 사용하여 최종 컨텐츠를 렌더링한다.

서버렌더링을 사용하는 경우, React 앱은 초기 렌더링을 위해 서버의 HTML로 렌더링된다. 이로 인해 JavaScript 코드가 로드되기 전에 초기 HTML을 표시할 수 있다.

그러나 문제는 서버에는 레이아웃 정보가 없다는 것이다.

위 예시에서 Tooptip 컴포넌트의 useLayoutEffect 호출은 콘텐츠 높이에 따라 콘텐츠 위 또는 아래에 올바르게 배치되도록 한다. 만약 서버에서 생성한 HTML에 툴팁을 렌더링하려고 하면, 아마 어떻게 해야하는지 모를 것이다. 서버에는 레이아웃이 아직 없기 때문이다! 따라서 서버사이드 렌더링을 하더라도 JavaScript가 로드되고 실행된 후 클라이언트에서 그 위치가 '점프'된다.

일반적으로 레이아웃 정보가 필요한 컴포넌트는 서버에서 렌더링할 필요가 없다. 예를 들어, 초기 렌더링 중에 `Tooltip`을 표시하는 것은 의미가 없을 수 있다. 보통 클라이언트 상호작용에 의해 촉발되기 때문이다.

하지만 이 문제가 발생하는 경우 몇 가지 다른 옵션이 있다.
	- useLayoutEffect를 `useEffect`로 바꿔라. 이렇게 하면 React가 페인트를 막지 않고 초기 렌더링 결과를 표시해도 괜찮다고 알려준다.
	- 컴포넌트를 클라이언트 전용으로 표시해라. 이렇게 하면 서버 렌더링 중에 가장 가까운 `<Suspense>` 경계까지의 콘텐츠를 로딩 폴백(spinner나 glimmer)로 대체하도록 React에 지시할 수 있다.
	- `hydration`후에만 `useLayoutEffect`가 사용된 컴포넌트를 렌더링하도록 한다. 그러면 렌더링 로직은 다음과 같을 수 있다 : `return isMounted? <RealContent /> : <FallbackContent />` 서버에서는 `useLayoutEffect`가 사용되지 않으므로 에러를 해결할 수 있다.
	- 컴포넌트를 외부 저장소와 동기화하고 레이아웃 측정이 아닌 다른 이유로 `useLAyoutEffect`에 의존하는 경우, 대신 서버 렌더링을 지원하는 `useSyncExternalStore`를 고려해보아라.