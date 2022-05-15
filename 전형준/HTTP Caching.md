웹 사이트와 애플리케이션의 이전에 fetch한 자원을 재사용하는 것으로 성능 향상을 기대할 수 있습니다. 자원을 재사용하는 웹 캐시는 latency와 network traffic을 줄여서 웹 사이트를 더 responsive하게 만들어 줍니다.

## Types of caches
HTTP cache에는 shared와 private cache, 2가지 종류의 캐시가 있습니다. 
- shared cache : 여러 명의 유저를 위한 cache 이며 ISP나 회사에서 사용하는 web proxy에 주로 사용 됩니다.
- private cache : 한 명의 유저를 위한 cache 이며 HTTP를 통해 통신하는 모든 document에 사용 됩니다.

![](https://velog.velcdn.com/images/whow1101/post/b81067c7-2598-4907-92d6-2f40cef006b9/image.png)

## Targets of caching operations
HTTP 캐싱은 주로 `GET` methods를 사용하는 것을 장려합니다.

아래는 캐싱이 주로 사용되는 형태 입니다

- HTML 문서, image 나 파일같은 자원을 포함하는 `GET` reqeust에 대한 `200` response
- `301` response (permanent redirect)
- `404` response (Not Found)
- `206` response (Partial Content)
- `GET` method 외에 유저가 적절한 해더를 사용하는 경우

## Controlling caching
### `Cache-Control` 헤더
`Cache-Control` 헤더는 요청과 응답에 대한 캐시 정책을 정의합니다.
`Cache-Control` 헤더에 주로 사용되는 값을 알아보겠습니다. 

### No caching
```
Cache-Control : no-store
```
말 그대로 캐싱하지 않습니다.

### Cache but revalidate
이미 cache 되어있는 응답이 있지만, origin 서버에서 validation을 요청 한 후 응답을 가져 옵니다.
```
Cache-Control : no-cache
```

### Private and public caches
`public`은 shared cache, `private`은 private cache 입니다.
shared cache에는 `Authroization` header 값이 저장되지 않지만 `public` 을 사용하면 캐싱할 수 있습니다.

```
Cache-Control : private
Cache-Control : public
```

### Expiration
`max-age=<seconds>`를 이용하여 cache된 자원을 얼마나 refresh 하지 않아도 되는지 나타냅니다. (단위는 초)
```
Cache-Control: max-age=31536000
```

### Validation
`must-revalidate`를 사용하면 cache는 반드시 자원을 사용하기 전에 verify 해야 합니다. 또한 expired된 자원은 사용할 수 없습니다.

```
Cache-Control: must-revalidate
```

## Freshness
자원이 cache에 저장되면, 이론적으로 cache는 영원히 사용 되어야 합니다. 하지만 cache는 한정된 공간을 가지고 있기 때문에 주기적으로 저장소에서 제거 해야 합니다. 이 프로세스를 `cache eviction`이라고 부릅니다.

또한 몇몇 자원은 서버에서 변하기 때문에 cache는 업데이트 되어야 합니다. HTTP는 client-server 프로토콜 이기 때문에 클라이언트는 서버 자원이 변했는지 알 수가 없습니다. 그래서 expiratoin time을 사용합니다. expiration time 전 자원은 `fresh resource`라고 부르며 expiration time이 지난 후는 resource는 `stale resource`라고 부릅니다.

Eviction algorith은 statle resource를 fresh resource로 바꾼다.

클라이언트가 stale resource를 요청하게 되면 `If-None-Match` header를 통해 이 자원이 여전히 `fresh`한지 확인합니다. 만약 그렇다면 서버는 body없이 `304` header를 응답합니다. 

![](https://velog.velcdn.com/images/whow1101/post/99c39439-8a60-42f3-9490-4d0f6a70b80f/image.png)


### Revved resources

caching 하면 할 수록 웹사이트는 더 빨라지고 성능이 향상 됩니다. 그렇기 때문에 expiration time을 늘리면 늘릴수록 유리 합니다. 하지만 주기적으로 자원이 변경되는 요청에서의 캐시는 상관 없지만 그렇지 않은 요청에 대해서는 어떻게 처리해야할지 어려웁니다.

그래서 웹 개발자는 `revving` 이라고 불리는 기술을 사용하여 비정기적으로 변경되는 자원 요청에 대한 캐싱 방법을 고안했습니다. 자원에 revision 이름을 추가하여 관리를 하는 것입니다. 하지만 이 방법은 해당 자원이 모든 자원과 연결되어야 하는 단점을 가지고 있습니다. 

![](https://velog.velcdn.com/images/whow1101/post/922fb10b-631f-4f57-9ed8-b0740f0470c9/image.png)



## 참고자료
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching
- https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control
- https://stackoverflow.com/questions/626057/is-it-possible-to-cache-post-methods-in-http
- https://stackoverflow.com/questions/47693414/http-caching-in-api-based-application