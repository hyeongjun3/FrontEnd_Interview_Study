# [번역] Core Web Vitals

프론트엔드의 성능을 측정하기 위해서는 무엇을 어떻게 측정해야할까요? 

이번 글에서는 현대에는 어떤 지표로 프론트엔드 성능을 측정하며, 어떤 방식으로 측정했는지에 대해서 알아보겠습니다.

## 프론트 엔드에서의 성능은 무엇일까요?

프론트 엔드에서 성능은 다양합니다. 하지만 그 중 가장 중요하다고 생각되는 것은 **유저에게 웹 사이트를 빠르게 표시**하는 것 입니다. 좀 더 기술적으로 풀어보면 빠른 로딩과 렌더링으로 유저에게 좋은 경험을 주는 것 입니다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/63b6aae7-b216-4ae9-8ae6-8b2327d6596b/Untitled.png)

프론트 엔드 성능은 로딩과 렌더링 시간을 줄이는 것으로 볼 수 있습니다. 

들어가기 앞서 브라우저에 웹사이트가 표시되는 일련의 과정, 즉 렌더링 과정에  대해서 알아보겠습니다. 

### 브라우저 렌더링 과정

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a220ba50-bdae-4df0-affb-5341de52f9a1/Untitled.png)

브라우저 렌더링 과정은 아래와 같습니다.  

1. 브라우저는 HTML, CSS, 자바스크립트, 이미지, 폰트 파일 등 렌더링에 필요한 리소스를 요청하고 서버로부터 응답을 받는다.
2. 브라우저의 렌더링 엔진은 서버로부터 응답된 HTML과 CSS를 파싱하여 DOM과 CSSOM을 생성하고 이들을 결합하여 렌더 트리를 생성한다.
3. 브라우저의 자바스크립트 엔진은 서버로부터 응답된 자바스크립트를 파싱하고 실행한다. 이때 자바스크립트는 DOM API를 통해 DOM이나 CSSOM을 변경할 수 있다. 변경된 DOM과 CSSOM은 다시 렌더 트리로 결합된다.
4. 렌더 트리를 기반으로 HTML 요소의 레이아웃(위치와 크기)을 계산하고 브라우저 화면에 HTML 요소를 페인팅 한다.

이처럼 브라우저가 HTML, CSS, JavaScript와 그 외 리소스를 스크린에 표시하는 일련의 과정을 **Critical Rendering Path**라고 부르기도 합니다.

Critical Rendering Path를 최적화한다면 로딩과 렌더링 시간이 줄여서 프론트 엔드 성능이 향상 될 것 입니다. 그런데 최적화된 시간이 얼마나 유저에게 좋은 경험을 줄 수 있을까요? 

[https://youtu.be/G1IWq2blu8c?t=1006](https://youtu.be/G1IWq2blu8c?t=1006)

기대했던 컨텐츠를 보는데 걸리는 시간은 큰 차이가 있지 않기 때문에 유저한테는 큰 차이가 없는 것을 볼  수 있습니다.  

옛날에 사용된 `load` 나 `DOMContentLoaded` 는 스크린에 컨텐츠를 표시하는 것과는 관련이 크게 없습니다. 현재는 프론트엔드 성능 측정에 새로운 지표가 필요합니다.

## 그래서 어떤 지표?

프론트엔드 성능 측정에 대한 새로운 지표는 Web Vitals로 소개가 됩니다.  Web Vitals은 

- Time to First Byte(TTFB)
- First Contentful Paint(FCP)
- Total Blocking Tim(TBT)
- Time to Interactive(TTI)
- …. 등등

많은 지표를 가지고 있습니다. 이 많은 지표 중 W3C Web Performance Working Group과 구글은 중요하다고 생각되는 3가지 지표를 소개하였습니다. 그리고 이 지표들을 **Core Web Vitals**라고 부릅니다

## Core Web Vitals

Core Web Vitals은

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c68ad3fd-9d1b-40b3-ae30-7af4aeb89c9c/Untitled.png)

