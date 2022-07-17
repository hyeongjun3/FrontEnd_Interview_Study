# Web Vital - CLS

# Cumulative Layout Shift (CLS)

이번 글에서는 Core Web Vitals 중 하나인 CLS에 대해서 자세하게 알아보겠습니다.

실제로 웹서핑을 하다보면 클릭할려고 했던 버튼이 이상한 곳으로 움직여서 다른 버튼을 눌렀던 경험이 있을 것 입니다.

[layout-instability2.webm](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7c5dcffc-ec6e-4e1c-bee3-585b36c43847/layout-instability2.webm)

layout shift가 일어나서 발생하는 문제로, CLS는 이런 예기치 않은 layout shift가 얼마나 발생하는지를 측정한 값입니다. CLS는 0.1 이내라면 유저에게 좋은 경험을 준다고 할 수 있습니다. 

그럼 CLS는 어떻게 측정을 할 수 있을까요?

### CLS 측정 - Layout shift 점수

점수 측정을 위해서 브라우저는 viewport 크기와 프레임 사이에 불안정한 element의 움직임으로 결정 됩니다. layout shift 점수는 impact fraction과 distance fraction이라는 두가지 측정값을 사용합니다.

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

이처럼 큰 CLS를 가진 웹사이트는 좋지 못합니다. **하지만 모든 layout shift가 나쁜 것은 아닙니다.**

## Expected vs. unexpected layout shifts

모든 layout shift가 나쁜 건 아닙니다. 사실 많은 동적 웹 애플리케이션에서는 element의 첫번째 위치는 자주 바뀝니다. CLS가 크더라도 유저가 예상할 수 있는 layout shift는 크게 나쁘지 않습니다.

### User-initiated layout shifts

