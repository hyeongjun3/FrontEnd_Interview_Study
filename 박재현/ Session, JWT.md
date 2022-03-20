# Session, JWT 세션과 JWT

## Session 이란
- 일정 시간동안 같은 사용자(브라우저)로 부터 들어오는 일련의 요구를 하나의 상태로 보고 그 상태를 일정하게 유지시키는 기술
  - 또는 사용자가 브라우저를 닫아 서버와의 연결을 끝내기까지의 기간
- session id 세션 아이디
  - 클라이언트를 구분할 수 있는 고유 id
  - 웹 서버에 클라이언트에 대한 정보를 저장한 후 session id 로 접근한다

### Session 이용 예
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
- 토큰 자체에 정보를 담고 있다 (self-contained)
- 상태를 저장하고있지 않는다 (stateless)

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
  - Signature: 증명 verification
    - header 와 payload 를 더한 뒤, 비밀키를 이용해 생성
    - 즉 signature 는 서버측에서 관리하는 비밀키가 유출되지 않는 한 해독할 수 없다
    ```
    HMACSHA256(
      base64UrlEncode(header) + "." +
      base64UrlEncode(payload),
      secret
    )
    ```

### JWT 이용 과정
1. 유저가 id, password 를 직접 입력해서 서버에 요청
2. 서버가 해당 id, password 를 확인하고, secret key를 통해서 액세스 토큰과 리프레시토큰을 발급, 보내준다
- 액세스 토큰이 유효할 때
  1. 클라이언트는 이후 서버에 요청할 때마다 헤더에 액세스 토큰을 항상 헤더에 담아서 같이 보낸다
  2. 서버는 이때 signature를 비밀키로 해독한 다음, 위변조 여부 및 유효 기간 등을 확인한다
  3. 이상이 없다면 요청에 받게 데이터를 보낸다
- 액세스 토큰이 만료되면 
  1. 클라이언트는 리프레시 토큰을 이용해서 새로운 액세스 토큰을 발급 받는다.


## 비교
### 서버 부하
- Session
  - -) 세션 저장소 사용으로 인한 **서버 부하**
- JWT 
  - +) 인증 정보에 대한 별도의 저장소가 필요 없다 
  - -) 토큰의 길이가 길어서 인증요청이 많아지면 **네트워크 부하**가 심해진다

### 보안
- Session
  - +) 개인정보는 세션 아이디에 없고 서버에 저장되어 있기 때문에 안전
  - +) 서버쪽에서 session 통제 가능
  - -) 다만 여전히 세션 아이디가 탈취되면 위험하다
- JWT
  - +) signature 에 쓰이는 서버의 비밀키가 유출되지 않는한 안정하다
  - -) payload 자체는 암호화되지 않기 때문에 유저의 중요한 정보는 담을 수 없다
  - -) 토큰은 한번 발급되면 유효기간 만료까지 계속 사용가능하기 때문에 탈취되면 그동안 대처하기 어렵다
    - 상태를 저장하지 않기 때문에 한번 만들어지면 제어가 불가능하다

### 환경
- Session
  - -) 앱 등 브라우저 외의 환경에서는 쿠키를 활용할 수 없다
- JWT
  - +) JWT 인증은 오늘날 표준 프로토콜 인 OAuth2 로 수행된다
    - 소셜로그인은 모두 JWT 인증이다
  - +) 모바일 앱에서도 잘 작동한다
  - +) 확장성이 우수하다
### 특정 사용자 관리
- Session
  - +) 세션 아이디로 클라이언트를 구분할 수 있다
  - +) 특정 접근을 강제로 중단시킬수 있다
- JWT
  - -) 특정 사용자의 접속을 강제로 만료하기 어렵다

### References
- https://jeong-pro.tistory.com/80
- https://tansfil.tistory.com/58?category=475681
- https://stupidsecurity.tistory.com/7
- https://jwt.io/introduction
- https://velog.io/@geunwoobaek/JWT란
- https://covenant.tistory.com/201
- https://inpa.tistory.com/entry/WEB-📚-JWTjson-web-token-란-💯-정리
- https://velog.io/@geunwoobaek/JWT란
