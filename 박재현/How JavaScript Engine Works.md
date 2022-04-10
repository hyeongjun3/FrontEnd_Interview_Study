# How JavaScript Engine Works 자바스크립트 엔진 동작 방식

## Javascript engine
자바스크립트 소스코드를 기계어로 변환시켜주는 존재

## 특징
- 인터프리터와 컴파일러 혼합되어 있다
- single thread 싱글 스레드 프로그래밍 언어

## 구조 
### Memory Heap
- 참조 타입(객체 등) 데이터가 메모리에 할당된다
  - 동적으로 할당되는 변수의 경우, 컴파일러는 얼마나 많은 메모리를 필요로 할지 알 수가 없다
  - 따라서 콜 스택에 변수를 위한 공간을 할당할 수 없기 때문에, 동적 변수를 런타임 시점에 Heap 공간에 할당한다

### Call Stack
- 코드가 실행되면서 생성되는 Execution Context를 저장하는 자료구조
  - 자바스크립트는 하나의 콜 스택을 가지며 (싱글 스레드) 기본적으로 한번에 하나의 태스크만 처리할 수 있다
- 원시 타입 (문자열, 숫자 등) 데이터가 메모리에 할당된다

Execution Context 실행 컨텍스트
- 자바스크립트 코드가 실행되는 환경
  - 태스크가 요청되면 콜 스택을 통해 순차적으로 실행한다
  - 함수가 호출되면 call stack에 push 되고, 결과를 반환하면 pop 된다
- 과정 예
  ```jsx
  var a = 1;
  var b = 2;

  function sum () {
    var c = 3;
    var d = a + b + c;
    
    function print () {
      console.log(d)
    }
    print();
  }

  sum()
  ```
  1. 전역 컨텍스트가 호출되면서 **전역 실행 컨텍스트**가 실행 스택에 push되고
    - `[전역 EC]`
  2. sum 함수가 호출 및 실행되면서 **sum 함수의 함수 실행 컨텍스트**가 실행 스택에 push
    - `[전역 EC, sum 함수의 EC]`
  3. print 함수가 호출 및 실행되면서 **print 함수의 함수 실행 컨텍스트**를 실행 스택에 push  
    - `[전역 EC, sum 함수의 EC, print 함수의 EC]`
  4.  print 함수의 실행이 끝나면 print 함수의 함수 실행 컨텍스트는 pop
    - `[전역 EC, sum 함수의 EC]`
  5. sum 함수의 실행 또한 완료되며 함수 실행 컨텍스트가 pop
    - `[전역 EC]`

비동기 처리 실행 과정
- 실행할 준비를 마친 이벤트 처리기 함수와 비동기 처리는 실행하기에 앞서 이벤트 큐에 대기 행렬을 만든다
- 현재 실행 중인 함수의 작업이 끝나면, 실행을 기다리는 첫번째 실행 컨텍스트 부터 차례대로 콜 스택에 push 해서 실행해 나간다


## 대표적인 예
- V8
  - Chrome, Node.js, Electron에서 사용
- JavaScriptCore
  - Safari, React Native 에서 사용
    - 리액트 네이티브를 크롬에서 디버깅할 때는 V8 사용
- Chakra
  - Explorer, Edge에서 사용
- SpiderMonkey
  - Firefox에서 사용

## 코드 해석 과정 (V8)
Adaptive JITC 방식
1. Tokenizing 토크나이징
  - JavaScript soure code를 분석해서, 의미를 갖는 최소 단위인 Token 토큰들으로 분해 
2. Parsing 파싱
  - 분해한 토큰들을 분석해서, 문법적으로 의미를 갖는 AST (Abstract Syntax Tree) 생성
3. Interpreter 로 AST를 byte code 로 변환
  - V8 의 경우 Ignition 이라는 인터프리터를 사용한다
  - 코드가 한줄한줄 실행될 때마다 바이트 코드로 바꾸고 실행하는 것
4. 바이트 코드를 실행
  - 이 바이트 코드를 실행함으로서 실제로 작동한다
  - 다만, 자주 사용되는 코드는 기계어로 한번 더 컴파일돼서 실행한다
    - V8 의 경우 TurboFan 이라는 컴파일러를 사용한다
  - 다시 사용이 덜 된다 싶으면 Deoptimizing 하기도 한다 (기계어 -> 바이트코드)

### Adaptive JITC 방식
- interpreter 와 JITC 를 유동적(adaptive)으로 적용하는 방식
  - 이벤트 처리 관련 코드들은 반복되서 실행되는 것이 많아서 interpreter가 효율이 좋다
  - 비지니스 로직 관련 코드들은 반복되서 실행되는 것이 적어서 JITC가 효율이 좋다
- 변수의 type이 변하지 않는다면 높은 성능을 얻을 수 있다
- 최근 JavaScript 엔진들은 대부분 이 방식을 사용하고 있다

### JITC 
- Just In Time Complier
- runtime 시점에 기계어로 번역

### Profiling
- 바이트 코드를 실행할 때 자주 사용되는 코드 (변수들의 타입, 값)를 수집하는 것
- 이를 JITC 에 전달한다 
  - 컴파일러 입장에서 실행시점까지 동적 타입을 알 수 없는데, 이를 Prifiling 을 통해 받는 것

### IR (Intermediate Representation)
- profiling data + byete code
- 인터프리터 모드라면 IR 을 바로 읽어서 실행하고, JIT 모드라면 IR 을 기계어로 번역하고 실행한다

### Hotspot
- 자주 반복돼서 실행되는 구간 (코드)

## references
- https://joshua1988.github.io/web-development/translation/javascript/how-js-works-inside-engine/
- https://evan-moon.github.io/2019/06/28/v8-analysis/
- https://m.blog.naver.com/z1004man/221914280533
- https://corock.tistory.com/467
- https://watermelonlike.tistory.com/170
- https://meetup.toast.com/posts/77
- https://curryyou.tistory.com/237
- https://curryyou.tistory.com/276
- https://joontae-kim.github.io/2020/10/12/excution-context/