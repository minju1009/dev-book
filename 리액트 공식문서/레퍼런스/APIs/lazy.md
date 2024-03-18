
> `lazy` 를 사용하면 처음 렌더링될 때까지 컴포넌트 코드의 로딩을 지연시킬 수 있다.
# **Reference**
### `lazy(load)`
컴포넌트 외부에서 `lazy`를 호출하여 지연 로드된 React 컴포넌트를 선언한다.

```jsx
import { lazy } from 'react';  

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

`load`: [Promise](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise) 또는 다른 _thenable(_`then` 메서드를 가진 Promise와 유사한 객체)을 반환하는 함수. 

- React는 반환된 컴포넌트를 처음 렌더링하려고 시도할 때까지 `load`를 호출하지 않는다. 
- (성공) React는 먼저 `load`를 호출한 후 resolve될 때까지 기다린 다음, resolve된 값의 `.default`를 React 컴포넌트로 렌더링한다. 
- 반환된 Promise 및 Promise의 resolve된 값은 모두 캐시되므로, React는 `load`를 두 번 이상 호출하지 않는다. 
- (실패) Promise가 reject되면 React는 가장 가까운 Error Boundary에 reject된 이유를 `throw` 한다.

`lazy` 는 트리에 렌더링할 수 있는 React 컴포넌트를 반환합니다. 지연 컴포넌트의 코드가 로딩되는 동안 렌더링을 시도하면 _일시 중단 된다. 로딩하는 동안 로딩 표시기를 보여주려면 [`<Suspense>`](https://react-ko.dev/reference/react/Suspense) 를 사용하면 된다.

# **Usage**
### Suspense와 함께 사용하여 만드는 지연 컴포넌트

이 컴포넌트가 처음 렌더링될 때까지 로딩을 지연시키려면 import를 lazy 함수로 감싸면 된다.

```jsx
import { lazy } from 'react';  

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

- 이 패턴을 사용하려면 임포트하려는 지연 컴포넌트를 `default` 내보내기로 내보내야 한다.
- 이제 컴포넌트의 코드가 필요에 의해서만 로드되므로, 로드되는 동안 표시할 내용도 지정해야 한다. 지연 컴포넌트나 그 부모 컴포넌트를 [`<Suspense>`](https://react-ko.dev/reference/react/Suspense) 로 감싸면 됩니다.

```jsx
<Suspense fallback={<Loading />}>  
	<h2>Preview</h2>  
	<MarkdownPreview />  
</Suspense>
```

```jsx
const MarkdownPreview = lazy(() => delayForDemo(import('./MarkdownPreview.js')));
```

이렇게 하면, MarkdownPreview가 로딩이 필요할 때까지 로딩을 하지 않고, 필요한 순간 로드될때 로드 중이면 먼저  Loading 컴포넌트를 보여준다.

이 로딩된 값은 캐시되기 때문에 다시 한 번 로드될 때에는 Loading 표시가 보이지 않게 된다.

# **Troubleshooting**
### `lazy` 컴포넌트의 상태가 예기치 않게 초기화 됩니다
다른 컴포넌트 _안에서_ `lazy` 컴포넌트를 선언하면 안된다.
```jsx
import { lazy } from 'react';  

  

function Editor() {  

// 🔴 나쁨: 이렇게 하면 리렌더링시마다 모든 state가 초기화됨.
const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));  
// ...  
}
```

대신 늘 최상위에서많 호출해야 한다.
```jsx
import { lazy } from 'react';  

// ✅ Good: Declare lazy components outside of your components  
// ✅ 좋음: lazy 컴포넌트를 컴포넌트들의 밖에서 선언하세요  

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));  

function Editor() {  
// ...  
}
```