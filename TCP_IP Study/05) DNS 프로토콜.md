# DHCP, DNS 프로토콜



## DHCP 프로토콜

DHCP는 ISP(인터넷 서비스 업체 ex.kt)에서 가지고 있는 IP를 임대해주는 인터넷 프로토콜로 알고 있다

> 동적 호스트 설정 프로토콜(Dynamic Host Configuration Protocol):

- 호스트에게 ip주소, 서브넷 마스크, 라우팅 ip 주소, dns 서버 ip 주소를 일정 시간 할당해주는 프로토콜

- 우리의 공유기가 라우터와 DHCP 서버의 역할을 한 번에 하고 있다구 한다!

- IPv4의 IP 주소가 고갈 되었기 때문에 IP를 임대해오고 반납하는 **유동 IP**를 할당 받는다고 한다

- 공유기는 DHCP 서버와 NAT 기능, 스위치 기능, 방화벽 기능을 포함한 라우터라고 합니다

  > 욘석 나보다 더 열심히 살고 있었구나ㅠ^ㅠ

- 공유기가 IP 주소를 요청하면 ISP의 IP 주소 데이터베이스에서 남아있는 IP 주소를 가져온다


[DHCP 참고 자료 출처](https://dthumb-phinf.pstatic.net/?src=%22https%3A%2F%2Fimg.extrememanual.net%2F2016%2F11%2Fdhcp_server_title.jpg%22&type=ff500_300)](https://extrememanual.net/8698)



## DNS 프로토콜



> 저는 DNS가 도메인과 맵핑된 IP를 가지고 있는 서버 자체인줄 알았는데
>
> NS(Name Server)의 서버를 DNS 서버라고 일반적으로 표현하는거였더라구요? ^ㅠ^



DNS 프로토콜은 NS을 포함해서 도메인을 어떻게 IP로 바꾸는지 모든 과정을 포함한 프로토콜인거 같스빈다



그 동안 무지성으로 `nslookup`을 썼었는데😥 

이번에 찾아보면서 name server lookup이라는걸 알게 됐습니다 ㄷ ㄷ



HTTP 헤더의 HOST 필드에 도메인을 명시하여 웹 서버를 구분할 수 있다고 합니다



위 포스팅을 보면 DNS 프로토콜의 개념을 정리해보도록 하겠슴다

![img](https://postfiles.pstatic.net/MjAyMTEyMTZfODIg/MDAxNjM5NjQ5MTk5MTE2.OBMtoFweufqj66cp-_GM3p_zxGYNxIcxmGR-q09959og.C_b_drMF9EnHf-ayi_j5IYYnJw44ZYB1XeMX9cOW5jUg.PNG.thwjd2717/image.png?type=w773)



DNS 레코드

A레코드: 특정 도메인에 매핑하는 IP주소(IPv4)

\> nslookup -type=a naver.com 서버:    kns.kornet.net Address:  168.126.63.1 권한 없는 응답: 이름:    naver.com Addresses:  223.130.195.95          223.130.195.200          223.130.200.107          223.130.200.104

kns.kornet.net, 168.126.63.1는 KT의 도메인 서버입니다



하나의 도메인에 여러 개의 IP를 할당할 수도 있슴다

네이버는 하나의 도메인으로 A레코드에 4개의 IP 주소가 있음을 확인할 수 있습니다



네임서버: 도메인 이름과 IP주소를 서로 상호 전환 시켜주는 서버를 뜻 함

\- 각 도메인에 적어도 한 개 이상 있어야 하며 DNS 서버를 가르킴

\- 보통 도메인 네임 하나당 2개 이상의 네임 서버를 가지고 있다함

```sh
> nslookup -type=ns naver.com 
서버:    kns.kornet.net Address:  168.126.63.1 
권한 없는 응답: naver.com       
nameserver = e-ns.naver.com naver.com       
nameserver = ns1.naver.com naver.com       
nameserver = ns2.naver.com ns1.naver.com   
internet address = 125.209.248.6 ns2.naver.com   
internet address = 125.209.249.6 e-ns.naver.com  
internet address = 175.158.6.250
```



네이버는 3개의 ns 서버가 같은 ip주소로 운영되고 있음을 확인할 수 있었습니다

DNS 질의를 NS 서버가 해주는건가?!



근데 권한 없는 응답이 계속 뜹니다

왜일까요?



`Non-authoriative answer`:

현재 도메인이 실제로 사용하고 있는 DNS 서버가 아닌,

다른 DNS 서버에서 저장한 캐시를 보여줬다는 뜻이라고 합니다



AAA 레코드

A 레코드와 같은 역할을 하지만 IPv4가 아닌 IPv6 주소 체계에 사용 됩니다



[DNS 레코드 출처](https://blog.o3g.org/network/dns-record/)