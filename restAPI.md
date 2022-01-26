 

Day1, 2-2. 그런 REST API로 괜찮은가 

REST API 변천사
WEB(1991)

인터넷에 정보를 공유하는 법

정보들을 하이퍼텍스트로 연결

표현 형식: HTML

식별자: URI

전송 방법: HTTP

REST(2000): Representational State Transfer

자원을 이름(표현)으로 구분하여 해당 자원의 상태(정보)를 전송 시키는 방법

HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시하고, HTTP Method(POST, GET, PUT, DELETE)를 통해 해당 자원에 대한 CRUD Operation을 적용하는 것을 의미한다.

[Network] REST란? REST API란? RESTful이란? - Heee's Development Blog 

WEB을 망가트리지 않고 HTTP를 진보시키는 방법

로이 필링의 박사 논문으로 창시된 개념

로이 필링이 rest를 만들게 된 계기: how do i improve http without breaking the web

API 등장

Salesforce API - SOAP 등장 (2000)

xml로 구현


망했음
 

flicker API 등장 (2004)

soap api이랑 rest api(박사논문) 두 개의 api 모두 제공

딱 봐도 rest가 훨씬 간단함 → 결국 rest api의 승리
4. rest api가 웹 세상 국룰이 되었는데 원작자 씅 받음

마솦의 레스트 api 가이드라인 발표

 우리가 알고 있던 rest api

but, 로이 필딩 트위터:

저거슨 HTTP API지, REST API가 아니다…!

그외 로이 필딩의 rest api에 관한 말말말

REST APIs must be hypertext-driven

REST API를 위한 최고의 버저닝 전략은 버저닝을 안 하는 것 

→ 사람들이 생각하는 rest api와 로이필딩이 생각하는 rest api의 개념이 다름

 그래서 레스트 api가 뭔데???

rest api
REST 아키텍쳐 스타일을 따르는 API

REST: 분산 하이퍼미디어 시스템(예: 웹)을 위한 아키텍쳐 스타일

 아키텍쳐 스타일: 제약 조건의 집합

rest 아키텍쳐 스타일을 따라야 찐 rest api인 것

rest 아키텍쳐 스타일(아키텍쳐 스타일의 집합)
6가지가 있으나 http만 잘 따라도? client-server, stateless, cache, layered system은 조건에 부합

client-server

stateless

cache

uniform interface → 얘를 만족 시키지 못하는 경우가 많음

layered system

code-on-demand → 옵션

서버에서 코드를 클라이언트에 보내서 실행시킬 수 있어야 함

자바스크립트 뜻하는 거라 함 

???ㅁ???

 

Unifrom Interface의 제약 조건
 identification of resources

리소스를 uri로 구분 되어야 함

manipulation of resources through representations

리프리젠테이션 전송을 통해서 리소스를 조작해야함

리소스를 만들거나 수정, 삭제할 때 http 메세지에 표현을 담아서 전송해야함

self-descriptive messages → 거의 안지켜짐

hypermedia as the enfine of application state (HATEOAS) → 거의 안지켜짐 

 

Uniform Interface의 제약 조건 중 안 지켜지는 두 가지 
1. self-descriptive message란? → descriptive: 설명적인
메세지는 스스로를 설명해야한다
api만 봐도 무슨 내용인지 파악할 수 있어야함

 

첫 번째 예시
HTTP 요청 메세지
보통은 GET /HTTP/1.1만 작성하고 목적지인 Host는 추가하지 않음

목적지 없으면 내용 파악이 않되므로 self-descriptive 하지 않은 api

 

두 번째 예시

HTTP/1.1 200 OK.                       # 응답 메세지

[{"op": "remove", "path": "/a/b/c"}]   # 본문
클라이언트는 어떤 문법으로 작성된지 모르므로 해석하지 못함

self-descriptive하지 않음

어떻게 해야 self-descriptive 해질까

content-type 추가


HTTP/1.1 200 OK.                       # 응답 메세지
Content-Type: application/json         # 어떤 문법으로 작성됐는지 추가

[{"op": "remove", "path": "/a/b/c"}]   # 본문(json)
content-type 헤더를 추가하므로 대괄호와 중괄호가 어떤 의미(문법)인지 알게 됨

문법 해석이 가능해지면 파싱?이 가능해짐

but, op, path의 의미는 여전히 파악 불가 → self-descriptive 하지 않음

 content-type 에 설명 추가


HTTP/1.1 200 OK.                       
Content-Type: application/json-patch+json   # 어떤 타입의 메세지인지 정의
[{"op": "remove", "path": "/a/b/c"}]   
json-patch+json 이란 미디어 타입으로 정의된 메세지

 클라이언트는 json-patch란 명세?를 찾아가서 op, path의 의미를  파악할 수 있음

api 메세지만으로 무슨 내용인지 해석할 수 있음 → self-descriptive 함

 

2. HATEOAS
애플리케이션 상태는 Hyperlink를 이용해 전이되어야 함

 

예시
요론게 애플리케이션 상태의 전이
/에서 환영합니다!를 띄워줌

 페이지의 링크를 따라 상태가 전이 됐으므로 hateoas라고 할 수 있음

 

html은 hateoas 만족
a 태크를 통해 하이퍼링크로 상태 전이 가넝
 

json으로도 만족시킬 수 있음

