# 배경
scheduler의 역할은 WORK를 언제 처리할지 우선순위 판단
그 판단을 위한 객체가 Task!
즉, Task는 WORK를 언제 실행할 지 우선순위 판단을 위한 값을 가진 객체
(callback(performConcurrentWorkOnRoot), priorityLevel, expirationTime 값 등)
WORK는 fiber, reconciliation(재조정 작업)을 수행하기 위한 값을 가진 객체

# 사전 코드 흐름
1. requestHostCallback에 flushWork를 인자로 전달
2. 전달 받은 flushWork를 scheduledHostCallback에 할당
3. port.postMessage 호출하면 performWorkUntilDeadline 호출
4. performWorkUntilDeadline에서 scheduledHostCallback이 null이 아니면 hasTimeRemaining, currentTime 을 인자로 전달하여 호출
5. 호출한 결과값은 boolean 값(hasMoreWork 즉, WORK 가 더 있는지 판단)

# flushWork
isHostCallbackScheduled에 false 할당
isHostTimeoutScheduled에 false 할당 + cancelHostTimeout 호출
isPerformingWork = true (중복 WORK 실행 방지)
workLoop 호출 결과값 return
finally 안에서 currentTask에 null 할당
currentPriorityLevel에 다시 previousPriorityLevel
isPerformingWork = false

# workLoop
currentTask가 있다면 계속 while 돌아
currentTask의 만료 시간이 남았는데 scheduler에게 할당된 시간이 만료되면 break
currentTask에 할당된 callback(WORK) 꺼내오기
	- callback이 
		- null이 아니면, callback 호출(WORK 시작)
			- 잔여 작업을 return 하면 currentTask.callback = continuationCallback
			- 잔여 작업이 없다면 currentTask를 taskQueue에서 꺼내기
		- null 이면
			- Task 취소 taskQueue에서 꺼내기
다시 taskQueue 에서 맨 위 Task 꺼내와서 while 돌기

남은 task가 없으면 return true(끝)
있다면 다시 requestHostTimeout 호출(workLoop!)하고 return false(끝 아님)
