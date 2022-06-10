브라우저와 서버가 통신하는 방법은 어떤 것이 있을까요? 먼저 익숙한 주제에서 시작해보겠습니다. 가장 먼저 생각나는 건 우리 모두에게 익숙한 Ajax 입니다. REST API를 사용할 때 자주 보았죠.

## **Ajax**

Ajax는 ’Asynchronous JavaScript and XML’을 의미하며 JSON, XML, HTML 이나 텍스트파일을 포함하는 다양한 정보를 서버에 **비동기** 전달 할 수 있는 기술들의 집합 입니다. 

Ajax로 인해서 웹 페이지는 리로딩 없이 없데이트가 될 수 있게 되었습니다. 서버와 통신할 때마다 화면이 껏다 켜지(리렌더링)면 매우 불편했겠죠?

> Ajax는 XMLHttpRequest가 아닙니다. Ajax는 비동기로 서버를 전송할 수 있게 해준 기술들의 집합으로 개념 입니다.  
> 주로 XMLHttpRequest가 Ajax에 이용되기 때문에 이런 오해가 생겼습니다.

### XMLHttpRequest

주로 사용되는 `XMLHttpRequest` 객체를 이용해서 HTTP 통신을 하는 방법은 [MDN](https://developer.mozilla.org/en-US/docs/Web/Guide/AJAX/Getting_Started)에 잘 나와있습니다. 그러므로, 여기서는 생략하겠습니다. 불편하다는 정도만 알고가면 될 것 같습니다.

말씀드린 것 처럼 `XMLHttpRequest`로 통신을 하기에는 귀찮습니다. 이 불편한 통신을 간단하게 할 수 있는 **fetch API**와 **Axios 라이브러리**가 등장하게 되었습니다.

fetch와 axios는 둘다 Promise 기반으로 동작하는 공통점을 가지고 있습니다. 반대로, fetch API는 표준이라는 점과 axios는 그렇지 않다는 점과 사용법에 약간의 차이가 있습니다.

서로 간의 성능에는 큰 차이가 없기 때문에 사용법에 대해서 설명을 드리겠습니다.

## 간단한 예제

각각의 방식으로 POST 통신을 하는 예제입니다.

``` js
// axios
const url = "<http://test.com/post>"
const data = {
  a: 10,
  b: 20,
};
const options = {
  headers: {
    Accept: "application/json",
    "Content-Type": "application/json;charset=UTF-8",
  },
}
axios.post(url, data, options)
.then( ({data}) => {
  console.log(data);
})
```

```js
// fetch
const url = "<http://test.com/post>"
const options = {
  method: "POST",
  headers: {
    Accept: "application/json",
    "Content-Type": "application/json;charset=UTF-8",
  },
  body: JSON.stringify({
    a:10,
    b:20,
  })
}
fetch(url, options)
.then(response => response.json())
.then( data => {
  console.log(data);
})
```

- axios는 전달할 데이터를 파라미터로 넘겨줄 수 있지만 fetch의 경우 body 속성에 추가를 해줘야 합니다.
- fetch의 경우 전달할 데이터를 `JSON.stringify()` 메소드를 사용하여서 JSON string으로 변환 하여야 합니다.
- Axios는 응답을 JSON으로 자동 변경해주는 반면에 fetch는 `Response.json()` 메소드를 이용해서 JSON으로 변경해주어야 합니다.
- Axios는 응답을 `data` 프로퍼티로 전달 받아야 하지만 fetch는 이름을 커스텀 할 수 있습니다.

fetch 사용시 몇줄만 추가하면 axios에서 제공하는 기능을 충분히 재현할 수 있습니다.

## 호환성

Axios가 주장하는 주된 특징은 지원 범위가 더 넓다는 것 입니다. 심지어 오래된 IE11도 Axios를 실행 시킬 수 있다고 합니다. fetch의 경우 Chrome42+, Edge14+ Firefx39+, Opera29+, Safari+10.1, NodeJs18.0.0+ 에서 지원 됩니다.

현재에는 호환성 반면에서는 fetch 또한 호환성에 큰 문제가 없고 최근 fetch가 2022-04-19에 NodeJS도 지원하게 되었기 때문에 호환성에서도 큰 차이는 없습니다.

## Response timeout

개발자들이 Axios를 사용했던 주된 이유는 timeout을 발생 시켜주기 때문입니다.

```js
axios({
  method: 'post',
  url: '/login',
  timeout: 4000,    // 4 seconds timeout
  data: {
    firstName: 'David',
    lastName: 'Pollock'
  }
})
.then(response => {/* handle the response */})
.catch(error => console.error('timeout exceeded'))
```

하지만 fetch 또한 timeout을 구현할 수 있습니다.

```js
const controller = new AbortController();
const options = {
  method: 'POST',
  signal: controller.signal,
  body: JSON.stringify({
    firstName: 'David',
    lastName: 'Pollock'
  })
};  
const promise = fetch('/login', options);
const timeoutId = setTimeout(() => controller.abort(), 4000);

promise
  .then(response => {/* handle the response */})
  .catch(error => console.error('timeout exceeded'));
```

`AbortController` 객체를 사용하여 요청이 원하는 동작을 하지 않을 때 요청에 `AbortSignal`을 전달 할 수 있습니다.

## HTTP interceptors

axios의 주요 특징은 HTTP 요청을 훔칠 수 있는 것입니다. 이 특징을 이용하면 모든 HTTP 요청에 포함되어야할 로직을 각각 만드는 것이 아닌 하나만 만들어도 됩니다.

HTTP method를 훔쳐서 로그를 출력하는 예제입니다.

```js
axios.interceptors.request.use(config => {
  // log a message before any HTTP request is sent
  console.log('Request was sent');

  return config;
});

// sent a GET request
axios.get('https://api.github.com/users/sideshowbarker')
  .then(response => {
    console.log(response.data);
  });
```

하지만 fetch 또한 `Proxy`와 `Reflect`를 이용해서 구현할 수 있습니다.

```js
const myFetch = new Proxy(fetch, {
  apply: function(target, thisArg, argumentList) {
    console.log('Request was sent')
    return Reflect.apply(target, thisArg, argumentList);
  }
})

myFetch('https://api.github.com/orgs/axios')
  .then(response => response.json())
  .then(data => {
    console.log(data) 
  });
```

## CSRF(XSRF) 보호

axios의 경우 CSRF Attack을 방지할 수 있는 CSRF Token을 이용한 protection 기법이 내장되어 있습니다.

```js
axios.defaults.xsrfCookieName = 'csrftoken';
axios.defaults.xsrfHeaderName = 'X-CSRF-TOKEN'
```

위의 단 두줄의 추가로 CSRF 공격을 방지할 수 있습니다. 하지만, 당연하게도 fetch 또한 사용할 수 있다.

```js
const csrfToken = getCookie('csrftoken');
const headers = new Headers({
  X-CSRF-TOKEN: csrfToken
})
```

## 마치며

axios에서 제공하는 기능은 대부분 쉽게 fetch로도 구현할 수 있기 때문에 굳이 axios를 사용해야할 이유는 없는 것 같습니다. fetch와 axios의 사용은 취향차이인 것 같습니다. 과거에는 axios가 더 강력할 수 있었으나 현재는 큰 차이가 없어보입니다.

## 참조하면 좋은 글

- [https://en.wikipedia.org/wiki/Ajax_(programming)](https://en.wikipedia.org/wiki/Ajax_(programming))
- [https://developer.mozilla.org/en-US/docs/Web/Guide/AJAX](https://developer.mozilla.org/en-US/docs/Web/Guide/AJAX)
- [https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- [https://axios-http.com/docs/intro](https://axios-http.com/docs/intro)
- [https://blog.logrocket.com/axios-vs-fetch-best-http-requests/](https://blog.logrocket.com/axios-vs-fetch-best-http-requests/)