HTTP/1.1 200 OK.                       # 응답 메세지
Content-Type: application/json 
Link: </articles/1>; rel="previous",
      </articles/3>; rel="next";
{
  "title": "second article",
  "contents": "blah..."
}
Link라는 헤더를 통해 다른 리소스로 연결 시킬 수 있음

읽는 사람도 이해할 수 있어서 self-descriptive도 만족

 

우리는 왜 Uniform Interface을 지켜야할까?
독립적 진화를 위해
로이 필링이 rest를 만들게 된 계기: how do i improve http without breaking the web

서버와 클라이언트는 각각 독립적으로 진화한다

서버의 기능이 변경돼도 클라이언트를 업데이트할 필요 없다

 

레스트 api 잘 지켜진 사례
http2로 html 명세가 변경돼도 웹 브라우저는 잘 동작함

웹 브라우저가 지원 안하는 최신 html로 만들어진 웹 페이지여도 잘 동작함

페이지가 깨질 수도 있지만 동작은 함

↔︎ 앱: 앱은 서버와 클라이언트의 호환성을 위해 클라이언트가 앱을 업데이트 해야 사용할 수 있음
앱은 rest api로 서버와 클라이언트간 통신하지 않음

 

웹은 어쩜 그렇게 레스트를 잘 지킬 수 있을까?
웹 진영의 매우 많은 노력 덕분에

하위 호완성을 깨뜨리지 않기 위해 문서 작성만 7년 걸림
오타 고치면 웹이 깨져서 못 고침

charset이 아니라 인코딩이지만 못 고침

http 상태 코드가 추가되면서 415번까지 옴

만우절 때 만들었던 httpc 상태 코드 416을 만듦

많은 http 서버 구현체들(node.js, go)이 http 상태 코드 416을 이미 구현해놓음 

httpc는 http가 아니지만 현재는 http 상태코드 415 → 417로 넘어감(416은 공란)

크롬에서 http/0.9 한 번 빼볼려고 했는데 몇몇 서비스의 프락시 오류

포기

 

레스트가 웹의 독립적 진화에 도움을 주었나
예스, 레스트 만든 로이 필딩이 http와 uri 명세 저자 중 한 명

http에 지속적으로 영향을 줌

uri에서 리소스의 정의가 추상적으로 변경 → 식별하고자 하는 무언가

예전에는 리소스가 위치한 곳을 정의

host 헤더 추가

→ REST는 웹의 독립적 진화를 위해 만들어졌고 웹은 독립적으로 진화했으므로 REST는 성공!
 

그럼 REST API는 성공했나? 
rest api는 rest 아키텍쳐 스타일을 따라야하나 rest api라고 하는 대부분의 api들이 rest 아키텍쳐 스타일을 따르지 않음

로이필딩: rest api는 하이퍼텍스트를 포함한 self-descriptive한 메세지의 uniform interface를 통해 리소스에 접근하는 api므로 지켜지지 않으면 rest api가 아니다😡

지키기 어려운데 꼭 지켜야 하나요?🥲

로이필딩: 놉

시스템 전체 통제란

가령 서버 개발과 클라이언트 개발을 같이 한다면 굳이 쓸 필요가 없다

진화란

앱의 강제 업데이트처럼 불편함을 감소할 수 있다면 굳이 쓸 필요가 없다

 

REST API의 현재
영원히 고통받는 로이 필딩
 

왜 API는 REST가 잘 안되는걸까
일반적인 웹과 비교 
JSON 잡았다 욘석
HTML과 JSON의 비교
HTML 명세: HTML에서 사용할 수 있는 모든 태그가 정의 돼 있음

↔︎ 반면 json 오브젝트 내에서 key-value를 어떻게 정의하든 사용자의 맘

문법은 정의되어 있어서 불완전하게나마 정의 

문법은 해석 가능하나 의미를 해석할려면 별도의 문서(API 문서 등)가 필요

 

TO-DO 리스트 비교하기
html은 self-descriptive한가
그렇다
html은 hateoas한가
a 태그로 하이퍼링크를 통해 상태 전이하므로 그렇다

 

 

json은 self-descriptive한가
놉
 

json은 hateoas한가
하이퍼링크가 없어서 놉

 

[도전] REST API 만족해보기
이걸 언제 해
그냥 우리 스웨거 쓰자😇
단점: 링크 표현법을 media type이던 profile이던 명세에 정의해야함
JSON으로 하이퍼링크를 표현하는 법을 정의한 명세는 이미 존재함

json api, hal, uber 등등

단점: 명세에서 요구하는걸 따라야 하므로 기존 api를 많이 고쳐야함(침투적)

link, location 등의 http 헤더는 하이퍼 링크를 표현할 수 있음

 

 

하 기차나

저거 다 지켜야해?

로이 필딩: 너나 보는 사람이 이해할 수 있다면 굳이 안해두 됨

얼탱 없네???

 

정리
손이 아파…
강연자 피셜: rest api 아니더라도 rest api라고 우겨도 됨 ㅇㅇ
아??? 기존 rest api에 대해 계속 부정적으로 발표했으면서 막판에 '너가 편한대로 살아 화이팅^^!' 하는게 어딨음 ㅠㅠ

하지만 좋은 강의였다 두루뭉실했던 api 개념이 조금 더 구체적으로 잡힘 
