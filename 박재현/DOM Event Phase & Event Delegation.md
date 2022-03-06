# DOM Event Phase & Event Delegation 이벤트 단계와 이벤트 위임

## Event Phase 이벤트 단계
특정 요소 a에서 이벤트를 트리거했을 때
1. window 객체에서 요소 a까지 이벤트가 전파된다 (Capturing phase 캡처링 단계)
2. 요소 a
3. 요소 a에서 window 객체로 이벤트 전파 (Bubbleing phase 버블링 단계)

이때 요소 a의 부모인 요소 b에 addEventListener 로 eventhandler 를 붙이면,
- 기본적으로 요소 b에 버블링 단계로 이벤트가 전파되었을 때 eventhandler가 실행된다
- 단 설정을 수정하면 요소 b에 캡쳐링 단계로 이벤트가 전파되었을 때 eventhandler가 실행된다
  ```
    elementB.addEventListener(..., {capture: true})
    elementB.addEventListener(..., true)
  ```

## Properties of Event object 이벤트 관련 속성
- `Event.target`: 이벤트가 발생한 가장 구체적인 (안쪽의) 요소
- `Event.currentTarget`: this, eventListener 가 달린 요소
- `Event.eventPhase`: 현재 이벤트 단계 (캡처링=1, 타깃=2, 버블링=3)

## Methods of Event object 이벤트 관련 메서드
- `Event.stopPropagation()`: 상위 요소로 이벤트 전파를 하지 않는다 (버블링 단계를 일으키지 않는다)
  - 즉, 상위 요소의 eventhandler 가 발생하지 않는다 (기본설정인 capture: false 일때)
- `Event.preventDefault()`: 해당 이벤트의 기본 동작을 실행하지 않는다 
  - 예: form 요소에서 onsubmit 시 새로고침하는 기본 동작을 실행하지 않고 싶을 때 이용

## Event Delegation 이벤트 위임
- 이벤트핸들러를 각각의 하위 요소에 붙이지 않고, 상위 요소에 붙이는 개발 방식
  - event bubbling 을 응용한 것
- 장점
  - 이벤트 핸들러를 한번만 붙이면 된다
    - 메모리가 사용량이 줄어든다 
  - 자식요소가 이후에 새로 생길 때 이벤트핸들러를 추가로 붙여줄 필요가 없다
    - 또한 자식 요소에가 제거될때 이벤트핸들러를 해제할 필요도 없다
- 사용 예
  - 부모요소에 이벤트 핸들러를 달고 `Event.target` 의 `.classList`, `.matches(선택자)` 등을 확인해서 어느 자식 요소에서 이벤트가 발생했는지 파악한다
- 주의사항
  - React 에서는 이벤트위임을 자동으로 해준다. 
    - 자식 요소에 이벤트핸들러를 붙였다고 생각하지만 React가 자동으로 root 요소에 이벤트 위임을 해준다



### reference
- https://www.youtube.com/watch?v=7gKtNC3b_S8
- https://ko.javascript.info/bubbling-and-capturing