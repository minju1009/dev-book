# **Reference**
### `memo(Component, arePropsEqual?)`

`memo`로 컴포넌트를 감싸 메모화된 버전의 컴포넌트를 얻을 수 있다. 메모화된 버전의 컴포넌트는 일반적으로 props가 변경되지 않는 한 부모 컴포넌트가 리렌더링 할 때 같이 리렌더링하지 않는다. 그러나 메모화는 성능을 최적화하지만 보장되는 것은 아니어서, React는 여전히 리렌더링 할 수도 있다.

```jsx
import {memo} from 'react';

const SomeComponent = memo(function SomeComponent(props)){
	// ...					   
});
```

**Parameters**
- Component : 메모화 하려는 컴포넌트이다. `memo`는 