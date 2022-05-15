# HTTP Headers HTTP 헤더

## General header 공통 헤더
요청과 응답에 모두 사용되는 헤더
### Date
HTTP 메시지가 만들어진 시각
- 요청과 응답시에 자동으로 만들어진다
- 예
  - `Date: Thu, 12 Jul 2018 03:12:27 GMT`

### Connection
현재의 전송이 완료된 후, 네트워크 접속(TCP Connection)을 유지할지 말지를 제어
- 종류
  - `Connection: keep-alive`
    - TCP connection 유지
  - `Connection: close`
    - TCP connection 종료
### Cache-Control
캐싱을 허용할지 않을지 제어
- 응답헤더 Cache-Control을 이용하여 페이지가 캐시에 저장되지 않도록 하는 기법이 흔히 쓰임
  - 항상 최신의 페이지를 받도록 하기 위해
- 예
  - `Cache-Control: public, max-age=3600`
  - `Cache-Control: must-revalidate`
  - `Cache-Control: max-age=31536000`

## Entity header 엔티티 헤더
메시지 바디의 컨텐츠에 대한 헤더
- 주로 앞에 `Content-` 가 붙어 있다
- 종종 공통 헤더에 포함해서 취급되기도 한다

### Content-Length
헤더 + 바디의 크기를 나타낸다
- 바이트 단위
- 자동으로 생성된다
- 예: `Content-Length: 25907`
### Content-Type
미디어 타입과 문자열 인코딩을 제어
- 미디어 타입
  - [type]/[subtype]
  - type
    - 예: text, application, audio, font, image, multipart
  - subtype
    - 각 타입별로 무수히 많다
- 예
  - `Content-Type: text/html; charset=utf-8`
  - `Content-Type: multipart/form-data; boundary=something`
### Content-Language
사용자에게 언어를 알려준다
- 사용자가 선호하는 언어에 따라 사용자를 구분할 수 있게 한다
  - 예를 들어서 Content-Language: en-US로 설정된다면, 미국에서 만들어졌지만, 일부분이 영어가 아닐 수도 있다
  - 만약 Content-Language가 지정되지 않으면 모든 사람들에게 공개된 정보라고 간주한다
- Content-Language는 Text 뿐만 아니라 미디어 타입에도 적용된다
- 예
  - `Content-Language: en-US`
  - `Content-Language: de-DE, en-CA`
### Content-Encoding
미디어 타입을 압축되는 방식을 알려준다
- 브라우저가 이를 받아서 알아서 압축 해제해서 사용한다
- 예
  - `Content-Encoding: gzip`
  - `Content-Encoding: compress`

## Request header 요청 헤더
### Host
요청하는 호스트에 대한 호스트명 및 포트번호 (URI)
- 필수 항목 
- 예
  - `Host: www.naver.com`

### User-Agent
클라이언트 소프트웨어(브라우저, OS) 명칭 및 버전 정보
- 예
  - `User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:50.0) Gecko/20100101 Firefox/50.0`

### Accept
클라이언트 자신이 원하는 미디어 타입 및 우선순위를 알린다
- 예
  - `Accept: */*`
    - 모든 미디어 타입이 가능
  - `Accept: image/*`
    - 모든 이미지 타입이 가능
### Accept-Chartset, Accept-Encoding, Accept-Language
- 클라이언트가 원하는 문자열 집합, 문자 인코딩 방식, 언어
### Authorization
인증 토큰(JWT/Bearer 토큰)을 넣어서 전달하는 헤더
- `[토큰의 종류 (Basic, Bearer)] [토큰]`
- 예
  `Authorization: Bearer faxdgshatw4rfdfhdfdghdjhwu4y43qt3rwdSDFHS`
### Origin
요청 대상 주소 (경로는 포함되지 않는다)
- `Origin: <scheme> "://" <hostname> [ ":" <port> ]`
- 주로 POST 메서드로 요청을 할때 설정한다
- 요청을 보낸 주소와 받는 주소가 다르면 CORS 에러가 발생한다
- 예
  - `Origin: https://developer.mozilla.org`
### Referer
클라이언트에서 현재 페이지 이전의 페이지 주소
- 어떤 페이지에서 현재의 페이지로 들어왔는지 파악하기 위해 사용된다
  - 즉 애널리스틱 같은 곳에 사용된다
- 예
  - `Referer: https://www.naver.com/test`


## Response header 응답 헤더
### Access-Control-Allow-Origin
요청을 허용할 클라이언트의 orign을 지정한다
- 예
  - `Access-Control-Allow-Origin: www.naver.com`
  - `Access-Control-Allow-Origin: *`

### Access-Control-Request-Method,  Access-Control-Request-Headers, Access-Control-Allow-Methods, Access-Control-Allow-Headers
preflight 요청으로 특정 메서드, 헤더를 이용할 것을 서버에 알리고, 서버가 그에 대한 요청으로 허용할 메서드, 헤더를 알려준다
- 예
  - 요청
    ```
    OPTIONS /resource/foo
    Access-Control-Request-Method: GET
    Access-Control-Request-Headers: Content-Type, x-requested-with
    Origin: https://foo.bar.org
    ```
  - 응답
    ```
    HTTP/1.1 200 OK
    Content-Length: 0
    Connection: keep-alive
    Access-Control-Allow-Origin: https://foo.bar.org
    Access-Control-Allow-Methods: POST, GET, OPTIONS, DELETE
    Access-Control-Allow-Headers: Content-Type, x-requested-with
    Access-Control-Max-Age: 86400
    ```
### Allow
Access-Control-Allow-Methods랑 비슷하지만, CORS 요청 외에도 적용된다
- 예
  - GET www.naver.com은 되고, POST www.naver.com은 허용하지 않는 경우
    - `405 Method Not Allowed` 에러를 응답하면서 `Allow: GET` 로 허용하는 메서드를 알려준다

### Content-Disposition
응답 본문을 브라우저가 어떻게 표시해야 할지 알려준다
- 예
  - `Content-Disposition: inline`
    - 웹페이지 화면에 표시된다
  - `Content-Disposition: attachment; filename='filename.csv'`
    - 'filename.csv' 라는 파일명으로 다운로드된다

### Location
300번대 응답이나 201 Created 응답일 때 어느 페이지로 이동할지를 알려준다
- 예
  ```
  HTTP/1.1 302 Found
  Location: /
  ```
### Content-Security-Policy
다른 외부 파일들을 불러오는 경우, 차단할 소스와 불러올 소스를 명시한다
- 예
  - `Content-Security-Policy: default-src 'self'`
    - 자신의 도메인의 파일들만 가져올 수 있다
  - `Content-Security-Policy: default-src https:`
    - https를 통해서만 파일을 가져올 수 있다
  - `Content-Security-Policy: default-src 'none'`
    - 외부의 파일을 가져올수 없다

## references
- https://developer.mozilla.org/ko/docs/Web/HTTP/Headers
- https://gmlwjd9405.github.io/2019/01/28/http-header-types.html
- https://wonit.tistory.com/309
- https://www.zerocho.com/category/HTTP/post/5b3ba2d0b3dabd001b53b9db
- https://www.zerocho.com/category/HTTP/post/5b4c4e3efc5052001b4f519b