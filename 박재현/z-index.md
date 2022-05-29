# z-index 와 stacking context (natural stacking order)

- ref
    - [https://www.zerocho.com/category/CSS/post/5a18b330e9c0ec001b08238e](https://www.zerocho.com/category/CSS/post/5a18b330e9c0ec001b08238e)
    - [https://mytory.net/archives/10997](https://mytory.net/archives/10997)
    - [https://erwinousy.medium.com/z-index가-동작하지않는-이유-4가지-그리고-고치는-방법-d5097572b82f](https://erwinousy.medium.com/z-index%EA%B0%80-%EB%8F%99%EC%9E%91%ED%95%98%EC%A7%80%EC%95%8A%EB%8A%94-%EC%9D%B4%EC%9C%A0-4%EA%B0%80%EC%A7%80-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EA%B3%A0%EC%B9%98%EB%8A%94-%EB%B0%A9%EB%B2%95-d5097572b82f)

요소 의 앞뒤로 보이는 순서 조정하는 속성

## z-index 적용 규칙

### 기본 규칙

- z-index 를 이용하기 위해서는 `position: static` 이 아니어야 한다
- 먼저 각 Stacking Context (쌓임 맥락)의 앞뒤 순서를 생각한다
    - 아무리 z-index 가 높아도 속한 쌓임 맥락을 벗어날 수 없다
- 다음으로 같은 쌓임 맥락 안에서의 순서를 생각한다

### 같은 쌓임 맥락 안에서의 순서

가장 아래에 보임 → 가장 위에 보임

1. 쌓임 맥락의 뿌리(root) 요소 
    - 다른 것들을 포함하는 요소
2. `static`이 아니고, `z-index` 값이 음수인 요소(와 자식들). 
3. `static` 인 것들
4. `static`이 아니고,  `z-index:auto` 혹은 `z-index:0`인 요소(와 그 자식들)
5. `static`이 아니고,  `z-index` 값이 양수인 요소(와 그 자식들)

## 기타

### Stacking Contexts 가 만들어지는 경우

- 요소가 문서의 최상위 요소일 때 (즉, `<html>` 요소)
- 요소가 `position: static` 이 아니면서 `z-index: auto` 가 아닐 때
    - 일부에선 `position: fixed` 이면 `z-index: auto` 여도 생긴다
- 요소의 `opacity` 값이 1보다 작을 때
    - **opacity 또는 transform같은 css 속성들을 설정하면 element가 새로운 stacking context에 배치된다.**
    - `position: static` 이 아니면서 `z-index: 0` 가 된 것처럼 작동한다
        - position 이나 z-index 를 설정하지 않았어도
- 일부 브라우저에서, `position: fixed` 일 때
    - 즉 `z-index:auto` 이여도 `position:fixed` 이면 쌓임 맥락이 생성된다
