React는 Virtual DOM을 이용하여 실제 DOM을 제어하는 것보다 성능을 올렸다고 합니다. 

그럼, 왜 Vritual DOM을 제어하는게 DOM을 제어하는 것보다 좋을까요? 또 어떻게 제어할까요?

## Virtual DOM?
virtual DOM(VDOM)은 실제 'DOM' 또는 렌더링 환경(iOS, Android 같은)과 동기화 할 수 있는 'virtual'한 UI 표현 방식을 뜻하는 프로그래밍 개념 입니다.

### 왜 VDOM?
DOM은 HTML같은 document를 표현하기 위해서 트리구조를 이용합니다. 그러나 script에 의해서 DOM이 속성이 변경이 되어야 한다면 전체 app를 리렌더링 해야하는 단점이 있습니다. VDOM은 전체 app를 리렌더링 하는 대신 최소한의 DOM만 조작할 수 있게 해줍니다. 여기서 실제 DOM과 VDOM을 비교하는 것을 `reconcilation` 이라고 부릅니다

![](https://images.velog.io/images/whow1101/post/2e3c9ecd-6882-48d2-b7d6-c7bffd6648fe/vdom1.png)

React는 declartive API를 제공해주어 개발자는 react 내부에서 어떤 일이 일어나고 있는지 모르며 알 필요가 없습니다. 이렇게 해주는 것은 reconciliation 때문이며 개발자가 더 쉽게 개발을 할 수 있게 도와 줍니다.

## Reconcilation?
React의 `render()` 함수는 state나 props가 갱신되면 새로운 React Element Tree를 반환 합니다. 이 때 React는 이전 트리를 방금 만들어진 트리에 맞게 가장 효과적으로 UI를 갱신하는 방법을 알아 내야 합니다

하나의 트리를 다른 트리로 변환하기 위한 최소한의 연산 수를 구하는 알고리즘의 일반적인 복잡도는 O(n^3)의 복잡도를 가집니다.

React에서 이 알고리즘을 적용하면, 1000개의 Element가 있다면 10억번의 비교 연산을 해야 합니다. 너무나도 비싼 연산 이죠.. React는 대신 두가지 가정을 기반하여 O(n) 복잡도를 가진 알고리즘을 구현했습니다.

1. 서로 다른 타입의 두 엘리먼트는 서로 다른 트리를 만들어낸다.
2. 개발자가 `key` prop를 통해, 여러 렌더링 사이에서 어떤 자식 엘리먼트가 변경되지 않아야 할지 표시해 줄 수 있다.

### 엘리먼트의 타입이 다른 경우
두 root 엘리먼트의 타입이 다르면 React는 이전 트리를 버리고 완전히 새로운 트리를 구축합니다.

```html
<div>
  <Counter />
</div>

<span>
  <Counter />
</span>
```

이전 `Couter`는 사라지고, 새로운 `Counter`가 다시 마운트 될 것입니다.

### DOM 엘리먼트의 타입이 같은 경우
같은 타입의 두 React DOM 엘리먼트를 비교할 때, React는 두 엘리먼트의 속성을 확인하여, 동일한 내역은 유지하고 변경된 속성들만 갱신합니다.

```html
<div className="before" title="stuff" />

<div className="after" title="stuff" />
```

위 두 엘리먼트를 비교하면, React는 현재 DOM 노드 상에 `className`만 수정합니다.

### 같은 타입의 컴포넌트 엘리먼트
컴포넌트가 갱신되면 인스턴스는 동일하게 유지되어 렌더링 간 state가 유지됩니다. 

### 자식에 대한 재귀적 처리
DOM 노드의 자식들을 재귀적으로 처리할떄, React는 기본적으로 동시에 두 리스트를 순회하고 차이점이 있으면 변경을 생성합니다.

```html
<ul>
  <li>first</li>
  <li>second</li>
</ul>

<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```

React는 두 트리에서 `<li>first</li>`가 일치하는 것을 확인하고 `<li>second</li>`가 일치하는 것을 확인합니다. 그리고 마지막으로 `<li>third</li>`를 트리에 추가합니다.

하지만 위와 다르게 리스트의 맨 앞에 엘리멘트를 추가하는 경우 성능이 좋지 않습니다.

```html
<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>

<ul>
  <li>Connecticut</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
```
React는 종속 트리를 그대로 유지하는 대신 모든 자식을 변경합니다. 이러한 비효율은 문제가 될 수 있습니다.

### Keys
이러한 문제를 해결하기 위해, React는 `key`속성을 지원한니다. 자식들이 key를 가지고 있다면, React는 key를 통해 기존 트리와 이후 트리의 자식들이 일치하는지 확인합니다.

```html
<ul>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>

<ul>
  <li key="2014">Connecticut</li>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
```

React는 `'2014'`key를 가진 엘리먼트가 새로 추가되었다는 것을 인지하고 `'2015'`와 `'2016'`key를 가진 엘리먼트는 그저 이동만 하면 되는 것을 알 수 있습니다.




**reconcilation**
서로 다른 트리에서 어떤 부분이 바뀌어야 하는지 비교하는 React가 사용하는 알고리즘 입니다.

**update**

React API의 중요 아이디어는 전체 app을 re-render해야 하는지 아닌지 판단하는 것 입니다. 이것은 개발가 app이 어떻게 바뀌는지 알 수 없게 추상화 하는 것을 가능하게 합니다.

실제 앱에서 전체 app을 re-rendering하는 것은 성능 면에서 비용이 엄청나게 많이 듭니다. 리액트는 뛰어난 성능을 유지하면서 전체 앱을 리렌더링하는 것처럼 보이는 최적화 기능을 가지고 있습니다. 이런한 최적화의 대부분은 reconcilation이라고 불리는 과정의 일부 입니다.

reconcilation은 일반적으로 virtual DOM 뒤에 잇는 알고리즘 입니다. React application을 render할 때 app을 describe하는 node는 생성되고 메모리에 저장됩니다. 이 트리는 렌더링 환경으로 플러쉬 됩ㄴ니다. 예를들면, 브라우저 애플리케이션의 경우, 이것은 DOM 집합으로 번역됩니다. 앱이 변경될 때(보통 `setState`에 따라), 새로운 트리가 생성 됩니다. 새로운 트리는 이전 트리와 비교하여 어떤 operation이 app을 render하는데 필요한지 계산합니다.

### Reconcilation versus rendering
DOM는 React가 render할 수 있는 rendering 환경 중에 하나 입니다. 다른 환경으로는 native iOS나 Android가 있습니다.

다양한 타겟에 지원을 할 수 있는 이유는 React는 reconcilation과 rendering을 다른 phaser를 분리했기 때문입니다. reconciler는 tree의 어떤 부분이 변경 되었는지 계산하고 renders는 이 정보를 이용해서 app을 rendering 합니다. 

이 분리는 React DOM과 React Native가 같은 reconciler를 사용하며 자기 자신의 renders를 사용할 수 있는 것을 의미합니다.


## 진짜 DOM은 매번 모든 App를 리렌더링 할까?
아니요 그렇지 않습니다. Web은 `shadow DOM`을 사용하여 DOM 트리에 속한 엘리먼트에 부착될 수 있게 해줍니다.
![](https://images.velog.io/images/whow1101/post/03a4c207-0834-47c2-91d6-82f9008cfe77/shadowdom.svg)


## 관련자료
- https://reactjs.org/docs/faq-internals.html
- https://en.wikipedia.org/wiki/Web_Components
- https://reactjs.org/docs/reconciliation.html
- https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model
- https://velog.io/@syoung125/eact-Reconciliation%EC%9D%B4%EB%9E%80-virtual-DOM-%EB%A6%AC%EC%95%A1%ED%8A%B8%EA%B0%80-%EC%84%A0%EC%96%B8%EC%A0%81
- https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_shadow_DOM
