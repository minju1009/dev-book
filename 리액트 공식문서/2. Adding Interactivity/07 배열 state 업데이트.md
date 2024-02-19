배열도 객체와 같다. state에 저장된 배열을 업데이트하려면, 새로운 배열을 만들고(혹은 기존의 배열을 복사본을 만들고) 새 배열을 사용하도록 state를 설정해야 한다.

- React state 안의 배열 항목을 추가, 제거 또는 변경하는 방법
- 배열 내부의 객체를 업데이트 하는 방법
- Immer로 덜 반복적으로 배열을 복사하는 방법

## 변이 없이 배열 업데이트하기
- adding 
	- X - push, unshift
	- O - concat, [...arr]

- removing
	- X - pop, shift, splice
	- O - filter, slice

- replacing 
	- X - splice, arr[i] = ... 
	- map

- sorting 
	- X - reverse, sort
	- copy the array first!!

## 배열 내부의 객체 업데이트하기
중첩된 객체처럼, 배열 내부의 객체 자체도 복사본을 만들어준 후 변경해야 한다.