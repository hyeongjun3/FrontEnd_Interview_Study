# Programing Language and Process 프로그래밍 언어와 처리

##  처리 과정 예

### 컴파일 언어
- 고급 언어로 작성된 프로그램
- -(Compiler 컴파일러)->
- 어셈브리어
- -(어셈블러)->
- 목적 파일 (*.obj)
- -(Linker 링커)->
- 기계 언어 (*.exe)
- (원하는 시점에) 실행

### 인터프리터 언어
- 고급 언어로 작성된 프로그램
- -(Interpreter 인터프리터)->
  - -(compiler)->
  - bytecode 바이트코드
  - -(virtual machine)->
- 기계 언어
- 바로 실행

## 언어 분류

### High level language
- 일상적인 언어 수준에 가까운 고수준언어
- 처리기에 따른 분류
  - 인터프리터 언어 (스크립트 언어)
    - JavaScript, Python, LISP, Perl, Ruby
  - 컴파일러 언어
    - C, Java, Objective C


### Low level language
- 기계어와 1:1 대응되는언어 (기계 중심적언어)
- CPU의 종류에 따라 서로 다른 코드를 가짐
- 예
  - Assembly Language 어셈블리어

### Machine language
- 프로그램을 나타내는 가장 낮은 단계의 언어
  - 기계가 직접 이해할 수 있는 2진수 언어
  - CPU 가 바로 해독할 수 있는 유일한 언어
  - 모든 프로그래밍 언어의 종착지
  - 데이터를 bit 수준으로 나타내고, 기계 명령을 직접 표현함
- CPU의 종류에 따라 서로 다른 코드를 가짐

## 처리기 분류
### Assembler 어셈블러
- 어셈블리어로 작성된 원시 프로그램을 기계어로 된 목적 프로그램으로 변환한다

### Compiler 컴파일러
- 고급 언어로 작성된 프로그램을 번역해서 오브젝트 코드로 변환한다
  - 바로 실행하지 않을 수 있다

### Interpreter 인터프리터
- 소스 프로그램을 한 줄씩 기계어(bytecode)로 해석해서 바로 실행하는 것을 반복

### 컴파일러 vs 인터프리터
- 속도
  - 컴파일러
    - 코드 실행 전 전체를 컴파일 하는 과정이 필요하기 때문에 초기에 속도가 느리다
    - 하지만 실행 속도는 빠르다
  - 인터프리터
    - 한줄 한줄 변환하기 때문에 실행 속도가 빠르다
    - 처리 속도는 빠르지만, 총 실행시간은 오래걸린다
- 반복 작업
  - 컴파일러
    - 반복되는 작업이 있으면 불필요한 동작을 제거해서 최적화(optimization) 한다
      - 예를 들어 특정 함수를 10억번 반복해야 할 경우, 컴파일 과정에서 함수를 반복하는 것이 아니라 함수의 결과물을 반복하도록 컴파일 한다. 
  - 인터프리터
    - 같은 결과를 반복해도 매번 반복해서 변환, 실행한다
- 에러, 디버깅
  - 컴파일러
    - 한번에 번역하기 때문에, 전체에 대한 실행파일을 만들어서 실행해봐야 에러를 확인할 수 있다
  - 인터프리터
    - 줄 단위로 번역과 실행하기 때문에 중간에 문제 있는 코드를 만난 경우, 그 줄 아래는 실행하지 않는다
    - 디버깅하기 쉽다
- 실행 파일, 메모리
  - 컴파일러
    - 한번 번역하면 실행파일이 생성되어 메모리를 사용하지만, 다음에 실행할 때는 이 파일만 실행하면 되기 때문에 실행시간이 빠르다
  - 인터프리터
    - 직접 실행하기 때문에 실행파일을 생성하지 않아 메모리를 사용하지 않는다


### references
- https://cozy-dandelion.tistory.com/17
- https://st-lab.tistory.com/176
- http://www.ktword.co.kr/word/abbr_view.php?m_temp1=4852
- https://m.blog.naver.com/richardsky9/221013124624
- https://velog.io/@uhs2000/Python-인터프리터
- https://jins-dev.tistory.com/entry/Compiler-와-Interpreter-의-개념과-차이점
- https://soldonii.tistory.com/52
- https://greendreamtrre.tistory.com/65 WIP:
- https://shrtorznzl.tistory.com/82 WIP:

WIP:
- https://www.notion.so/e8149742c1474aa3884bc7a0fe026a4b

- https://www.notion.so/Compile-d2a9fc73cc3f45c9b0cc19cc564c0ce8
- https://www.notion.so/Interpret-0c47836cf40543acad1749c75ddcb726