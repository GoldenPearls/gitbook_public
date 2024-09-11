# HTTP의 기본

![](https://velog.velcdn.com/images/prettylee620/post/10625551-8095-4d58-add6-100f3576ab65/image.png)

> 💡 간단 요약 : 인터넷 통신은 IP 프로토콜을 통해 이뤄지고, TCP는 연결 지향적이고 신뢰성이 높은 가상 회선 방식, UDP는 간단하고 빠른 데이터그램 방식이야. TCP는 연결 설정 후 안전하게 데이터 전송하고, UDP는 빠르게 전송하되 순서나 에러에 대한 보장은 없어. 포트는 프로세스 식별에 쓰이고, DNS는 도메인 명을 IP 주소로 변환해줘.

## 1. 모든 것이 HTTP(HyperText Transfer Protocol)

### 1) HTTP 메세지에 모든 것을 전송

1. 전송종류

* HTML, TEXT
* IMAGE, 음성, 영상, 파일
* JSON, XML (API)

2. 거의 모든 형태의 데이터 전송 가능
3. 서버간에 데이터를 주고 받을 때도 **대부분 HTTP 사용**
4. 1997년에 나온 `HTTP/1.1` 버전이 가장 많이 사용됨

### 2) 기반 프로토콜

![](https://velog.velcdn.com/images/prettylee620/post/0364c56a-a259-4b88-8f0f-bcb368105d6b/image.png)

1. TCP : HTTP/1.1, HTTP
2. UDP : HTTP/3

### 3) HTTP 특징

1. 클라이언트 서버 구조
2. 무상태 프로토콜(스테이스리스), 비연결성
3. HTTP 메세지
4. 단순함, 확장 기능

## 2. 클라이언트 서버 구조

1. Request, Response 구조
2. 클라이언트는 서버에 요청을 보내고, 응답을 대기
3. 서버가 요청에 대한 결과를 만들어서 응답

### 서버와 클라이언트 분리가 중요

1. 서버

* 데이터
* 비즈니스 로직

2. 클라이언트

* UI
* 사용성

## 3. 무상태 프로토콜

### 1) 스테이스리스(Stateless)

1. 서버가 클라이언트의 상태를 보존 x
2. 장점 : 서버 확장성 높음(스케일 아웃)
3. 단점 : 클라이언트가 추가 데이터 전

### 2) Stateful, Stateless 차이

#### Stateful(상태 유지)

1. 고객: 이 노트북 얼마인가요?
2. 점원: 100만원 입니다. . (노트북 상태 유지)
3. 고객: 2개 구매하겠습니다.
4. 점원: 200만원 입니다. 신용카드, 현금중에 어떤 걸로 구매 하시겠어요?**(노트북, 2개 상태 유지)**
5. 고객: 신용카드로 구매하겠습니다.
6. 점원: 200만원 결제 완료되었습니다. **(노트북, 2개, 신용카드 상태 유지)**

#### Stateless 중간에 점원이 바뀌면?(무상)

1. 고객: 이 노트북 얼마인가요?
2. 점원A: 100만원 입니다.
3. 고객: 2개 구매하겠습니다.
4. 점원B: ? 무엇을 2개 구매하시겠어요?
5. 고객: 신용카드로 구매하겠습니다.
6. 점원C: ? 무슨 제품을 몇 개 신용카드로 구매하시겠어요?

### 3) Stateful

1. `상태 유지`: 중간에 다른 점원으로 바뀌면 안된다.**(중간에 다른 점원으로 바뀔 때 상태 정보를 다른 점원에게 미리 알려줘야 한다.)**

![](https://velog.velcdn.com/images/prettylee620/post/14eeabc6-769b-44f3-bf7a-f3007cc2709c/image.png)

### 4) Stateless

* 아무 서버나 호출해도 된다.

1. `무상태`: 중간에 다른 점원으로 바뀌어도 된다.

![](https://velog.velcdn.com/images/prettylee620/post/4eb000d5-40ec-4659-8c06-528c54f0a6fc/image.png)

* 갑자기 고객이 증가해도 점원을 대거 투입할 수 있다.
* 갑자기 클라이언트 요청이 증가해도 서버를 대거 투입할 수 있다.
* 무상태에서는 고객이 필요한 정보를 그때 그때 넘겨줌 마지막말로만 구매할 수 있듯이
* 중간에 서버가 장애가 나면?

![](https://velog.velcdn.com/images/prettylee620/post/1216568c-0486-40f9-bcb3-ef7cb387bbeb/image.png)

2. 무상태는 응답 서버를 쉽게 바꿀 수 있다. **-> 무한한 서버 증설 가능(스케일 아웃)**

![](https://velog.velcdn.com/images/prettylee620/post/0de15d34-0ad1-4bb8-a7f2-585b1a74a324/image.png)

### 5) Stateless 실무 한계

1. 모든 것을 무상태로 설계 할 수 있는 경우도 있고 없는 경우도 있다.
2. 무상태
   * 예) 로그인이 필요 없는 단순한 서비스 소개 화면
3. 상태 유지
   * 예) 로그인
   * 로그인한 사용자의 경우 로그인 했다는 상태를 서버에 유지
   * 일반적으로 브라우저 쿠키와 서버 세션등을 사용해서 상태 유지
   * 상태 유지는 최소한만 사용

## 4. 비연결성(connectionless)

### 1) 연결을 유지하는 모델

* 서버는 연결을 계속 유지, 서버 자원 소모

![](https://velog.velcdn.com/images/prettylee620/post/3e4ee9a9-e725-4857-a24a-a1e334bbc0a7/image.png)

### 2) 연결을 유지하지 않는 모델

* 서버는 연결 유지X, 최소한의 자원 사용

![](https://velog.velcdn.com/images/prettylee620/post/14398cc4-ed8e-4c96-a25d-0862f6885ae9/image.png)

### 3) 비 연결성

1. `HTTP`는 **기본이 연결을 유지하지 않는 모델**
2. 일반적으로 초 단위의 이하의 **빠른 속도로 응답**
3. 1시간 동안 수천명이 서비스를 사용해도 실제 서버에서 동시에 처리하는 요청은 수십개 이 하로 매우 작음

* 예) 웹 브라우저에서 계속 연속해서 검색 버튼을 누르지는 않는다.
* 서버 자원을 매우 효율적으로 사용할 수 있음

### 4) 비 연결성 한계와 극복

* TCP/IP 연결을 새로 맺어야 함 - `3 way handshake 시간 추가`
* 웹 브라우저로 사이트를 요청하면 HTML 뿐만 아니라 자바스크립트, css, 추가 이미지 등 등 수 많은 자원이 함께 다운로드
* 지금은 `HTTP 지속 연결(Persistent Connections)`로 문제 해결
* HTTP/2, HTTP/3에서 더 많은 최적화

### 5) HTTP 초기 : 연결, 종료 낭비

![](https://velog.velcdn.com/images/prettylee620/post/e2733a5e-70cf-438c-b585-2806cc35065e/image.png)

### 6) HTTP 지속 연결(Persistent Connections)

* 다 받을 때까지 연결을 유지해둠

![](https://velog.velcdn.com/images/prettylee620/post/023ec0f7-b901-4771-90e7-221cdb1c1a00/image.png)

### 7) 스테이스리스를 기억하자(서버 개발자들이 어려워하는 업무)

* 정말 같은 시간에 딱 맞추어 발생하는 대용량 트래픽
* 예) 선착순 이벤트, 명절 KTX 예약, 학과 수업 등록
* 예) 저녁 6:00 선착순 1000명 치킨 할인 이벤트 -> 수만명 동시 요청
* 보통 첫 페이지는 정적 페이지인 html(상태 없음) ⇒ 그 후 이벤트 버튼 누르게

## 5. HTTP 메세지

### 1) 요청 메세지와 응답 메세지 구조

![](https://velog.velcdn.com/images/prettylee620/post/74008ab2-b147-4859-9546-eaf7dffba786/image.png)

### 2) 요청 메세지 공식 스펙

![](https://velog.velcdn.com/images/prettylee620/post/ff8425af-8ff5-4c6e-8cf4-0de00184edb1/image.png)

#### 시작라인(요청 메세지)

* start-line = **request-line** / status-line
* **request-line** = method SP(공백) request-target SP HTTP-version CRLF(엔터)
* HTTP 메서드 (GET: 조회)
* 요청 대상 (/search?q=hello\&hl=ko)
* HTTP Version

#### 시작라인(요청메세지-HTTP 메서드)

* 종류: GET, POST, PUT, DELETE...
* 서버가 수행해야 할 동작 지정
  * `GET`: 리소스 조회
  * `POST`: 요청 내역 처리

#### 시작라인(요청 메세지-요청 대상)

* absolute-path\[?query] (절대경로\[?쿼리])
  * 절대경로= `"/"` 로 시작하는 경로
  * 참고: \*, http://...?x=y 와 같이 다른 유형의 경로지정 방법도 있다.

#### 시작 라인(요청메세지 - HTTP 버전)

![](https://velog.velcdn.com/images/prettylee620/post/b0ed4622-d791-4e02-8bc8-d30d1661978c/image.png)

* HTTP 버전

### 3) 시작라인(응답 메세지)

![](https://velog.velcdn.com/images/prettylee620/post/d23d9a6c-5564-47f0-86ce-35ac8ef0f22b/image.png)

* start-line = request-line / **status-line**
* **status-line** = HTTP-version SP status-code SP reason-phrase CRLF
* HTTP 버전
* `HTTP 상태 코드`: 요청 성공, 실패를 나타냄
  * 200: 성공
  * 400: 클라이언트 요청 오류
  * 500: 서버 내부 오류
* 이유 문구: 사람이 이해할 수 있는 짧은 상태 코드 설명 글

### 4) HTTP 헤더

* header-field = field-name ":" OWS field-value OWS **(OWS:띄어쓰기 허용)**
* **field-name**은 대소문자 구문 없음

![](https://velog.velcdn.com/images/prettylee620/post/6359f7c8-fdf3-4ee5-a098-8b22eb937143/image.png)

#### 용도

1. HTTP 전송에 필요한 **모든 부가정보**

* 예) 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트(브라우저) 정보, 서버 애플리케이션 정보, 캐시 관리 정보...

2. 표준 헤더가 너무 많음

* https://en.wikipedia.org/wiki/List\_of\_HTTP\_header\_fields

3. 필요시 임의의 헤더 추가 가능

* helloworld: hihi

### 5) HTTP 메세지 바디

![](https://velog.velcdn.com/images/prettylee620/post/97807144-52e6-458c-a6e4-fc47df226bee/image.png)

#### 용도

* 실제 전송할 데이터
* HTML 문서, 이미지, 영상, JSON 등등 byte로 표현할 수 있는 모든 데이터 전송 가능

### 6) 단순함의 확장 가능

* HTTP는 단순하다. 스펙도 읽어볼만...
* HTTP 메시지도 매우 단순
* 크게 성공하는 표준 기술은 단순하지만 확장 가능한 기술

### 7) HTTP 정리

1. HTTP `메시지`에 **모든 것을 전송**
2. HTTP 역사 **HTTP/1.1**을 기준으로 학습
3. 클라이언트 서버 구조
4. 무상태 프로토콜(스테이스리스)
5. HTTP 메시지
6. 단순함, 확장 가능
7. 지금은 HTTP 시대
