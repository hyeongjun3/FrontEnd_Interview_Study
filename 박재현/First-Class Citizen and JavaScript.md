# First-Class Citizen and JavaScript 1급 시민과 자바스크립트

## First-Class Citizen 1급 시민
프로그래밍에서 다음의 조건을 만족 시키는 것들
- variable 변수에 할당할 수 있다
- 함수 (혹은 메서드)의 parameter 인자로 전달할 수 있다
- 함수의 (혹은 메서드) return value 반환 값으로 이용할 수 있다

## 기타
- 대부분(거의 모든)의 프로그래밍 언어에서 숫자형/문자형 등은 1급 시민의 조건을 충족한다
- 함수에 대한 경우 추가 조건이 요구되기도 한다
  - runtime 런타임 생성이 가능하다
  - anonymous 익명으로 생성이 가능하다

## JavaScript 의 객체와 1급 시민
자바스크립트에서 객체는 1급 시민이다

## JavaScript 의 함수와 1급 시민
자바스크립트에서 함수는 1급 시민이다

### JavaScript 의 함수가 1급 객체인 것이 중요한 이유
- high order function 고차 함수가 가능하다
  - 함수를 인자로 받거나 함수를 반환하는 함수 
  - 인자로 함수 전달 예
    - each, filter, map, sort
- closure 클로저를 활용할 수 있다
  - JavaScript의 함수는 생성될 당시의 Lexical Environment를 기억하게 되는데, 함수를 주고받게 되면 이 Lexical Environment도 함께 전달된다
  - 이것을 이용해서 currying 커링, memoization 이 가능해 진다

### references
- https://seungwoohong.tistory.com/15
- https://bestalign.github.io/dev/first-class-object/