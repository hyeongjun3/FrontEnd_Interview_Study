# HTTP Message HTTP 메시지 형식

## HTTP Request HTTP 요청
### 예
```
POST / HTTP/1.1 
Host: localhost:8000
User-Agent: Mozilla/5.0 (Macintosh; ...) Firefox/51.0
Accept: text/html, application/xhtml+xml, ...,*/*;q=0.8
Accept-Language: en-US, enlq=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Content-Type: multipart/form-data; boundary=-12656974
Content-Lengh: 345
(empty line)
-12656974
(more data)
```
### 구조
- start line
- HTTP headers
- empty line
- body

### start line
- 구조
  - HTTP method
  - URL 또는 프로토콜, 포트, 도메인의 절대경로 등
  - 프로토콜 버전
- 예
  - `POST / HTTP 1.1`
  - `GET /background.png HTTP/1.0`
  - `HEAD /test.html?query=alibaba HTTP/1.1`
  - `OPTIONS /anypage.html HTTP/1.0`
  - `GET http://developer.mozilla.org/en-US/docs/Web/HTTP/Messages HTTP/1.1`
  - `CONNECT developer.mozilla.org:80 HTTP/1.1`
  - `OPTIONS * HTTP/1.1`

### HTTP headers
- 구조
  - Request header 요청 헤더
  - General header 공통 헤더
  - Entity header 엔티티 헤더

### body
업데이트를 하기 위해 서버에 데이터를 전송 
- 보통 HTML 폼 데이터를 포함하는 POST 요청
- GET, HEAD, DELETE , OPTIONS처럼 리소스를 가져오는 요청은 보통 본문이 필요가 없다
- 종류
  - single-resource bodies 단일-리소스 본문
    - 헤더 두개(Content-Type와 Content-Length)로 정의
  - multiple-resource bodies 다중-리소스 본문
    - 파트 마다 다른 정보를 지님
    - 보통 HTML 폼과 관련있다

## HTTP Response HTTP 응답
### 예
```
HTTP/1.1 403 Forbidden
Server: Apache
Content-Type: text/html; charset=iso-8859-1
Date: Wed, 10 Aug 2016 09:23:25 GMT
Keep-Alive: timeout=25, max=1000
Connection: Keep-Alive
Age: 3464
Date: Wed, 10 Aug 2016 09:46:25 GMT
X-Cache-Info: caching
Content-Length: 220

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
(more data)
```
### 구조
- start line
- HTTP headers
- empty line
- body

### start-line
- 구조
  - 프로토콜 버전
  - 상태 코드
  - 상태 텍스트
- 예
  - `HTTP/1.1 404 Not Found.`

### HTTP headers
- 구조
  - Request header 요청 헤더
  - General header 공통 헤더
  - Entity header 엔티티 헤더

### body
업데이트를 하기 위해 서버에 데이터를 전송 
- 모든 응답에 본문이 들어가지는 않는다
  - 예: 201, 204 들은 보통 본문이 없다
- 종류
  - 길이가 알려진 single-resource bodies
    - 헤더 두개(Content-Type와 Content-Length)로 정의
  - 길이를 모드를 single-resource bodies
    - Transfer-Encoding가 chunked로 설정되어 있으며, 파일은 청크로 나뉘어 인코딩 되어 있다
  - multiple-resource bodies 다중-리소스 본문
    - 상대적으로 드물다

## references
- https://hanamon.kr/네트워크-http-메세지-message-요청과-응답-구조/
