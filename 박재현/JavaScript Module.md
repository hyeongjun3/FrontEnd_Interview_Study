# JavaScript Module System 자바스크립트 모듈 시스템

## 개념
### Module System 모듈 시스템
플러그인 파일이나 잘게 쪼개진 자바스크립트 코드 조각을 재사용하기 위해서 각각의 파일을 등록하고, 등록된 파일을 자바스크립트에서 불러와서 사용할 수 있게 해주는 시스템

### 핵심기능
- '엄격모드' (use strict)로 실행 된다
  - 선언되지 않은 변수에 값을 할당하는 등의 코드는 에러를 발생시킨다
- 모듈 레벨 스코프
  - 모듈별로 각 스코프가 있다 
  - 따라서 기본적으로 모듈 내부에 정의한 변수나 함수는 다른 스크립트에서 접근할 수 없다
- 단 한 번만 평가됨
  - 동일한 모듈이 여러 곳에서 사용되더라도 모듈은 최초 호출 시 단 한 번만 실행된다
  - 실행 후 결과는 이 모듈을 가져가려는 모든 모듈에 export 되어서 적용된다
  - 따라서 어떤 파일에서 특정 모듈의 변수를 변경하면, 이 모듈을 사용하는 다른 파일에서도 변경사항이 적용된다
- import.meta 객체는 현재 모듈에 대한 정보를 제공해줍니다.
- `this`는 undefined
  - 일반 스크립트의 this는 전역 객체인 것과 달리 모듈 최상위 레벨의 this는 undefined입니다.

### 자바스크립트에서 모듈을 사용하지 않았을 때의 단점
- 전역 유효 범위를 오염시키게 된다
  - 전역 유효 범위의 오염
    - 전역 변수와 전역 함수를 전역 객체에 선언하는 행위
- `var`의 경우, 동일한 이름의 변수가 여러 개 있다면, 좀 더 이후에 선언된 것만 이용하게 된다
- 유지 보수하기 힘들다

## 종류
기본적으로 브라우저는 ESM 제외하고는 모듈을 사용할 수 없다

### CJS (CommonJS)
서버사이드에서 사용, 동기적 작동
- `module.export`, `exports.이름`, `require("경로")` 이용
- Node 에서 이용하는 방식
- 브라우저에서 이해할 수 없다
  - CJS 방식으로 모듈 시스템을 사용한 후에 코드를 브라우저에서 실행하기 위해서는 모듈 번들링이 필요하다
    - browserify, webpack, rollup 등 모듈 번들러를 이용 
- ESM인 import/export 를 이용하기 위해선 babel 을 거쳐서 ESM -> CJS로 변환해야 한다
- 동기적으로 작동한다
  - 모듈이 import(require)되기 전까지 아래 부분의 코드들이 실행되지 않는다

### AMD (Asynchronous Module Definition)
클라이언트에서 주로 사용, 비동기적 작동
- 비동기적이다, 브라우저에서 모듈시스템을 사용하기 위해 만들어졌다
  - CommonJS는 모든 파일이 로컬 디스크에 있어, 필요할 때 바로 불러올 수 있는 상황에서만 사용한다
    - 브라우저에서 이런 방식은 필요한 모듈이 모두 다운로드될 때까지 아무것도 할 수 없는 상황이 발생한다
- 네트워크 요청을 통해 모듈을 불러오는 방식
- AMD 를 이용하는 라이브러리
  - RequireJs
  - RINF

### UMD (Universal Module Definition)
AMD와 CommonJS가 서로 호환되지 않는 문제를 해결하기 위한 API
- 모듈 시스템이라기보다는 런타임을 확인하고 분기하는 방식

### ESM (ECMAScript Module)
자바스크립트 언어 자체에서 모듈을 탑재
- 하지만 최신 문법인 만큼, 모든 브라우저가 지원하지 않는다
- 파일 확장자로 mjs 를 사용하는 것이 권장된다
- 예
  ```html
  <script type="module" src="./index.mjs"></script>
  ```

## ESM vs CJS
CommonJS에서는 require()는 동기로 이루어진다. 따라서 promise나 콜백 호출을 리턴하지 않는다. 
- require()는 디스크로 부터 읽어서 (네트워크 일수도 있다) 그 즉시 스크립트를 실행한다. 
- 따라서 스스로 I/O나 부수효과 (side effect)를 실행하고 module.exports에 설정되어 있는 값을 리턴한다.

ESM은 모듈 로더를 비동기 환경에서 실행한다. 
- 먼저 가져온 스크립트를 바로 실행하지 않고, import와 export구문을 찾아서 스크립트를 파싱한다. 
  - 파싱 단계에서, 실제로 ESM 로더는 종속성이 있는 코드를 실행하지 않고도도, named imports에 있는 오타를 감지하여 에러를 발생시킬 수 있다
- ESM 모듈 로더는 가져온 스크립트를 비동기로 다운로드 하여 파싱한다음, import된 스크립트를 가져오고, 더 이상 import 할 것이 없어질 때까지 import를 찾은 다음 dependencies의 모듈 그래프를 만들어 낸다. 
  - 그리고, 스크립트는 실행될 준비를 마치게 되며, 그 스크립트에 의존하고 있는 스크립트들도 실행할 준비를 마치게 되고, 마침내 실행된다.
- ESM 모듈 내의 모든 자식 스크립트들은 병렬로 다운로드 되지만, 실행은 순차적으로 진행된다.

## 결론
### NodeJs 에서
CommonJs로 작업하거나,
ESM 문법 작업후, 바벨로 CommonJs로 변환해서 작동시킨다

### 브라우저에서
ESM을 지원하는 브라우저만 지원하고 싶다면 ESM 을 택하면 되지만,
현재로서는 ESM 문법으로 작업 후 browserify, webpack, rollup 등으로 하나의 파일로 번들링해서 이용한다

## references
- https://www.youdad.kr/js-module-system/
- https://helloworldjavascript.net/pages/293-module.html
- https://tangoo91.tistory.com/22
- https://baeharam.netlify.app/posts/javascript/module
- https://ko.javascript.info/modules-intro
- https://yceffort.kr/2020/08/commonjs-esmodules
- https://beomy.github.io/tech/javascript/cjs-amd-umd-esm/
- https://intrepidgeeks.com/tutorial/commonjs-amd-cmd-umd-specification-difference-summary