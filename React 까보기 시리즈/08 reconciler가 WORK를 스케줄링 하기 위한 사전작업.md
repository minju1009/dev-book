## 배경
scheduler는 적절한 타이밍에 우선순위를 판단하여 WORK를 실행
WORK 실행에 관한 우선순위 관련 값은 언제 이벤트가 발생했느냐,
즉 expiration Time은 이벤트 발생 시점.
이 값을 reconciler가 fiber에 expirationTime 할당(scheduleWork 함수는 reconciler가 호출)

## reconciler의 사전 작업
1. 해당 컴포넌트에서 이벤트가 발생했음을 알려주는 expirationTime을 할당
2. 이벤트가 발생한 컴포넌트의 VDOM root를 가져옴
3. root에 스케줄링 정보를 기록

### root란?
ReactDOM.render()호출로 컴포넌트를 삽입하는 부모 태그가 root`  <div id = "root"></div>`
root : VDOM = 1:1 DOM 업데이트를 위한 ㅈWORK 실행 우선순위 관련 값인 스케줄링 정보는 root에 할당! 


```js
export const scheduleWork = scheduleUpdateOnFiber;
// 업데이트를 스케줄하는 것

export function scheduleUpdateOnFiber (){

...
const root = makrUpdateTimeFromFiberToRoot(fiber, expirationTime);
if(root === null){
	warnAboutUpdateOnUnmoutedFiber...
}
}

// 여기서 받는 fiber는 current임.
function makrUpdateTimeFromFiberToRoot(fiber, expirationTime){
	
	// expirationTime이 클 수록 먼저 처리해야하는 업데이트구나..! 앞서 발생한 업데이트구나..!
	if(fiber.expirtationTime < expirationTime){
		// 먼저 실행해야 하므로 큰 값을 할당
		fiber.expirationTime = expirationTime;
	}


    // 둘중에 어떤게 current인지 Workinprogress인지 모르니 두 번 진행
	let alternate = fiber.alternate;
	if(alternate !== null && alternate.expirationTime < expirationTime){
		alternate.expirationTime = expirationTime
	}


	// 부모를 return으로 참조하고 있음
	let node = fiber.return;
	let root = null; 
	// 최상위 라는 것!!
	if(node === null && fiber.tag === HostRoot){
		root = fiber.stateNode; // root 찾음!
	}else{
	// root가 아닐 때, root 찾을 때까지 계속 while문 돌림
	while (node !== null){
		alternate = node.alternate;
		if(node.childExpirationTime < expirationTime) {
			node.childeExpirationTime = expirationTime;
		}
		if(
			alternate !== null && 
			alternate.childExpirationTime < expirationTime
		)

	}

	....

	return root;
	}


}

```

![[Pasted image 20240826214319.png]]