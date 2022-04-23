# How to Define Functions 함수를 정의하는 방법들

## 종류
- Function Declaration 함수 선언문
- Function Expression 함수 표현식
- Arrow Function 화살표 함수
- Function Constructor 함수 생성자

## Function Declaration 함수 선언문
```js
function test(x){
	return x+1
}
```
- 함수 선언문이 평가되면, 함수이름으로 식별자가 암묵적으로 생성되고, 객체가 할당되기 된다
  - 함수 이름과 함수 식별자는 다르다
- 선언, 초기화, 할당이 동시에 된다
  - 호이스팅 된다
- 함수 선언문은 코드 블럭 자체가 실행 가능 코드(표현식)가 아니다
  - 해당 코드 블럭을 콘솔에서 **실행하여도 어떠한 결과가 리턴되지 않는다**
  - 예
  ```js
  function add (x,y) {
    return x+y;
  }
  // 위를 실행하면 완료 값 undefined 가 출력된다
  ```
- 끝에 `;` 세미콜론 붙일 필요 없다


## Function Expression 함수 표현식
```js
var test = function(){
	return x+1
};
```
- 자바스크립트의 함수가 일급 객체이기 때문에 가능
- 호이스팅 일어나지 않는다
- 끝에 `;` 세미콜론 붙여야 한다

## Arrow Function 화살표 함수
```js
var test = x => x+1;
```
- prototype 프로퍼티가 없다
- 화살표 함수 안에서 yield 키워드를 사용할 수 없다
  - 따라서 화살표 함수는 제너레이터로 사용할 수 없다

## Function Constructor 함수 생성자
```js
var test = new Function("x", "return x+1");
```
- 일반적이지 않으며 바람직하지도 않다