- Largest Contentful Paint (LCP) : 가장 사이즈가 큰 컨텐츠가 paint 되는 시간입니다. 2.5초 이내라면 유저에게 좋은 경험을 줄 수 있습니다.
- First Input Delay (FID) : 유저의 상호작용에 얼마나 빠르게 반응하는지에 대한 시간입니다. 100밀리초 이내라면 유저에게 좋은 경험을 줄 수 있습니다.
- Cumulative Layout Shift (CLS) : 유저에게 보이는 layout이 얼마나 움지이는지에 대한 지표 입니다. CLS 값이 0.1 이내라면 유저에게 좋은 경험을 줄 수 있습니다.

Core Web Vital의 3가지 지표가 어떤 것이며, 어떻게 측정되는지에 대해서 알아보겠습니다. 

## LCP

Largest Contentful Paint (LCP)는 말그대로 가장 사이즈가 큰 컨텐츠가 paint 될 때 까지의 시간입니다. 2.5초 이내라면 유저에게 좋은 경험을 줄 수 있습니다.

그러면 LCP는 어떤 element를 어떻게  측정을 할까요?

### 가장 큰 컨텐츠가 될 수 있는 Element는 어떤 것이 있을까?

가장 큰 컨텐츠가 될 수 있는 후보군은 아래와 같습니다. 

- `<img>` elements
- `<svg>` 안에 있는 `<image>` element
- `<video>` element (poster 이미지를 사용한다면)
- `url()` 을 사용하는 background-image
- Block-level을 가진 텍스트 element

### Element의 크기는 어떻게 결정?

LCP에 보고되는 element의 크기는 viewport에 눈에 보이는 element의 크기를 선택합니다. 만약 element가 viewport 밖에 있거나, 보이지 않으면 측정되지 않습니다.

이미지의 경우에는 intrinsic 크기와 실제로 보여지는  크기 중 작은 값으로 선택됩니다. 만약 image가 intrinsic 사이즈보다 작게 표시가 된다면 표시된 값이 선택이 되고 그 반대의 경우에는 intrinsic 사이즈가 선택이 됩니다.

텍스트의 경우에는 텍스트 노드의 크기만 고려됩니다.

모든 element의 CSS 요소(margin, padding, border)는 크기에 포함되지 않습니다. 

### 언제 LCP가 보고되는가?

웹 페이지는 단계적으로 로드되는 경우가 많으며, 그 단계마다 페이지의 최대 크기 element가 변경 될 수 있습니다.

즉, 페이지가 로드될 대마다 LCP에  보고되어 최대 크기 element가 변경됩니다.

### 좋은 LCP와 나쁜 LCP

아래는 나쁜 LCP를 가진 예시입니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3806986e-60cb-4b23-9bce-67be1f2476a1/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bc196b78-26b1-44ad-a8a1-54326bc6ea96/Untitled.png)

두 타임라인 모두 컨텐츠가 로드될 때 가장 큰 요소가 변경됩니다. 첫 번째 예의 경우에는 타이틀이 가장 큰 요소로 존재했다가 마지막에는 이미지가 가장 큰 요소가 됩니다.

두 번째 예의 경우에는 레이아웃이 변경되고 컨텐츠도 변경되고 있습니다. 마지막에는 이미지가 가장 큰 요소가 됩니다.

두가지 예시 모두 좋지 못한 LCP를 가지고 있습니다. 반대로 좋은 LCP를 가진 예시를 보겠습니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/72c1effd-f422-459a-8066-d65b031446e8/Untitled.png)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/afb731b3-497b-4711-9af7-eeda0e9fa996/Untitled.png)

첫 번째 예에서는 Instagram 로고, 두 번째 예에서는 텍스트가 가장 큰 element 입니다. 둘 다 좋은 LCP를 가지고 있습니다. 

## First Input Delay(FID)

First Input Delay도 말그대로 유저가 페이지와 처음 상호 작용할 때(예: 링크를 클릭하거나 버튼을 탭하거나 JavaScript 기반 컨트롤을 사용할 때), 그에 대한 응답으로 브라우저가 핸들러 처리하기  시작하기 가지의 시간입니다. 100밀리초 이내라면 유저에게 좋은 경험을 줄 수 있습니다.

