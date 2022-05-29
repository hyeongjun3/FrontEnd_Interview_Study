# will-change

- ref
    - [https://developer.mozilla.org/en-US/docs/Web/CSS/will-change](https://developer.mozilla.org/en-US/docs/Web/CSS/will-change)
    - [https://wit.nts-corp.com/2017/06/05/4571](https://wit.nts-corp.com/2017/06/05/4571)

## wil-change  란

브라우저에게 특정 스타일 속성이 바뀔 것임을 알려줘서 성능 최적화를 시키는 속성

- value
    - `auto`
        - 기본값
        - 아무것도 하지 않는다
    - `scroll-position`
        - 애니메이션이 발생하거나 요소의 스크롤 위치가 바뀔 것을 알려준다
    - `contents`
        - 애니메이션이 발생하거나 요소의 컨텐츠가 바뀔 것을 알려준다
    - `<custom-ident>`
        - 직접 스타일 속성을 지정
        - 대표적인 예
            - opacity, transform, top, left, right, bottom

## 주의사항

### 마지막 수단으로서 이용할 것, 너무 많은 속성과 요소에 이용하지 말것

- will-change 자체도 어느정도 자원을 소모한다

### 애니메이션 동작이 끝난후 기본 상태로 되돌릴 것

- 요소의 변경이 종료되면 반드시 will-change를 삭제해서 will-change에 사용하고 있던 자원을 회수해야 한다
- will-change 의 경우 다른 최적화에 비해 최적화를 길게 유지한다
    - 따라서 자원을 일찍 회수해야 한다

### 브라우저에게 미리 will-change 사용을 알려줘야 한다

- 변화가 일어날 요소에 will-change를 직접 선언하면 적용이 되지 않는다.
    
    ```css
    /* 변화할 요소와 will-change가 같이 있으므로 적용이 안 됨 */
    .box {
        transform: rotate(180deg);
        transition: transform 1s linear;
        will-change: transform;
    }
    ```
    
- 선택자 `:hover`, 자바스크립트 `mouseenter` 등을 통하여 미리 알려주어야 한다
    
    ```css
    /* hover를 통해서 will-change를 선언 했으므로 적용 */
    .box:hover {
        will-change: transform;
    }
    .box {
        transition: transform 1s linear;
    }
    .box:active {
        transform: rotate(180deg);
    }
    ```
