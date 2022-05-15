# React Virtual DOM 리액트 가상 돔

## 용어
### DOM (Document Object Model)
HTML 문서의 내용과 구조가 객체 모델로 만든 것
- HTML (혹은 XML) 문서에 접근하기 위한 일종의 인터페이스
- 트리 형태로 되어 있어서 특정 노드를 찾거나, 수정, 제거할 수 있다

### Virtual DOM
DOM을 좀더 추상화시킨 자바스크립트 객체
- 매번 실제 DOM에 접근하여 조작하는 대신에, 
- DOM의 상태를 메모리에 저장하고 변경 전과 변경 후의 상태를 비교한 뒤 DOM에 변경이 있을 때만 해당 변경을 반영한다

### React DOM
Virtual DOM에서 HTML을 생성하는 데 필요한 라이브러리
- React DOM에는 리액트 엘리먼트를 브라우저에 렌더링하는데 필요한 모든 도구가 들어 있다

### Shadow DOM
DOM의 구조를 가지고 있으나, 외부에 노출되지 않은 DOM
- DOM의 구조를 캡슐화할 때 사용한다
- 외부의 style은 적용되지 않는다
- shadow DOM을 추가하거나 접근하기 위해서는 별도의 방법이 필요하다
- 렌더링 비용을 줄일 수 있다
  - 일반적인 DOM은 트리가 렌더링 된후 DOM트리가 수정되서 비용이 크지만, Shadow DOM은 Shadow Host를 만나는 순간 렌더링 되기 때문
- 용어
  - shadow host
    - DOM이 부착되는 통상적인 DOM 노드
  - shadow tree
    - shadow DOM 내부의 DOM 트리
  - shadow boundary
    - shadow DOM이 끝나고, 통상적인 DOM이 시작되는 장소
  - shadow root
    - shadow tree 의 root 노드

### Reconciliation 재조정
리액트에서 컴포넌트가 리렌더링될 때, 요소를 비교해서 업데이트를 결정하는 프로세스

## 원리
### DOM에 변화가 생길 때 일어나는 과정
1. CSS의 재연산
2. Reflow
3. Repaint

### Virtual DOM 을 사용하는 이유
DOM의 변화는 많은 작업을 일으키므로, DOM의 변화를 최소화하기 위해 Virtual DOM을 사용한다
- DOM 자체가 느리다는게 아니다

### Virtual DOM 이용 과정
1. 업데이트된 전체 UI를 Virtual DOM에 리렌더링한다
2. 이전 Virtual DOM에 있던 내용과 현재의 내용을 비교한다
3. 바뀐 부분만 실제 DOM에 적용
  - 컴포넌트가 업데이트 될때, 레이아웃 계산이 한번만 이루어진다

### Virtual DOM 이 항상 빠를까?
작은 규모의 어플리케이션에는 적합하지 않다
- 지속적으로 데이터가 변하는 대규모 어플리케이션에 적합하다

### 비교 알고리즘 (Diffing Algorithm)
요소의 타입이 다른 경우
- 이전의 트리를 버리고, 완전히 새로운 트리를 구축한다
- 예
  - <a>에서 <img>로 수정
  - <Article>에서 <Comment>로 수정
  - <Button>에서 <div>로 수정

DOM 요소 타입이 같은 경우
- 두 요소의 속성을 확인하여, 동일한 내역은 유지하고 변경된 속성들만 갱신합니다
- 예
  - className 만 수정
  - style 에서 변경된 속성만 수정

컴포넌트 타입이 같은 경우
- 컴포넌트가 업데이트되면 인스턴스는 동일하게 유지되어 렌더링 간 state가 유지된다
- 새로운 요소의 내용을 반영하기 위해 현재 컴포넌트 인스턴스의 props를 갱신한다

### key의 활용
자식 요소들이 리스트로 이루어져 있을 때, key를 통해 동일한 자식을 파악해서 효율적으로 작업한다

## references
- https://brunch.co.kr/@eight-two-five/14
- https://akdl911215.tistory.com/326
- https://ko.reactjs.org/docs/reconciliation.html