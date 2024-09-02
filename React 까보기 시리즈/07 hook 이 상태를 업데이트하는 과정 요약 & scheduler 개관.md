reconciler는 비동기 호출이 필요한 WORK의 실행 제어권을 scheduler 에게 위임.
scheduler가 브라우저 상태와 다양한 조건을 확인하여 적절한 시기를 판단하고 WORK를 실행

reconciler가 WORK를 스케줄링 하고 scheduler에 의해 실행되는 흐름에서 각 패키지의 역할

### reconciler
- VDOM 재조정 작업 전에 설정해야 하는 부분들 처리
### scheduler
- 스케줄링 된 Task에 우선순위 반영과 실행하기 적절한 때를 판단하고 작업의 실행과 중단을 담당
- host config 모듈 존재, 호스트 환경에 의존적인 API 사용하는 모듈(비동기 API, performance, isInputPending 등)