layout shift는 유저가 예상하지 못했을때 좋지 못합니다. 반대로 유저의 행동(link 클릭, 버튼 클릭, search box 입력 같은 동작의 입력에서 일어나는 경우는 괜찮습니다. 

예를 들어, 만약 유저 행동이 network 요청을 트리거 한다면 공간을 좀 만들고 loading indicator를 보여주는 것으로 좋지 못한 layout shift를 피할 수 있습니다. 하지만 사용자가 무언가가 로드되고 있다는 사실을 깨닫지 못하거나 리소스가 언제 준비될지 알지 못하는 경우 대기 시간에 뭔가 다른 것을 클릭하게 될 수도 있습니다.

유저의 행동으로 일어나는 layout shift는 나쁘지 않기 때문에 사용자 입력 후 500밀리초 이내에 발생하는 레이아웃 이동에는 `[hadRecentInput](https://wicg.github.io/layout-instability/#dom-layoutshift-hadrecentinput)` 플래그가 설정되어 계산에서 제외됩니다.

### Animations and transitions

제대로 된 애니메이션 및 전환은 사용자를 놀라게 하지 않고서도 페이지의 콘텐츠를 업데이트할 수 있는 좋은 방법입니다. 페이지에서 갑자기 예기치 않게 콘텐츠가 이동하는 경우는 거의 항상 좋지 않은 사용자 경험으로 이어집니다 그러나 한 위치에서 다음 위치로 점진적이고 자연스럽게 이동하는 콘텐츠는 사용자가 상황을 더 잘 이해하고, 사용자에게 상태의 변경을 안내하는 데 도움이 됩니다.

CSS `[transform](https://developer.mozilla.org/docs/Web/CSS/transform)` 속성을 사용하면 레이아웃 이동을 트리거하지 않고 요소에 애니메이션을 적용할 수 있습니다.

- `height`와 `width` 속성 대신 `transform: scale()` 를 사용하는 것이 좋습니다.
- 요소를 이동하려면 `top` , `right` , `bottom` 또는 `left` 속성을 변경하는 것은 피하고, 대신 `transform: translate()`를 사용하세요.

## How to measure CLS

CLS, lab과 field 환경에서 측정하는 툴이 존재 합니다. 

### Field tools

- [Chrome User Experience Report](https://developers.google.com/web/tools/chrome-user-experience-report)
- [PageSpeed Insights](https://pagespeed.web.dev/)
- [Search Console(Core Web Vitals Report)](https://support.google.com/webmasters/answer/9205520)
- `[web-vitals` JavaScript 라이브러리](https://github.com/GoogleChrome/web-vitals)

### Lab tools

- [Chrome DevTools](https://developer.chrome.com/docs/devtools/)
- [Lighthouse](https://developer.chrome.com/docs/lighthouse/overview/)
- [WebPageTest](https://webpagetest.org/)

### Measure CLS in JavaScript

JavaScript의 CLS를 측정하기 위해서는 [Layout Instability API](https://github.com/WICG/layout-instability) 을 사용해야 합니다. 아래 예제는 예기치 않은 layout shift를 보고 세션으로 그룹화하고 변경될 때마다 최대 세션 값을 기록하는 `PerformanceObserver`를 만드는 방법을 보여줍니다.

```jsx
let clsValue = 0;
let clsEntries = [];

let sessionValue = 0;
let sessionEntries = [];

new PerformanceObserver((entryList) => {
  for (const entry of entryList.getEntries()) {
    // Only count layout shifts without recent user input.
    if (!entry.hadRecentInput) {
      const firstSessionEntry = sessionEntries[0];
      const lastSessionEntry = sessionEntries[sessionEntries.length - 1];

      // If the entry occurred less than 1 second after the previous entry and
      // less than 5 seconds after the first entry in the session, include the
      // entry in the current session. Otherwise, start a new session.
      if (sessionValue &&
          entry.startTime - lastSessionEntry.startTime < 1000 &&
          entry.startTime - firstSessionEntry.startTime < 5000) {
        sessionValue += entry.value;
        sessionEntries.push(entry);
      } else {
        sessionValue = entry.value;
        sessionEntries = [entry];
      }

      // If the current session value is larger than the current CLS value,
      // update CLS and the entries contributing to it.
      if (sessionValue > clsValue) {
        clsValue = sessionValue;
        clsEntries = sessionEntries;

        // Log the updated value (and its entries) to the console.
        console.log('CLS:', clsValue, clsEntries)
      }
    }
  }
}).observe({type: 'layout-shift', buffered: true});
```

대부분의 경우, 페이지가 unload되는 시점의 현재 CLS 값은 해당 페이지의 최종 CLS 값이지만 몇가지 중요한 예의 사항이 있습니다.

다음 섹션에서는 API가 보고하는 내용과 메트릭이 계산되는 방법 사이의 차이점이 나열되어 있습니다.

### Differences between the metric and the API

- 만약 페이지가 background에서 로드 되거나 브라우저가 컨텐츠를 paint 하기 전에 백그라운드화 된 경우 CLS 값을 보고하지 않습니다.
- 만약 페이지지가 [back/forward cache](https://web.dev/bfcache/#impact-on-core-web-vitals)에서 복원될 때 사용자는 이를 별개의 페이지 방문으로 경험하므로 해당 CLS 값을 0으로 재설정해야 합니다.
- API는 iframe 내에서 발생하는 이동에 대한 `layout-shift` 를 보고하지 않지만 CLS를 적절하게 측정하려면 이를 고려해야 합니다. sub frame은 aggregation을 위해 API를 사용하여 상위 프레임에 `layout-shift` 항목을 보고할 수 있습니다.

이러한 예외 외에도 CLS는 페이지의 전체 수명을 측정하기 때문에 조금 더 복잡해집니다.

- 사용자는 며칠, 몇 주, 몇 달 등 *매우* 오랫동안 탭을 열어 둘 수 있습니다. 실제로 사용자는 탭을 닫지 않을 수도 있습니다.
- 모바일 운영 체제에서 브라우저는 일반적으로 백그라운드 탭에 대해 페이지 언로드 콜백을 실행하지 않으므로 "최종" 값을 보고하기 어렵습니다.

이러한 경우를 처리하려면 페이지가 언로드될 때뿐 아니라 페이지가 백그라운드일 때마다 CLS를 보고해야 합니다(`[visibilitychange` 이벤트](https://developer.chrome.com/blog/page-lifecycle-api/#event-visibilitychange)는 이 두 시나리오 모두에 적용됨). 그리고 이 데이터를 수신하는 분석 시스템은 백엔드에서 최종 CLS 값을 계산해야 합니다.

개발자는 이러한 모든 경우를 직접 암기하고 고심하지 않아도, `[web-vitals` JavaScript 라이브러리](https://github.com/GoogleChrome/web-vitals)
를 사용해 위에서 언급한 모든 것을 처리하는 CLS를 측정할 수 있습니다.

```jsx
import {getCLS} from 'web-vitals';

// Measure and log CLS in all situations
// where it needs to be reported.
getCLS(console.log);
```

## How to  improve CLS

대부분의 website의 경우 너는 아래 가이드를 따라서 예기치 못한 layout shift를 피해야 한다.

- `image` 와 `video` element 에 항상 크기 속성을 포함하거나 **[CSS aspect ratio boxes](https://css-tricks.com/aspect-ratio-boxes/)** 와 같은 방식으로 필요한 공간을 미리 확보해야 합니다. 이 접근은 브라우저가 image가 로딩되는 동안 브라우저가 문서에 올바른 양의 공간을 항당할 수 있습니다. 너는 또한 [unsized-media feature policy](https://github.com/w3c/webappsec-feature-policy/blob/master/policies/unsized-media.md) 를 사용하여 브라우저에서 이 동작을 강제할 수 있습니다.
- 사용자의 상호 작용에 대한 응답을 제외하고는 절대 기존 콘텐츠에 콘텐츠를 추가하지 마세요.
- layout를 변경하는 것은 propertie늘 바꾸는 것보다는 animation을 사용하세요

## TODO

- [Optimize CLS](https://web.dev/optimize-cls/)
- [Debug layout shifts](https://web.dev/debug-layout-shifts)

## 참고자료

- [https://web.dev/cls/](https://web.dev/cls/)