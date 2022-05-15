# Website Rendering Process 웹사이트 렌더링 과정과 최적화

## 기본 렌더링 과정
1. 서버로 부터 리소스 요청, 받기
2. Render Tree 형성
  - DOM, CSSOM 를 결합
3. Layout 처리
4. Paint 처리

### 1. 서버로 부터 리소스 요청, 받기
브라우저가 HTML, CSS, Javascript, 이미지, 폰트 파일 등 렌더링에 필요한 리소스를 요청하고 서버로부터 응답을 받는다

### 2. Render Tree
DOM 및 CSSOM을 결합하여 렌더링 트리를 형성한다
- HTML 을 처리 (parse)해서 DOM (Document Object Model) Tree 를 빌드
- CSS 를 처리해서 CSSOM (CSS Object Model) Tree 빌드
- 자바스크립트를 실행해서 DOM API를 통해 DOM이나 CSSOM을 변경할 수 있다
  - 변경된 DOM과 CSSOM은 다시 렌더 트리로 결합된다

### 3. Layout
뷰포트(Viewport) 내에서 렌더링 트리의 각 노드들의 정확한 위치와 크기를 계산한다
- Box-Model 을 생성한다
- %, vh, vw 등 상대적인 위치, 크기 속성이 이때 pixel 단위로 계산된다

### 4. Paint 
요소들의 위치와 크기, 스타일 계산이 완료된 Render Tree 를 이용해 실제 픽셀 값을 채워넣는다
- Paint / Rasterize
  - Render Tree 를 화면의 픽셀로 변환하는 프로세스
  - layer 를 만든다
- Composite
  - 여러 layer 를 합성해서 실제 화면에 나타낸다

## 화면 업데이트 (리렌더링)
### Reflow
Layout 작업이 다시 일어나는 것
- reflow가 일어나면 repaint 도 발생된다
- 어떠한 액션이나 이벤트에 따라 html 요소의 크기나 위치등 레이아웃 수치를 수정하면 그에 영향을 받는 자식 노드나 부모 노드들을 포함하여 Layout 과정을 다시 수행하게 된다
- 예
  - 페이지 최초 렌더링
  - 브라우저 크기 바꿀 때 (viewport 크기 변경)
  - 노드 추가 또는 제거
  - 스타일 추가 또는 제거
  - 요소의 위치, 크기 변경
    - left, top, margin, padding, border, widht, height 등
  - 텍스트 내용 변경
    - input 값 변경 등
  - 이미지 크기 변경
  - 폰트 변경

### Repaint
Paint 작업이 다시 일어나는 것
- reflow 없이 repaint 만 일어날 수도 있다
- 예
  - 레이아웃에는 영향을 주지 않는 스타일 속성이 변경되었을 때
    - background-color, visibility 등

## Rendering Optimization 렌더링 최적화
- render tree 최적화
- reflow 최적화
- repaint 최적화

### Render Tree 최적화
- 렌더링하지 않는 노드에는 `visibilty: invisible;` 가 아닌 `display: none;` 사용하기
  - `visibilty: invisible;`: 레이아웃 공간을 차지하기 때문에 reflow의 대상이 된다
  - `display: none;`: Render Tree에서 아예 제외된다 (Layout 공간을 차지하지 않는다)

### Reflow 최소화
- 가능한 한 DOM 구조상 가장 하위의 노드에 스타일 변화를 주기
- 가능한 한 reflow 를 일으키는 스타일 속성보다는 repaint 만 발생시키는 속성 이용
  - `position:fixed` 또는 `position:absolute` 활용
    - 애니메이션이 있거나 레이아웃 변화가 많은 노드
  - `left`, `right`, `width`, `height` 보다는 `transform`
  - `visibility`, `display` 보다는 `opacity`
- reflow 를 일으키는 스타일 속성 예
  - position
  - width, height
  - left, top, right, bottom
  - margin, padding
  - border, border-width
  - clear
  - display
  - float
  - font-family, font-size, font-weight, line-height
  - overflow
  - text-align, vertical-align
  - white-space
  - ...

## Repaint 활용
- repaint 만을 일으키는 스타일 속성 예
  - background, background-image, background-position, background-repeat
  - border-radius, border-style
  - box-shadow
  - color
  - outline, outline-color, outline-style, outline-width
  - visibitly
  - ...

## references
- https://boxfoxs.tistory.com/408
- https://cresumerjang.github.io/2019/06/24/critical-rendering-path/
- https://boxfoxs.tistory.com/408
- https://webclub.tistory.com/346