state는 객체를 포함해서, 어떤 종류의 JavaScript 값이든 저장할 수 있다. 그러나 React state에 있는 객체를 직접 변이해서는 안 된다. 대신 객체를 업데이트하려면 새 객체를 생성하고(혹은 기존 객체의 복사본을 만들고), 해당 복사본을 사용하도록 state를 설정해야 한다.

- React state에서 객체를 올바르게 업데이트하는 방법
- 중첩된 객체를 변이하지 않고 업데이트하는 방법
- 불변성이란 무엇이며, 불변성을 깨뜨리지 않는 방법
- Immer로 객체 복사를 덜 반복적으로 만드는 방법

## 변이란 무엇인가요?
숫자, 문자열, 불리언과 같은 원시자료형은 불변값이다. 즉, '변이'할 수 없거나 읽기 전용이다.

기술적으로 객체 자체의 내용을 변경하는 것(변이)는 가능하나, React state의 객체는 문자열과 같이 불변하는 것처럼 취급해야 한다. 객체를 직접 변이하는 대신 항상 교체해야 한다.

```javascript
const [position, setPosition] = useState({x:0, y:0});
position.x = 5; // 이렇게 변이하면 안 된다!!!
```

## state를 읽기 전용으로 취급하세요
다시 말해, state에 넣는 모든 JavaScript 객체를 읽기 전용으로 취급해야 한다.

- [리렌더링을 촉발](https://react-ko.dev/learn/state-as-a-snapshot#setting-state-triggers-renders)하려면 **새 객체를 생성하고 state 설정자 함수에 전달해야 한다!

```javascript
const [position, setPosition] = useState({x:0, y:0})

// 다음과 같이 하면 state를 변경하지 못한다.
onPointerMove= {e => {
				position.x = e.clientX;
				position.y = e.clientY;
}}

// 아래처럼 새로운 객체를 넣어 state를 업데이트 해줘야한다.
onPointerMove = {e => {setPosition({
				 x: e.clientX,
				 y: e.clientY
})}}
```
위 예시에서 setPosition으로 React에 다음을 지시하고 있는 것이다.
	- position을 이 새 객체로 바꿔라.
	- 이 컴포넌트를 다시 렌더링 하라.

## 전개 구문을 사용하여 객체 복사하기
`{...obj, something: 'newValue'}`
전개구문을 사용하여 얕은 복사를 통해 객체를 새로운 주소값을 갖도록 복사하고, 변경하고자 하는 key만 바꿔주는 식으로 업데이트 하면된다.

## 중첩된 객체 업데이트하기
중첩된 객체의 경우 중첩된 값을 변경시키고 싶다면 내부의 함수도 복사해줘야 한다. 실은 서로 주소값을 가리키고 있는 상태이기 때문이다. 
얕은 복사는 한 단계만 복사하기 때문에, 중첩된 객체는 복사하지 않고 동일한 주소값을 가리킨다. 따라서 한 단계 더 복사를 해 줘야만 중첩된 객체를 업데이트 할 수 있다.





### Updating Arrays in State
- 배열도 마찬가지!