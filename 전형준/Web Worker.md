## Web Worker?
웹 워커는 싱글 스레드로 동작하는 JavaScript를 보완하기 위해서 브라우저가 제공하는 기능입니
다. 

아래는 Web Worker와 관련한 간단한 워크플로우입니다.
![](https://velog.velcdn.com/images/whow1101/post/473caaf3-9674-42f3-abb7-8dfac562661f/image.png)

Web Worker를 사용하지 않았을 때에는 UI 동작과 데이터 핸들링이 순차적인 방식으로 처리가 
되는 반면, Web Worker를 사용하면 병렬 처리가 가능합니다. 그러므로 성능 향상을 기대할 수 있습니다.

자바스크립트 엔진 동작 방식인 이벤트 루프에 대해서 자세히 살펴보겠습니다.

![](https://velog.velcdn.com/images/whow1101/post/6e037b6a-3236-436c-8852-a32fcb3bd2d3/image.png)

왼쪽에 태스크 큐, 마이크로 태스크 큐, 애니메이션 프레임이 있습니다.

모든 큐가 각가 맞는 콜백 함수를 전달 받아 각각의 태스크를 스택에 담아서 하나씩 처리합니다. 다시 스택이 비면 이벤트 루프의 우선순위에 따라서 순차적으로 큐에 있는 항목을 스택에 넣어 처리합니다.

웹 API의 일부 함수는 멀티 스레드로 처리가 되는 것도 있지만, 사용자가 직접 데이터를 처리하는 부분은 싱글 스레드에서 동작되므로 데이터 처리가 많아질수록 병목현상이 생겨 렌더링 프레임에 영향을 미치게 됩니다.

이런 병목현상을 개선하기 위해서 Web Worker를 사용할 수 있습니다.

![](https://velog.velcdn.com/images/whow1101/post/e76cb95e-edf6-4326-b3d4-c5c03b263335/image.png)

UI를 처리하는 메인스레드 외, 각각의 데이터를 처리할 수 있는 Web Worker를 필요한 개수만큼 생성하여 스레드처럼 사용할 수가 있습니다.

메인스레드와 Web Worker는 메시지 방식으로 서로 통신하며 데이터를 주고받습니다. Web Worker 자체는 단순해서 메인 스레드에는 Web Worker로 로드할 URL을 넘겨주고 메시지를 주고받는 콜백을 등록하여 사용하면 됩니다. Web Worker로 사용될 스크립트에서도 마찬가지입니다. 메시지 콜백만 등록하여 사용하면 됩니다.

## Web Worker를 사용하지 않는 이유
이처럼 Web Worker가 많은 장점이 있는데 잘 사용되지 않는 이유는 다음과 같습니다.

1. 서버에서 대부분의 데이터를 처리하게 때문에 굳이 Web Worker를 사용하게 될 일이 없습니다(Web Worker를 사용할 만큼 부하가 걸리는 작업을 안 함).
2. DOM 제어 및 Window 객체의 일부 함수가 메인 스레드에서만 가능하고 Web Worker에서는 사용이 불가능한 부분이 있습니다.
3. 단순 계산식에서는 Web Worker를 사용하지 않는 것이 더 빠르기도 하고, 메인 스레드와 Web Worker 간 데이터 전송 시에는 비용이 발생하기 때문입니다.
 
## Web Worker를 사용하는 경우
반면, Web Worker를 사용하는 경우는 다음과 같습니다. 

1. 화면 동작에 영향을 미치는 연산 동작, 즉, 바이너리 파일 핸들링이나 복잡한 계산이 필요한 경우에 유용합니다. 
2. 백그라운드에서 지속적인 작업을 해야 하거나 메인 스레드 영향을 미치지 않고 작업을 하는 경우에 유용하게 사용될 수 있습니다.
3. 멀티 스레드로 개발했을 때 사용자 환경 개선에 도움이 되는 경우로 볼 수 있습니다. 싱글 페이지 애플리케이션으로 개발되는 프로젝트라면, 사용했을 때 도움이 되는 부분이 있을 것으로 생각됩니다.

## Web Worker 사용 예제
Worker는 `Worker()` 생성자를 사용하여 생성된 객체이며 자바스크립 파일을 실행합니다. 또한 Worker는 현재의 `window`와는 다른 글로벌 컨텍스트에서 실행됩니다. 따라서 `Worker`내에서  현재의 글로벌 스코르를 접근하기 위해서 `window`를 사용하면 에러가 리턴됩니다.

Worker Thread에서는 몇가지 제한 하에 어떤 코드라도 실행할 수 있습니다. 예를들어, Worker내에서는 직접 DOM을 조작할 수 없습니다. 그리고 `window` 객체의 빌트인 메서드나 속성을 사용할 수 없는 것들이 있습니다.

Worker와 메인 스레드 사이에서는 `postMessage()`메서드를 사용하여 메시지를 발송하고, `onmessage`이벤트 핸들러에 의해 응답합니다.


아래는 Dedicated Worker의 예제 코드 입니다.

```js
// main.js
const first = document.querySelector('#number1');
const second = document.querySelector('#number2');

const result = document.querySelector('.result');

if (window.Worker) {
  const myWorker = new Worker("worker.js");

  first.onchange = function() {
    myWorker.postMessage([first.value, second.value]);
    console.log('Message posted to worker');
  }

  second.onchange = function() {
    myWorker.postMessage([first.value, second.value]);
    console.log('Message posted to worker');
  }

  myWorker.onmessage = function(e) {
    result.textContent = e.data;
    console.log('Message received from worker');
  }
} else {
  console.log('Your browser doesn\'t support web workers.');
}
```

```js
// worker.js
onmessage = function(e) {
  console.log('Worker: Message received from main script');
  const result = e.data[0] * e.data[1];
  if (isNaN(result)) {
    postMessage('Please write two numbers');
  } else {
    const workerResult = 'Result: ' + result;
    console.log('Worker: Posting message back to main script');
    postMessage(workerResult);
  }
}
```

## 참고자료
- https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API
- https://tech.kakao.com/2021/09/02/web-worker/