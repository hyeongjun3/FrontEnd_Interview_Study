# Canvas 최적화

canvas API는 웹에서 2D 그래픽을 렌더딩하는데 가장 많이 사용되는 도구 중 하나입니다. canvas를 사용하는 애플리케이션이라면 규모가 커질 수록 최적화에 대한 수요는 늘어나게 됩니다.  

canvas를 최적화하는 다양한 방법이 있습니다. 여기서는 간단하게 소개만 시켜 드리겠습니다.

## offscreen canvas를 이용한 Pre-render

만약 반복적으로 canvas에 그려야하는 객체가 있다면 해당 객체를 offscreen canvas에 pre-rendering 하여 성능을 개선할 수 있습니다.

예를 들어서, 마리오 게임이 있다고 생각합시다. 여기서 마리오를 그리는 동작을 pre-redenring하여 구현할 수 있습니다.

pre-render 사용 X

```jsx
// canvas, context are defined
function render() {
  drawMario(context);
  requestAnimationFrame(render);
}
```

사용 O

```jsx
var m_canvas = document.createElement('canvas');
m_canvas.width = 64;
m_canvas.height = 64;
var m_context = m_canvas.getContext('2d');
drawMario(m_context);

function render() {
  context.drawImage(m_canvas, 0, 0);
  requestAnimationFrame(render);
}
```

마리오를 그리는 `drawMario()` 동작이 비싸다면 위의 동작은 성능상 이득을 많이 얻을 수 있을 것 입니다. 

위의 예제는 offscreen canvas를 `canvas` element를 만들어서 생성하였지만 `OffscreenCanvas API` 를 이용하여 구현할 수 있습니다. 또한 offscreen canvas는 직접적으로 UI에 관여하지 않기 때문에 web worker에서 사용할 수 있습니다.

아래는 web worker + OffscreenCanvas 에제 입니다.

main.js (main thread code)

```jsx
const htmlCanvas = document.getElementById("canvas");
const offscreen = htmlCanvas.transferControlToOffscreen();

const worker = new Worker("offscreencanvas.js");
worker.postMessage({canvas: offscreen}, [offscreen]);
```

offscreencanvas.js (worker code)

```jsx
onmessage = function(evt) {
  const canvas = evt.data.canvas;
  const gl = canvas.getContext("webgl");

  // Perform some drawing using the gl context
};
```

또한 web worker를 사용하기 때문에 main thread의 유저의 상호작용을 방해하지 않습니다.

### 소수점 대신 정수 사용

floating point 값을 이용한 렌더링은 정수 값을 사용하는 렌더링보다 더 많은 연산을 필요합니다.

```abap
ctx.drawImage(myImage, 0.3, 0.5);
```

소수점 대신 `Math.floor()` 나 `Math.ceil()` 함수 등으로 정수 값으로 바꾸어준다.

```jsx
const dx = Math.floor(0.3);
const dy = Math.ceil(0.5);

ctx.drawImage(myImage, dx, dy);
```

### `drawImage()` 함수로 이미지 크기를 조정하지 말아라

`drawImage()` 함수로 하나의 이미지를 다양한 크기로 그리는 것보다 다양항 이미지를 가지고 렌더링 하는 것이 캐시를 활용할 수 있어서 성능상 이점이 있습니다.

### 복잡한 Scene을 그릴때는 여러개의 레이어 canvas를 사용

게임을 만든다고 생각해봅시다. 하나의 scene에 UI, 게임플레이, 배경화면이 있다고 생각해봅니다.

게임플레이는 매 프레임마다 변경되는 반면에, UI는 유저의 상호작용에만 변경이 되며, 배경화면은 변경되지 않을 것입니다.

여기서 3개의 layer를 구성하여 canvas를 구현할 수 있습니다.

