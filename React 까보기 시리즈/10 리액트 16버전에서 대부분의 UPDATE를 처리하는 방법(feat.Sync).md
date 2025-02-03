## scheduleWork(ScheduleUpdateOnFiber) 입구 함수!
```tsx
export function scheduleUpdateOnFiber(){
(생략)

// WORK가 동기적으로 호출되어야 하는 거다
if(expirationTime === Sync){
	// VDOM이 최초로 생성될 때
	if(
      	// Check if we're inside unbatchedUpdates
		(executionContext & LegacyUnbatchedContext) !== NoContext &&
		// Check if we're not already rendering
		(executionContext & (RenderContext | CommitContext)) === NoContext	
	){
		schedulePendingInteractions(root, expirationTime);

        // 바로 실행해버림
		performSyncWorkOnRoot(root);
	}
	// VDOM이 이미 생성되어 있을 때
	else{
		ensureRootIsScheduled(root);
		schedulePendingInteraction(root, expirationTime);
		if(executionContext === NoContext){
			fluchSyncCallbackQueue();
		}
	}
}

// 비동기처리일 때
else {
	ensureRootIsScheduled(root);
	schedulePendingInteractions(root, expirationTime);
}


}

```

reconciler 안의 코드를 scheduler가 가져와서 호출하지 않다. 반대도 마찬가지이다. 그러나 두 개가 연결되려면 통로가 필요하다. 이 함수는 scheduler가 reconciler 입구로 들어가기 위한 함수이다. -> 패키지 간의 의존성을 끊으려는 시도이다.


## 스케줄링과 관련된 로직은 ensureRootIsScheduled 에서
WORK를 스케줄링하기 전에 필요한 정보들 가져오기 -> 추가로 살펴볼 예정


