
performConcurrentWorkOnRoot 1
performConcurrentWorkOnRoot 2

workInProgressRoot로 잡기 가능

root는 최우선 Task가 참조하고 있는 root
즉, root가 다르다는 것은 다른 VDOM의 root가 할당되었다. -> 그 VDOM을 먼저 처리해야 한다.
그 처리를 위한 준비가 prepareFreshStack호출이다.

기존에 처리하고 있던 workInPrgressRoot를 초기화해야함

expirationTime과 renderExpirationTime이 다르다?
재조정 작업 전에 항상 초기화

브라우저의 콜스택, 싱글스레드, 현재 1을 처리하고 있으면 2를 처리할 수 없음. 만약 root가 바뀌면 prepareFreshStack을 위해 기존꺼를 던져서 클리어 해버리고 새로운 task를 담기 위한 준비를 한다.

performConcurrnetWorkOnRoot(비동기) -> perfom SyncWorkOnRoot 시간이 지나면 concurrnetWork가 syncWork로 변경됨 (동기)

expirationTime 


