Render phase는 변경된 상태 값을 반영한 workinProgress 트리 구축 과정을 일컫는다.
1. Host root부터 업데이트 컴포넌트까지 current 복제하여 workinProgress 트리를 만든다.
2. 업데이트 컴포넌트를 호출하여 변경된 상태 값을 반영한 React elemnt를 fiber로 확장한다.
3. 2의 과정을 leaf 노드까지 반복하여 workInProgress 트리를 완성

## 1. workLoop
- 개별 fiber에 Work 진행
- workInProgress가 null일 때까지, 자식 끝까지 진행
```tsx
function workLoopSync(){
	while(workInProgress !=== null){
		workInProgress = performUnitOfWork(workInProgress)
	}
}
```

## 2. performUnitOfWork()
- biginWork(자식반환) -> completeUnifOfWork(형제 반환)
```js
 unitOfWork.memoizedProps = unitOfWork.pendingProps
```
performUnitOfWork()가 호출될 때의 시점을 보면 `unitOfWork`의 부모는 이미 자식을 반환한 상태이므로 props는 변경될 일이 없습니다. 또한 `beginWork()`가 끝났다면 자신도 마찬가지로 자식을 반환한 후이므로 더이상 props로 영향을 줄 만한 상황이 남아있지 않습니다. 즉 7 라인에 도달하면 props로 영향을 받거나 줄 수 있는 상황은 일어나지 않으므로 더 이상 props를 pending 상태로 두지 않고 확정 지어 주는 것입니다.

## 3. beginWork()
- workInProgress의 컴포넌트를 호출해야 하는지 판단하는 것
- 호출판단 기준은?
	- 1) 컴포넌트 상태 변경
	- 2) props 변경

### 1) 컴포넌트의 상태 변경 판단
fiber를 가지고 어떻게 상태 변경을 판단?
간단하게 업데이트가 발생한 컴포넌트에 플래그 하나만 달아주면 된다.
이미 markUpdateTimeFromFiberToRoot()를 통해 이를 해 놓음 (expirationTime, childExpirtionTime)

현재 fiber에 chileExpirationTime만 설정되어 있다면 자손이 업데이트 된 것이므로 쭉 내려가면서 current를 단순 복제하여 workInProgress 트리를 구축, 
이를 반복하다가 expirationTime이 renderExpirationTime과 같은 컴포넌트를 찾으면 바로 해당 컴포넌트가 현재 진행되는 Work를 발생시킨 대상이며 재호출이 필요한 컴포넌트

### 2) Props 변경 판단
부모이ㅡ 재호출

## 4. bailoutOnAlreadyFinishedWork()
상태 변경 컴포넌트의 서브트리를 제외한 Host root ~ 업데이트 컴포넌트까지는 React Element 기준으로 변경된 것이 없다. 그래서 굳이 fiber로 새로 만들지 않고 currnet 를 재사용하여 workInProgress를 만들어낸다.

fiber는 컴포넌트 모델이 변경된 경우, 즉 React element가 변경된 경우에만 fiber로 다시 확장 시킨다.
그리고 이게 변경될 일은 props밖에 없다. 내부 상태값은 상관없음(useState)

-> memo , shouldComponentUpdate를 통해 빠져나갈 수 있도록 함

## 5. 재조정 전 사전 작업 공간인 update *** ()
여기까지 도달했다면 workInProgress는 업데이트 컴포넌트를 뜻한다.
컴포넌트를 호출하여 변경된 상태를 반영한 자식을 반환받아 재조정 작업을 진행해야 한다.

### IndeterminateComponent
- fiber의 type이 함수일 때, 실수할 수 있기 때문에
- 여기서 확정지어준다. 클래스 컴포넌트인지, 함수형 컴포넌트인지

### FunctionComponent
- 함수형 컴포넌트는 호출하기에 앞서 default props를 먼저 처리
- 마지막에는 항상 Work를 진행했음을 나타내는 effect tag를 달아줌

### HostComponent
- 커스텀 컴포넌트는 상태값을 가지고 있기 때문에 이를 적용하기 위한 호출이 필요하지만,
- 호스트 컴포넌트는 뷰 그 자체이므로 변경된 정보를 바탕으로 호스트 환경에 의존적인 처리가 필요하다.


