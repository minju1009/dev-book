## 불필요한 Effect를 제거하는 방법
- 렌더링을 위해 데이터를 변환하는 경우 Effect는 필요하지 않다.
- 사용자 이벤트를 처리하는 데에 Effect는 필요하지 않다.

## props 또는 state에 따라 state 업데이트하기
```js
function Form() {
	const [firstName, setFirstName] = useState('Taylor');
	const [lastName, setLastName] = useState('Swift');

	// 좋습니다~!! 렌더링 과정 중에 계산
	const fullName = firstName + ' ' + lastName

}
```
## 고비용 계산 캐싱하기
연산이 무겁지 않다면 렌더링 중에 계산하도록 하고, 무겁다면 useMemo로 감싸 캐싱하도록 한다.
useMemo로 감싸면 React에게 의존성 배열에 있는 값이 변경되지 않는 한 내부 함수가 다시 실행되지 않기를 원한다는 것을 React에게 알릴 수 있다.

계산이 비싼줄을 확인하려면 console.time을 통해서 확인해 볼 수 있다.
```js
console.time('filster array');
const visibleTodos = getFilteredTodos(todos, filter);
console.timeEnd('filter array');
```
일반적으로 기록된 시간이 1ms이상 정도에 해당한다면 해당 계산은 메모해 두는 것이 좋을 수 있다.

useEffect안에서 데이터 페칭을 했을때, 앞에 불렀던 호출이 뒤에 불렀던 호출의 응답 이후에 도착할 수 있다. 이를 '경쟁 조건'이라고 하는데, 서로 다른 두 요청이 서로 '경쟁'하여 예상과 다른 순서로 도착하는 경우를 의미한다.
> 경쟁 조건을 수정하기 위해서는 오래된 응답을 무시하도록 클린업 함수를 추가해야 한다.