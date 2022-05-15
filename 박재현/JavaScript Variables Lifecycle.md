# JavaScript Variables Lifecycle 자바스크립트 변수 라이프사이클

## Phases 단계
### 1. Declaration Phase 선언 단계
변수를 실행 컨텍스트의 **Environment Record 환경 레코드에** 등록한다

### 2. Initialization Phase 초기화 단계
환경 레코드에 등록된 변수를 위한 **공간을 메모리에 확보**한다
- 이 단계에서 변수는 `undefined`로 초기화**된다

### 3. Assignment Phase 할당 단계
`undefined`로 초기화된 변수에 **실제 값을 할당**한다

## 예
### var keyword
var 키워드로 선언된 변수는 선언 단계와 초기화 단계가 **한번에** 이루어진다
`var number = 10;` 를 예로 들면,
1. Declaration Phase & Initialization Phase
2. (initialized state)
  - `variable === undefined`
  - 변수 선언문 이전에 변수에 접근해도 에러가 발생하지 않고 undefined를 반환한다 (호이스팅)
3. Assignment Phase
  - 비유하자면, `var number = 10;` 의 `number = 10` 할당 부분을 이제야 실행하는 것
4. (assigned state)
  - `variable === 10`

### let, const keywords
let, const 키워드로 선언된 변수는 선언 단계와 초기화 단계가 **분리되어** 이루어진다
1. Declaration Phase
2. (uninitialized state)
  - 변수에 접근하면 RefernceError 발생
  - 스코프에 변수를 등록(선언단계)하지만 초기화 단계는 변수 선언문에 도달했을 때 이루어진다  
3. Initialization Phase
2. (initialized state)
  - `variable === undefined`
3. Assignment Phase
4. (assigned state)
  - `variable === 'value'`

### function declaration 함수 선언식
1. Declaration Phase, Initialization Phase, Assignment Phase
2. (assigned state)
  - 함수 호출 가능

## 기타
### 선언과 정의
- declaration 선언
  - 컴파일러에게 어떤 대상의 `이름`을 알려주는 행위
- definition 정의
  - 컴파일러에게 어떤 대상의 실제 `내용`을 알려주는 행위

## references
- https://dmitripavlutin.com/variables-lifecycle-and-why-let-is-not-hoisted/