```html
<div id="stage">
  <canvas id="ui-layer" width="480" height="320"></canvas>
  <canvas id="game-layer" width="480" height="320"></canvas>
  <canvas id="background-layer" width="480" height="320"></canvas>
</div>

<style>
  #stage {
    width: 480px;
    height: 320px;
    position: relative;
    border: 2px solid black;
  }

  canvas { position: absolute; }
  #ui-layer { z-index: 3; }
  #game-layer { z-index: 2; }
  #background-layer { z-index: 1; }
</style>
```

### Background image는 CSS를 사용

배경화면을 canvas에서 그리는 것보다 canvas를 `<div>` 로 감쏴서 `background` css를 이용해서 구현하는 것이 더 좋다. 

### CSS transform을 사용하여 canvas 크기를 조정

CSS transform은 GPU를 사용하기 때문에 크기를 직접 조절하는 것보다 더 빠릅니다. 가장 좋은 경우는 작은 사이즈의 canvas를 scale up 하는 것 입니다.

```jsx
const scaleX = window.innerWidth / canvas.width;
const scaleY = window.innerHeight / canvas.height;

const scaleToFit = Math.min(scaleX, scaleY);
const scaleToCover = Math.max(scaleX, scaleY);

stage.style.transformOrigin = '0 0'; //scale from top left
stage.style.transform = `scale(${scaleToFit})`;
```

### Transparency 끄기

만약 canvas에 trasnparency가 필요하지 않다면 `alpha` 값을 `false` 로 설정한다면 좋습니다.

```jsx
const ctx = canvas.getContext('2d', { alpha: false });
```

### 고해상도에서의 크기 조정

고해상도의 display의 경우 몇몇 아이템들이 blur 처리 되는 것을 보았을 것 입니다. 다양한 방법이 있지만 가장 좋은 방법은 canvas의 크기를 줄이거나 늘리는 것 입니다.

```jsx
// Get the DPR and size of the canvas
const dpr = window.devicePixelRatio;
const rect = canvas.getBoundingClientRect();

// Set the "actual" size of the canvas
canvas.width = rect.width * dpr;
canvas.height = rect.height * dpr;

// Scale the context to ensure correct drawing operations
ctx.scale(dpr, dpr);

// Set the "drawn" size of the canvas
canvas.style.width = `${rect.width}px`;
canvas.style.height = `${rect.height}px`;
```

### canvas 실행 합치기

예를들어서 여러개의 선을 그리는 것 보다 하나의 polyine를 그리는 것이 더 좋습니다. (예: 차트)

```jsx
for (var i = 0; i < points.length - 1; i++) {
  var p1 = points[i];
  var p2 = points[i+1];
  context.beginPath();
  context.moveTo(p1.x, p1.y);
  context.lineTo(p2.x, p2.y);
  context.stroke();
}
```

이후

```jsx
context.beginPath();
for (var i = 0; i < points.length - 1; i++) {
  var p1 = points[i];
  var p2 = points[i+1];
  context.moveTo(p1.x, p1.y);
  context.lineTo(p2.x, p2.y);
}
context.stroke();
```

### 가능하다면 `shadowBlur`사용과 텍스트 렌더링을 피해라

shadowBlur와 텍스트 렌더링은 비싼 동작이다.

### canvas를 지우는데 다양한 방법을 사용해라

canavs를 지우는데 방법은 다양합니다. `clearRect()` , `fillRect()` , 캔버스 사이즈 조정등 다양합니다. 상황에 맞게 사용할 줄 알야아 합니다.

### `setInterval()` 대신에 `window.requestAnimationFrame()` 를 사용해라

## 참고자료

- [https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Optimizing_canvas](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Optimizing_canvas)
- [https://web.dev/canvas-performance/](https://web.dev/canvas-performance/)
- [https://stackoverflow.com/questions/38709923/why-is-requestanimationframe-better-than-setinterval-or-settimeout](https://stackoverflow.com/questions/38709923/why-is-requestanimationframe-better-than-setinterval-or-settimeout)