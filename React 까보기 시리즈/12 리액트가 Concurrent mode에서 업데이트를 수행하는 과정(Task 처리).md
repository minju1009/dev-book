
## Task 처리
뒤에 처리할 것은 Timer에 등록 or 지금 처리할 것은 실행 후 reconciler에게 return하여 root에 기입
생성된 Task 들은 binary heap(우선순위 큐)에 추가되어  정렬
![[Pasted image 20241002224618.png]]

![[Pasted image 20241002224204.png]]


### Timer
delay가 0보다 큰 Task(startTime이 currentTime보다 큰 것들)
timeout이 지나면 Task가 된다.
sortIndex에 startTime 할당(delay가 작은 것(먼저 시작되어야 하는 것)부터 앞에 정렬)
- 만약 taskQueue에 task가 없고, timerQueue에 최신이 새로 생성된 timer라면
	- 기존에 
	- 
handleTimeout
1순위 Timer가 timeout되면 호출되는 함수
--> advanceTimers(currentTime)으로 timer를 꺼내서 taskQueue에 추가

advanceTimers
- Timer가 있으면
	- Task가 취소되면 timer.callback이 null이 되므로 취소된 Task(Timer)를 꺼낸다
		- Timer가 만료되면 timerQueue에서 꺼내서 sortIndex를 바꾸고 taskQueue에 추가
		- Timer가 아직 만료되지 않았다면 return해서 바로 종료
	- 그리고 timerQueue에서 맨 앞에 timer를 꺼낸다(while 문)

### Task


```js
if(startTime > currentTime) {
 // timer
 newTask.sortIndex = startTime;
 push(timerQueue, newTask)
 if(peek(taskQueue) === null && newTask === peek(timerQueue))
 	requestHostTimeout(handletimeout, startTime - currentTime)
}else {
	newTask.sortIndex = expirationTIme
	push(taskQueue, newTask)
	if(!isHostCallbackScheduled && !isPerformingWork){
	requestHostCallback(flushWork)
	
	}
}
```