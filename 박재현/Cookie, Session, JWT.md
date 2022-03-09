# (WIP) Cookie, Session, JWT

## Cookie
- 클라이언트 로컬에 저장되는 키와 값이 들어있는 작은 데이터 파일
  - 이름, 값, 만료 날짜/시간(쿠키 저장기간), 경로 정보 등
- 서버에서 HTTP Response Header에 Set-Cookie 속성을 이용하여 클라이언트에 쿠키를 제공
  - 서버측에서 만료 날짜/시간을 지정하여 정해진 시간동안 데이터(상태정보)를 유지할 수 있다. 
- 이후 서버에 요청(Request)할 때 헤더에 담겨저 보내진다.

### 이용 예
- 자동로그인 설정
- 팝업에서 "오늘 더 이상 이 창을 보지 않음" 설정
- 쇼핑몰 장바구니 등

### Cookie 의 종류
- Session Cookie 세션 쿠키
  - 만료 날짜/시간을 지정한 쿠키
  - 브라우저 메모리에 저장되어서 **브라우저가 종료되면 사라진다**
  - 비교적 보안에 유리
- Persistent Cookie 지속 쿠키
  - 만료 날짜/시간이 지정되지 않은 쿠키
  - 파일로 저장되어서 브라우저가 종료돼도 남아있다
  - 비교적 보안에 취약

### Cookie 이용 과정
1. 브라우저(클라이언트)에서 웹페이지에 접속 시도 (서버에 요청)
2. HTTP 헤더를 통해 해당 서버에서 쿠키 값을 응답으로 보낸다
  - 클라이언트는 해당 쿠키를 저장한다
3. 클라이언트가 해당 서버에 재요청할 때, 쿠키 정보도 같이 HTTP 헤더에 담아서 전달한다
4. 서버는 클라이언트의 요청에서 쿠키값을 참고하여 비즈니스 로직을 수행한다
  - 예: 로그인 상태 유지

## Session
- 일정 시간동안 같은 사용자(브라우저)로 부터 들어오는 일련의 요구를 하나의 상태로 보고 그 상태를 일정하게 유지시키는 기술
  - 또는 사용자가 브라우저를 닫아 서버와의 연결을 끝내는 시점
- session id 세션 아이디
  - 클라이언트를 구분할 수 있는 고유 id
  - 웹 서버에 클라이언트에 대한 정보를 저장한 후 session id 로 접근한다

### 이용 예
- authentication 인증
  - 새로고침, 페이지 이동해도 로그인이 풀리지 않고 유지

### Session 이용 과정
1. 브라우저(클라이언트)에서 웹페이지에 접속 시도 (서버에 요청)
2. 서버에서 세션 저장소에 클라이언트에 대한 데이터 저장하고 세션 아이디를 발급해준다
3. 클라이언트는 발급받은 세션 아이디를 쿠키로 저장한다
4. 클라이언트가 해당 서버에 재요청할 때, 세션 아이디를 HTTP 헤더에 담아서 서버에 전달한다
5. 서버는 전달받은 세션 ID로 세션에 있는 클라이언트 정보에 접근한다
6. 클라이언트 정보를 가지고 서버 요청을 처리해서 클라이언트에게 응답한다


## JWT (Json Web Token)
- Json 포맷을 이용해서 가볍고 자가수용적인 (self-contained) 방식으로 정보를 안전하게 전달하기 위한 토큰

### JWT 구조
- 구조
  - Header: 사용된 해쉬 알고리즘, 토큰 타입
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
  - Payload: 내용, 담을 데이터
    ```json
    {
      "sub": "12856324236",
      "name": "wiz",
      "admin": true
    }
    ```
  - Signature: 증명 verification (id, passowrd ?????)
    ```
    HMACSHA256(
      base64UrlEncode(header) + "." +
      base64UrlEncode(payload),
      secret
    )
    ```

### JWT 이용 과정
- 


## 비교
### 서버 부하
- Cookie
  
- Session
  - -) 서버에 처리를 요구하는 부하와 저장 공간을 필요로 한다
- JWT 

### 보안
- Cookie
- Session
  - +) 서버에 정보를 저장하기 때문에 보안성이 쿠키보다 우수
- JWT

### 기타
- Cookie
  
- Session
  - (WIP) +) 세션 아이디로 클라이언트를 구분할 수 있다

- JWT


### References
- https://jeong-pro.tistory.com/80
- https://tansfil.tistory.com/58?category=475681
- https://stupidsecurity.tistory.com/7

- https://jwt.io/introduction
- https://velog.io/@geunwoobaek/JWT란


- https://covenant.tistory.com/201
  - JWT 인증 과정 굿굿
- https://inpa.tistory.com/entry/WEB-📚-JWTjson-web-token-란-💯-정리
  - JWT 인증 과정 굿굿, 이용 과정
- https://velog.io/@geunwoobaek/JWT란
  - JWT 간단 명료

- https://cheershennah.tistory.com/135
  - JWT 인증 과정

- https://88240.tistory.com/190
  - 기타

- https://tecoble.techcourse.co.kr/post/2021-05-22-cookie-session-jwt/
  - 3가지 모두 정리


- https://jeong-pro.tistory.com/80
  - 쿠키 vs 세션