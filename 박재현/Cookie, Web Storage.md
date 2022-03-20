# Cookie, Web Storage 쿠키와 웹 스토리지

## Cookie
- 클라이언트 로컬에 키와 값의 형태로 데이터를 저장하며 서버와의 통신에 이용되는 정보
  - 이름, 값, 만료 날짜/시간(쿠키 저장기간), 경로 정보 등
- 서버에서 HTTP Response Header에 Set-Cookie 속성을 이용하여 클라이언트에 쿠키를 제공
  - 서버측에서 만료 날짜/시간을 지정하여 정해진 시간동안 데이터(상태정보)를 유지할 수 있다. 
- 이후 서버에 요청(Request)할 때 헤더에 담겨저 보내진다.
- 종류
  - Persistent/Permanent Cookie
  - Session Cookie

### Cookie 이용 예
- 자동로그인 설정
- 팝업에서 "오늘 더 이상 이 창을 보지 않음" 설정
- 쇼핑몰 장바구니 등

### Cookie 이용 과정
1. 브라우저(클라이언트)에서 웹페이지에 접속 시도 (서버에 요청)
2. HTTP 헤더를 통해 해당 서버에서 쿠키 값을 응답으로 보낸다
  - 클라이언트는 해당 쿠키를 저장한다
3. 클라이언트가 해당 서버에 재요청할 때, 쿠키 정보도 같이 HTTP 헤더에 담아서 전달한다
4. 서버는 클라이언트의 요청에서 쿠키값을 참고하여 비즈니스 로직을 수행한다
  - 예: 로그인 상태 유지

## Web Storage (local storage, session storage)
- 클라이언트 로컬에 키와 값의 형태로 데이터를 저장하는 API
- 종류
  - Local Storage
  - Session Storage

### Web Storage 이용 예
- 유저의 복잡한 데이터를 저장 

## 비교
### 접근 범위
- cookie
  - **기본적으로 해당 도메인**, 아무 브라우저, 아무 탭
    - Domain 속성으로 다른 도메인에서 접근 허용 가능
    - Path 속성으로 접근가능한 path 위치도 지정 가능
  - 서버에서 쿠키를 클라이언트에 보낼수 있고, 클라이언트에서 서버에 보낼 수도 있다
- local storage
  - **해당 도메인**, 아무 브라우저, 아무 탭
- session storage
  - 해당 도메인, 해당 브라우저, **해당 탭**

### 지속
- persistent cookie
  - 만료 날짜/시간이 지정되지 않은 쿠키
  - 파일로 저장되어서 브라우저가 종료돼도 남아있다
- session cookie
  - 만료 날짜/시간을 지정한 쿠키
  - 브라우저 메모리에 저장되어서 브라우저가 종료되면 사라진다
- local storage
  - 영구 지속
- session storage
  - 탭이 닫히면 사라진다

### 용량
- cookie
  - 4KB
- session storage
  - 보통 5MB
- local storage
  - 보통 5MB 이상

### ref
- https://javascript.info/localstorage 
- https://erwinousy.medium.com/쿠키-vs-로컬스토리지-차이점은-무엇일까-28b8db2ca7b2
- https://kamang-it.tistory.com/entry/Web조금-더-자세히cookie는-너무-구식아냐-이제부턴-Web-Storage
- https://inpa.tistory.com/entry/JS-📚-localStorage-sessionStorage
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#tracking_and_privacy