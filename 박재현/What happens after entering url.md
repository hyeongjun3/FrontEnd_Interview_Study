# 브라우저에 웹사이트 주소 입력했을 때 일어나는 일

## 1. 브라우저에서 URI 해석
`scheme:[//[user:password@]host[:port]][/]path[?query][#fragment]`
- 만약 URI 가 문법에 맞지 않으면 검색어를 입력한 것으로 간주하고 검색엔진으로 검색을 하게 된다
- 예시 (https://www.naver.com:8080/blog?q=test#tab1)
  - scheme: `https`
    - https, http, ftp 등
  - user, password 는 일반적으로 쓰이지 않는다
  - host:  `www.naver.com`
  - port: 8080
    - 생략되면 HTTP라면 80 을, HTTPS라면 443 을 기본 값으로 요청
  - path: `/blog`
  - query: `q=test`
  - fragment: `#tab1`
- 도메인명에서 ascii 가 아닌 유니코드들을 ascii 로 변환 (퓨니코드 Punycode)
  
## 2. HSTS 목록 조회
- 해당 URL이 HTTS 목록에 있으면 첫 요청을 HTTPS 로 보내고 아니면 HTTP로 보낸다
- HTTS (HTTP Strict Transport Seurity) 란
  - 서버가 HTTPS 프로토콜만 허용하길 원하면 응답 헤더에 Strict Transport Seurity 를 포함한다
  - 이를 브라우저가 확인하면 해당 서버에  요청할 때 HTTPS 프로토콜만 이용하고, 해당 URL을 HSTS 캐시에 저장한다

## 3. URL을 IP주소로 변환
- www.google.com 같은 사람이 읽기 쉬운 주소를 컴퓨터가 읽을 수 있도록 IP 주소로 변환
1. 브라우저가 자신의 로컬 hosts 파일과 브라우저 캐시에 해당 URL가 있는지 확인
2. 없다면 도메일 주소를 IP주소로 변환해주는 DNS 서버에 요청해서 해당 URL을 IP주소로 변환
  - 특정 도메인의 실제 IP 주소가 뭔지 알고 있는 서버 

## 4. ARP를 통해 IP주소를 MAC주소로 변환
- 실질적인 통신을 하기 위해서는 논리 주소인 IP 주소를 물리 주소인 MAC 주소로 변환해야 한다.
  - IP와 MAC의 차이
    - IP
      - 통신기기에 할당된 식별번호
      - 변경될 수 있다
      - 통신사에게 일정 금액 지불하고 받아오는 것
    - MAC
      - 통신기기 하드웨어에 부여된 고유한 식별번호
      - 변경될 수 없다
  - ARP
    - Address Resolution Protocol , 주소 결정 프로토콜
    - OSI 7 계층에서 네트워크 계층 주소와 링크 계층 주소 사이의 변환을 담당

## 5. 브라우저와 서버가 TCP 소켓 연결
- 실제로 통신하기 위해 TCP 소켓 연결을 진행
- TCP 소켓 연결은 3-way-handshake라는 과정을 통해 이루어진다.
- 현재 요청이 HTTP가 아닌 HTTPS 요청이라면 암호화 통신을 위한 TLS 핸드셰이킹이 추가된다.

## 6. HTTP(HTTPS) 프로토콜로 요청, 응답
1. 이제 연결이 확정되었으니 해당페이지의 데이터를 달라고 서버에 요청한다
2. 서버가 이 요청을 수락할 수 있는지 검사
3. 서버가 응답을 생성해서 브라우저에게 전달

## 7. 브라우저에서 응답을 해석
- HTML, CSS, JavaScript 들을 해석해서 렌더링
### references
- https://owlgwang.tistory.com/1
- https://deveric.tistory.com/97
- https://devjin-blog.com/what-happen-browser-search/
- https://chs96.github.io/컴퓨터%20네트워크/웹-브라우저에-URL을-입력했을-때-발생하는-일/
- https://wangin9.tistory.com/entry/브라우저에-URL-입력-후-일어나는-일들-1URL을-해석한다?category=827054