이벤트 핸들러를 작성하는 개발자는 이벤트가 발생하는 즉시 코드가 실행될 것이라고 가정합니다. 그러나 유저는 반대의 경험을 하곤 합니다. 이러한 문제가 발생하는 이유는 브라우저의 메인 스레드가 다른 작업을 하고 있어 사용자의 요청에 응답할 수 없기 때문입니다.

아래 리소스(CSS나 JS 파일)을 요청하는 네트워크 요청들과 요청이 끝나면 메인 스레드가 동작하는 타임라인이 있습니다. 

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3449e320-50d6-431f-b07a-c8ac4874486d/Untitled.png)

여기서 메인 스레드는 네트워크 요청이 끝나면 처리해야될 작업을 진행 중이며 베이지색으로 표시되어있습니다. 즉, 베이지 색 구간에서는 유저의 상호 작용을 처리할 수 없습니다. 실제로 가장 큰 FID는 FCP와 TTI 사이에 발생합니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a94b82ec-4b41-4a92-ad17-bfb1068e7ed3/Untitled.png)

## Cumulative Layout Shift (CLS)

실제로 웹서핑을 하다보면 클릭할려고 했던 버튼이 이상한 곳으로 움직여서 다른 버튼을 눌렀던 경험이 있을 것 입니다.

[layout-instability2.webm](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7c5dcffc-ec6e-4e1c-bee3-585b36c43847/layout-instability2.webm)

layout shift가 일어나 발생한 문제로, CLS는 예기치 않은 layout shift가 얼마나 발생하는지에 대해서 측정하며 소개드릴 측정 방법으로 0.1 이내라면 유저에게 좋은 경험을 줍니다.

### Layout shift 점수

점수 측정을 위해서 브라우저는 viewport 크기와 프레임 사이에 불안정한 element의 움직임으로 결정 됩니다. 해당 움직임에 대해서 impact fraction과 distance fraction이라는 두가지 측정값을 사용합니다.

```jsx
layout shift score = impact fraction * distance franction
```

### Impact fraction

impact fraction은 불안정한 element가 두 프레임 사이에 viewport에 영향을 미치는 정도를 측정합니다. 두 프레임에 있는 element의 영향력의 합집합입니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7d549133-6604-41cf-8dfd-3ac40e97a8dd/Untitled.png)

위의 예제의 경우 viewport의 50%을 차지하는 element가 있습니다. 당므 프레임에서 element가 아래로 viewport의 25%만큼 움직였습니다. 이  경우에 전체 viewport에 75% 영향을 주었기 때문에 impact fraction은 `0.75` 가 됩니다.

### Distance fraction

distance fraction은 불안정 element가 이동한 거리를 측정합니다.

위의 예제의 경우에는 viewport의 25%만큼 움직였기 때문에 `0.25` 가 됩니다.

따라서 이 예의 경우에는 CLS는 `0.75 * 0.25 = 0.1875` 가 됩니다.

또 다른 예를 보겠습니다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4278d29a-0893-4c21-8070-c0312b1c4eb2/Untitled.png)

회색 텍스트는 움직이지 않았기 때문에 불안정 element가 아닙니다.

click me 버튼의 경우 이전에 DOM에 없었기 때문에 불안정 element가 압니다.

녹색 텍스트의 경우 두 프레임에서 영향일 끼친 영역의 합집합이 50%이기 때문에 Impact fraction은 `0.5` 입니다.  이동한 거리는 약 14% 만큼으로 Distance fraction은 `0.14` 입니다.

CLS는 `0.5 * 0.14 = 0.07` 입니다.

## TODO

각 지표를 측정하는 방법 + 최적화 하는 방법

## 참고자료

- [https://www.youtube.com/watch?v=G1IWq2blu8c](https://www.youtube.com/watch?v=G1IWq2blu8c)
- [https://developer.mozilla.org/en-US/docs/Web/Performance/Critical_rendering_path](https://developer.mozilla.org/en-US/docs/Web/Performance/Critical_rendering_path)
- [https://web.dev/vitals/](https://web.dev/vitals/)
- [https://web.dev/lcp/](https://web.dev/lcp/)