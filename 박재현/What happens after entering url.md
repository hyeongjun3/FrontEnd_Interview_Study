# 브라우저에 웹사이트 주소 입력했을 때 일어나는 일

## 1. 브라우저에서 URI 해석
scheme:[//[user:password@]host[:port]][/]path[?query][#fragment]
- 만약 URI 가 문법에 맞지 않으면 검색어를 입력한 것으로 간주하고 검색엔진으로 검색을 하게 된다


## 2. HSTS 목록 조회
- HTTS (HTTP Strict Transport Seurity)
  - 서버가 HTTPS 프로토콜만 허용하길 원하면 응답 헤더에 Strict Transport Seurity 를 포함한다
  - 이를 브라우저가 확인하면 해당 서버에  요청할 때 HTTPS 프로토콜만 이용하고, 해당 URL을 HSTS 캐시에 저장한다
- 목록에 있으면 첫 오청을 HTTPS 로 보내고 아니면 HTTP로 보낸다

## 3. URL을 IP주소로 변환
- google.com 같은 사람이 읽기 쉬운 주소를 컴퓨터가 읽을 수 있도록 IP 주소로 변환
1. 브라우저가 자신의 로컬 hosts 파일과 브라우저 캐시에 해당 URL 가 존재하는지 확인
2. 존재하지 않는다면 도메일 주소를 IP주소로 변환해주는 DNS 서버에 요청해서 해당 URL을 IP주소로 변환
### DNS 서버에 요청하는 과정
WIP)


## 4. 라우터를 통해 해당 서버의 게이트웨이까지 이동
- DNS 서버에게 IP 주소를 받았으니 해당 서버로 요청을 보낸다.
- 네트워크 장비인 라우터가 해당 주소까지의 경로를 라우팅 하여 통해 해당 주소에 요청을 전달한다.
## 5. ARP를 통해 IP주소를 MAC주소로 변환
- 실질적인 통신을 하기 위해서는 논리 주소인 IP 주소를 물리 주소인 MAC 주소로 변환해야 한다.

- IP와 MAC의 차이를 간단하게 설명하자면 택배를 A 아파트 306호에 보내야 할 경우 
 - A 아파트의 주소는 IP 주소에 해당되며 
 - 306호는 MAC 주소에 해당된다.
## 6. 브라우저와 서버가 TCP 소켓 연결
- 실제로 통신하기 위해 TCP 소켓 연결을 진행
- TCP 소켓 연결은 3-way-handshake라는 과정을 통해 이루어진다.
- 만약, 현재 요청이 HTTP가 아닌 HTTPS 요청이라면 암호화 통신을 위한 TLS 핸드셰이킹이 추가된다. 이를 통해 서버와 클라이언트는 암호화 통신을 진행할 수 있다.

## 7. HTTP(HTTPS) 프로토콜로 요청, 응답
1. 이제 연결이 확정되었으니 해당페이지의 데이터를 달라고 서버에 요청한다
2. 서버가 이 요청을 수락할 수 있는지 검사
3. 서버가 응답을 생성해서 브라우저에게 전달

## 8. 브라우저에서 응답을 해석
- HTML, CSS, JavaScript 들을 해석해서 렌더링
### references
- https://owlgwang.tistory.com/1
- https://deveric.tistory.com/97
- https://devjin-blog.com/what-happen-browser-search/
- https://chs96.github.io/컴퓨터%20네트워크/웹-브라우저에-URL을-입력했을-때-발생하는-일/