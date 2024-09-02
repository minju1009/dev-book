07번 내용과 이어짐

### expirationTime
```tsx
export function getCurrentTime(){
	return msToExpirationTime(now());
}

// now 라는 함수는 ScehdulerWithReactIntegration.js에서 찾을 수 있음
export const now = 
	  initialTimeMs < 10000 ? Scheduler_now: () => Scheduler_now() - initialtimeMs;
	  // `performance.now` - 즉 브라우저를 키자마자부터 측정하는 시간 
	  // now가 커지면 더 오래된 것


// 시간이 흐를 수록 더 많이 빼니까, expirationTime값은 작아짐
// event가 더 먼저 발생할수록 msToExpirationTime은 커짐 
export function msToExpirationTime(ms:number): ExpirationTime{
	return MAGIC_NUMBER_OFFSET - ((ms/UNIT_SIZE | 0)
}


export function computeExpirationForFiber(
 currentTime: ExpirationTime,
 fiber:Fiber
)
```

currentTime -> 


### MAGIC_NUMBER_OFFSET
```tsx
const Sync = MAX_SIGNED_31_BIT_INT;
const Batched = Sync - 1;

const MAGIC_NUMBER_OFFSET = Batched - 1;
```

어차피 상수값인데, 왜 굳이 MACIG_NUMBER_OFFSET 상수를 만들었을까?
Sync 값이랑 우리가 계산해서 얻은 msToExpirationTime이랑 같아져 버리면 어떤게 어떤 건지 알 수가 없으니까!
Sync는 '동기적으로 처리해야한다'를 표현하는 값.
나중에 expirationTime이 Sync이면 아 이거 동기적으로 처리해야 하는거야~ 라고 이해할 수 있다.
