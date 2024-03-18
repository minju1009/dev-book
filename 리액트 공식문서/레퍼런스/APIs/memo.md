
memo`를 사용하면 컴포넌트의 props가 변경되지 않은 경우 리렌더링을 건너뛸 수 있습니다.
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
- Component : 메모화 하려는 컴포넌트이다. `memo`는 이 컴포넌트를 수정하지 않고 새롭게 메모화된 컴포넌트를 반환한다. 함수와 [`forwardRef`](https://react-ko.dev/reference/react/forwardRef) 컴포넌트를 포함한 모든 유효한 React 컴포넌트가 허용된다.

**Returns**
`memo`는 새로운 React 컴포넌트를 반환한다. `memo`에 제공한 원본 컴포넌트와 동일하게 동작하지만, 부모가 리렌더링 하더라도 prop이 변경되지 않는 한 React는 이를 리렌더링하지 않는다.

# **Usage**
### 1. prop이 변경되지 않았을 때 리렌더링 건너뛰기
React는 일반적으로 부모가 리렌더링될 때마다 컴포넌트를 리렌더링 한다. `memo`를 사용하면 부모 컴포넌트가 리렌더링될 때 새로운 props가 이전 props와 동일하면 리렌더링하지 않는 컴포넌트를 만들 수 있다. 이러한 컴포넌트를 _메모화되었다고_ 한다.

- `memo`를 적용하더라도 컴포넌트의 state가 변경되거나 사용중인 context가 변경되면 리렌더링 한다.

```jsx
const Greeting = memo(function Greeting({ name }) {
  console.log("Greeting was rendered at", new Date().toLocaleTimeString());
  return <h3>Hello{name && ', '}{name}!</h3>;
});
```

이 컴포넌트는 오로지 `name`이 변경되었을 때만 변경된다.

**모든 곳에 memo를 추가해야 할까?**
- 아니다!
- `memo`로 최적화하는 것은 컴포넌트가 정확히 동일한 props로 자주 리렌더링되고, 리렌더링 로직이 비용이 많이 들 때만 가치가 있다. 컴포넌트가 리렌더링되어도 인지할 수 있을 만큼의 지연이 없다면 `memo`가 필요하지 않다.
- memo 사용의 단점 : 가독성이 떨어지고, 모든 메모화가 효과적이지도 않다. 

### 2. state를 사용하여 메모화된 컴포넌트 업데이트
컴포넌트가 메모화된 경우에도 자체 state가 변경되면 리렌더링된다.  메모화는 부모에서 컴포넌트로 전달되는 props에만 연관이 있다.

### 3. context를 사용하여 메모화된 컴포넌트 업데이트하기
컴포넌트가 메모화 되었더라도 사용 중인 context가 변경될 때 이 컴포넌트는 리렌더링된다. 메모화는 부모로부터 전달되는 props에만 연관이 있다.

### 4. props 변경 최소화하기
`memo`를 사용할 때 어떤 prop든 이전의 prop과 _얕은 비교 결과 같지_ 않을 때마다 컴포넌트가 다시 작동한다. 즉, React는 [`Object.is`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/is) 비교를 사용하여 컴포넌트의 모든 prop을 이전 값과 비교한다. `Object.is (3, 3)`은 `true`이지만 `Object.is({}, {})`는 `false`이다. (주소값이 다르기 때문에)

따라서 `memo`를 최대한 활용하려면, props가 변경되는 시간을 최소화해야 한다. 예를 들어, prop이 객체인 경우, [`useMemo`](https://react-ko.dev/reference/react/useMemo)를 사용하여 부모 컴포넌트가 해당 객체를 매번 다시 만드는 것을 방지하라.

```jsx
function Page() {  

const [name, setName] = useState('Taylor');  
const [age, setAge] = useState(42);  

// useMemo로 감싸서 객체 변경 최소화 (아니면 매번 새로운 객체가 만들어진다! 아무 의미 없어진다!)
const person = useMemo(  
	() => ({ name, age }),  
[name, age]  
);  


return <Profile person={person} />;  
}  


const Profile = memo(function Profile({ person }) {  
// ...  

});
```

이런 경우에는 객체보다는 각각의 값을 전달하는 것이 더 나을 수도 있다.
```jsx
function Page() {  
	const [name, setName] = useState('Taylor');  
	const [age, setAge] = useState(42);  
	return <Profile name={name} age={age} />;  

}  

  
const Profile = memo(function Profile({ name, age }) {  
// ...  

});
```

함수를 전달해야 하는 경우도 마찬가지이다. 메모화된 컴포넌트에 함수를 전달해야 하는 경우, 아예 변경되지 않도록 컴포넌트 외부에서 선언하거나, [`useCallback`](https://react-ko.dev/reference/react/useCallback#skipping-re-rendering-of-components)을 사용하여 정의를 캐시하는 것이 좋다.

# **TroubleShooting**

### prop이 객체, 배열 또는 함수일 때 컴포넌트가 리렌더링되는데요 ?

React는 이전 prop과 새 prop을 얕은 비교로 동등성을 파악한다.

즉, 각각의 새 prop이 이전 prop과 `참조`가 동일한지 여부를 고려한다.

부모가 다시 렌더링될 때마다 새 객체나 배열을 생성하면 설령 개별 요소들이 모두 동일하더라도 여전히 React는 변경된 것으로 간주한다. 마찬가지로 부모 컴포넌트를 렌더링할 때 새 함수를 만들면 React는 함수의 정의가 동일하더라도 변경된 것으로 간주한다. 이를 방지하려면 [부모 컴포넌트에서 prop을 단순화하거나 메모화 하면 된다.](https://react-ko.dev/reference/react/memo#minimizing-props-changes)