## 클래스 컴포넌트와 에러바운더리
기본적으로 애플리케이션이 렌더링 도중 에러를 발생시키면 React는 화면에서 해당 UI를 제거한다. 이를 방지하려면 UI의 일부를 Error Boundary로 감싸면 된다.

### Error Boundary란?
> 에러가 발생한 부분 대신 에러 메세지와 같은 Fallback UI를 표시할 수 있는 특수한 컴포넌트이다.

### 사용법
-  [`static getDerivedStateFromError`](https://react-ko.dev/reference/react/Component#static-getderivedstatefromerror) 를 제공하여 에러에 대한 응답으로 state를 업데이트하고 사용자에게 에러 메세지를 표시할 수 있도록 해야 하며
- 선택적으로  [`componentDidCatch`](https://react-ko.dev/reference/react/Component#componentdidcatch)를 구현하여 분석 서비스에 에러를 기록하는 등의 로직 추가도 할 수 있다.
```js
class ErrorBoundary extends React.Component {  

constructor(props) {  
	super(props);  
	this.state = { hasError: false };  
}  

  
static getDerivedStateFromError(error) {  
	// Update state so the next render will show the fallback UI.  
	return { hasError: true };  
}  


	componentDidCatch(error, info) {  
	// Example "componentStack":  
	// in ComponentThatThrows (created by App)  
	// in ErrorBoundary (created by App)  
	// in div (created by App)  
	// in App  
	logErrorToMyService(error, info.componentStack);  
}  

  

	render() {  

		if (this.state.hasError) {  
			// You can render any custom fallback UI  
			return this.props.fallback;  
		}  

		return this.props.children;  
			
		}  
	
}
```

그런 다음 컴포넌트 트리의 일부를 감쌀 수 있다.
```js
<ErrorBoundary fallback={<p>Something went wrong</p>}>  
	<Profile />  
</ErrorBoundary>	
```

## 함수컴포넌트와 에러바운더리
함수 컴포넌트에는 아직 componentDidCatch에 대한 직접적인 대응이 없기에, 
1) 하나의 ErrorBoundary 컴포넌트를 작성하여 앱 전체에서 사용하거나
2) 이 작업을 대신해주는 react-error-boundary 패키지를 사용할 수도 있다.
3) Sentry를 이용하여 선언적 에러처리를 이번에 해보면 좋겠다!

## Class 형식이 필요한 이유
- getSnapshotBeforeUpdate: 가장 마지막으로 렌더링 된 결과가 DOM 등에 반영되었을 때 호출
- getDerivedStateFromError: 하위의 자손 컴포넌트에서 오류가 발생했을 때 render 단계에서 호출
- componentDidCatch: 하위의 자손 컴포넌트에서 오류가 발생했을 때 commit 단계에서 호출

위 세지 라이프사이클이 아직 Hooks에서는 구현되지 않았기 때문에 클래스 컴포넌트가 아직 필요하다.
https://jthcast.dev/posts/why-react-still-needs-class-type-(feat.errorboundary)/


## 센트리
### 센트리는 어떻게 모든 에러를 기록할까?
1. [GlobalEventHandlers.onerror](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onerror)를 통해 런타임 에러와 문법 오류를 모두 기록한다. 다만 Promise의 Rejection은 에러로 구분되진 않기 때문에 GlobalEventHandlers.onerror 이벤트 리스너가 호출되지 않는다. 따라서 [onnhandledrejection](https://developer.mozilla.org/en-US/docs/Web/API/Window/unhandledrejection_event)를 통해 핸들링되지 않은 Promise Rejection을 감지하고 기록한다. ([관련 코드 1](https://github.com/getsentry/sentry-javascript/blob/1fb0b4c3f6533c43ead2d2cb3f0dc4bc8a84e544/packages/browser/src/integrations/globalhandlers.ts#L70-L113), [관련 코드 2](https://github.com/getsentry/sentry-javascript/blob/1fb0b4c3f6533c43ead2d2cb3f0dc4bc8a84e544/packages/browser/src/integrations/globalhandlers.ts#L116-L178))
2. 디버깅에 중요한 네이티브 API(History, Console, DOM 등)를 몽키패칭하여 로그를 기록하는 코드를 심는다. 에러가 발생하면 onnhandledrejection 핸들러에서 기록된 로그와 해당 에러를 서버에 전송한다. ([관련 코드](https://github.com/getsentry/sentry-javascript/blob/d8ecb2b927e23c0583456bef0b0c04d01dfe5eba/packages/utils/src/instrument.ts#L45-L77))

### 오프라인 상태에서도 에러를 기록하는 것 같은데 어떻게 구현되어 있을까?
1. 오프라인 상태일 때 발생한 이벤트를 디바이스 로컬 데이터 저장소에 캐싱한다 ([해당 로직](https://github.com/getsentry/sentry-javascript/blob/15465a8feb91df3c435e0822ad3f67661154c425/packages/integrations/src/offline.ts#L70-L86)) - IndexedDB를 사용했다.
2. 온라인으로 돌아오면 캐싱된 이벤트들을 모두 서버에 전송한다 ([해당 로직](https://github.com/getsentry/sentry-javascript/blob/15465a8feb91df3c435e0822ad3f67661154c425/packages/integrations/src/offline.ts#L62-L68))
3. 센트리가 초기화 될 때 마다 캐싱된 이벤트들을 모두 서버에 전송한다 ([해당 로직](https://github.com/getsentry/sentry-javascript/blob/15465a8feb91df3c435e0822ad3f67661154c425/packages/integrations/src/offline.ts#L89-L92))
https://blog.hyeonsulee.com/how-does-sentry-work-in-javascript