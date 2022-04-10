React에서는 `setState()` 또는 hook을 사용하여 컴포넌트의 state를 변경합니다. 그리고 state가 변경되면 리렌더링이 발생합니다. 그러면 react는 리렌더링의 비용을 줄이기 위해서 어떤 방법을 사용하고 있을까요?

## state 변경 API는 비동기
먼저, state 변경 API는 비동기로 처리를 합니다. React는 render 비용을 줄이기 위해서 여러개의 API 호출을 단 한번의 호출로 합칩니다. 이렇게 합치는 과정을 `batch`라고 합니다. 즉, `batch` 하기 위해서는 함수는 비동기로 처리가 되어야 합니다. 주의 사항으로는`this.props`와 `this.state`는 비동기로 변경되기 때문에 의존해서는 안됩니다.

예를 들면 아래는 잘못된 코드 입니다.
```js
this.setState({
	counter: this.state.counter +  this.props.increment,
})
```
위의 문제는 아래와 같이 수정할 수 있습니다.
```js
this.setState((state,props) => ({
	counter: state.counter + props.increment
}))
```

## React는 어떤 기준으로 언제 batch?
react는 불필요한 렌더링을 줄이기 위해서 state 변경 함수를 `batch` 한다고 했습니다.

먼저, 아래 예를 참고하여 React의 batch 전략을 보겠습니다.
```js
import React, { Fragment, useState } from "react";
import ReactDOM from "react-dom";
function Component() {
  const [item1, setItem1] = useState("Initial Item 1");
  const [item2, setItem2] = useState("Initial Item 2");
  console.log("render: ", item1, item2);
  function handleClickWithPromise() {
    Promise.resolve().then(() => {
      setItem1("Updated Item 1");
      setItem2("Updated Item 2");
    });
  }
  function handleClickWithoutPromise() {
    setItem1("Updated Item 1");
    setItem2("Updated Item 2");
  }
  return (
    <Fragment>
      <button onClick={handleClickWithPromise}>
        {item1} - {item2} (with promise)
      </button>
      <button onClick={handleClickWithoutPromise}>
        {item1} - {item2} (without promise)
      </button>
    </Fragment>
  );
}
function App() {
  return <Component />;
}
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```

두개의 버튼이 렌더링 되며, 각각 state를 변경하는 API가 포함된 handler가 있습니다. 첫번째  버튼의 handler는 promise로 API가 감싸져 있으며 두번째 버튼은 그렇지 않습니다.

먼저 처음 mount 됐을 때의 결과 입니다. 
![](https://velog.velcdn.com/images/whow1101/post/263b888a-ed33-4850-a761-864519a6e90c/image.png)
초기값을 렌더링하고 있습니다.

다음은 API가 promise로 감싸져 있는 첫번째 버튼을 눌렀을 때의 결과입니다. 
![](https://velog.velcdn.com/images/whow1101/post/70ffe111-2e09-44fc-9ddb-25ebb7ed03a0/image.png)

API가 batch 되지 않고 각각 호출되어 불필요한 렌더링이 발생한 결과를 볼 수 있습니다.

반대로 두번째 버튼을 눌럿을 때의 모습입니다.
![](https://velog.velcdn.com/images/whow1101/post/7696ecff-f072-4529-bfd2-dc685facdcee/image.png)
API가 batch되어 한번의 렌더링만 발생한 결과입니다.

즉,React는 promise, setTimeout, native event handelr 등 다른 이벤트에 대해서는 batch가 되지 않습니다.

그럼 이런 오버헤드를 해결하는 방법은 무엇일까요? React에서 제공하는 함수 `unstable_batchedUpdates()` 를 사용하여 문제를 해결할 수 있습니다.

```js
function handleClickWithPromise() {
    Promise.resolve().then(() => {
      ReactDOM.unstable_batchedUpdates(() => {
        setItem1("Updated Item 1");
        setItem2("Updated Item 2");
      });
    });
  }
```

하지만 [React 문서](https://reactjs.org/docs/faq-versioning.html#what-counts-as-a-breaking-change)에서는 unstable_ 로 시작하는 API는 실험적인 기능이라고하며 나중에 개선한다고 합니다.

React 18에서는 위와 같은 batch 문제를 해결하였습니다.

## React 18 - Automatic batching
`createRoot`로 시작하는 React 18 에서는 fetch로 인한 batch가 되지 않는 위와 같은 문제를 해결하였습니다.

```js
function App() {
  const [count, setCount] = useState(0);
  const [flag, setFlag] = useState(false);

  function handleClick() {
    fetchSomething().then(() => {
      // React 18 and later DOES batch these:
      setCount(c => c + 1);
      setFlag(f => !f);
      // React will only re-render once at the end (that's batching!)
    });
  }

  return (
    <div>
      <button onClick={handleClick}>Next</button>
      <h1 style={{ color: flag ? "blue" : "black" }}>{count}</h1>
    </div>
  );
}
```

만약, batch를 원하지 않는다면 `flushSync()`를 사용할 수 있습니다.
```js
import { flushSync } from 'react-dom'; // Note: react-dom, not react

function handleClick() {
  flushSync(() => {
    setCounter(c => c + 1);
  });
  // React has updated the DOM by now
  flushSync(() => {
    setFlag(f => !f);
  });
  // React has updated the DOM by now
}
```

## 참고자료
- https://reactjs.org/docs/state-and-lifecycle.html
- https://reactjs.org/docs/react-component.html
- https://blog.logrocket.com/simplifying-state-management-in-react-apps-with-batched-updates/
- https://github.com/reactwg/react-18/discussions/21