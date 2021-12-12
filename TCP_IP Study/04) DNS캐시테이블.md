# TCP/IP

### DNS 캐시 테이블의 이해



### 도메인 네임( blog.naver.com)이 IP주소 223.130.195.68로 바뀌는 과정을 살펴보자

```shell
PS C:\Users\zeze> ping blog.naver.com

Ping blog.naver.com.nheos.com [223.130.195.68] 32바이트 데이터 사용:
요청 시간이 만료되었습니다.
요청 시간이 만료되었습니다.
요청 시간이 만료되었습니다.
요청 시간이 만료되었습니다.

223.130.195.68에 대한 Ping 통계:
    패킷: 보냄 = 4, 받음 = 0, 손실 = 4 (100% 손실),
```

**Host aliasing**(DNS 서버가 제공하는 서비스 중 하나)

호스트 이름이 너무 길 경우, 줄여서 부를 수 있도록 매핑 정보를 제공

www.naver.com의 실제 호스트 이름은 www.naver.com.nheos.com이나 너무 길어 줄여줌

(출처: https://ddongwon.tistory.com/74)



os는 도메인 네임에 대응 IP 주소를 아래 경로에 있는 파일에서 먼저 검색

C:\Windows\System32\drivers\etc\hosts

```shell
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#	127.0.0.1       localhost
#	::1             localhost
```



- blog.naver.com 도메인 네임 없음
  1. DNS 캐시 테이블에 도메인 네임 찾기
  2. 없을시 OS는 로컬 DNS 서버의 IP주소로 blog.naver.com 도메인 네임에 대한 질의 요청
     - KT 도메인 서버 IP 주소로 요청(168.126.63.1 168.126.63.2번)
  3. 로컬 DNS 서버로부터 IP주소 응답받으면 DNS 캐시테이블에 반영
     - ARP 요청 받아 목적지 MAC 주소를 ARP 캐시 테이블에 반영하는 동작과 같음



#### ARP 동작과 DNS 동작 비교

| 순서 | ARP 동작                              | DNS 동작                              |
| ---- | ------------------------------------- | ------------------------------------- |
| 1    | 출발지, 목적지 네트워크 ID 비교       | hosts 파일 검색                       |
| 2    | ARP 캐시 테이블 검색                  | DNS 캐시 테이블 검색                  |
| 3    | ARP 요청과 응답 수정                  | DNS 요청과 응답 수행                  |
| 4    | ARP 캐시 테이블에 목적지 맥 주소 변경 | DNS 캐시 테이블에 목적지 IP 주소 반영 |



#### ARP캐시테이블과 DNS캐시테이블 대응 관계 조작 보안 공격

- ARP/DNS 스푸핑 공격

  - DNS 스푸핑 공격은 파밍(Pharming) 공격이라고도 함

  - ARP/DNS 캐시 테이블 사용하는 주소 체계 조작

  - ```shell
    # hosts 파일 하단에 추가
    
    172.30.1.54 blog.naver.com
    ```

    - 블로그 도메인의 IP주소를 도커 사이트 IP 주소와 맵핑 시킴

      책에 나온 예시대로라면 블로그 도메인을 입력창에 넣으면 도커 사이트가 떠야되는데 정상적으로 블로그사이트 뜸...

      왜지???

- DHCP 스푸핑 공격:

  - DHCP 서버는 클라이언트에게 서버 IP 주소 할당 

  - 공격자가 가짜 DNS 서버 IP 주소 할당하면 클라이언트는 가짜 DNS 서버 이용

- DDoS(Distributed Denial of Service):

  - 출발지 IP 주소를 수시로 변경, 상대방에게 불필요한 데이터 계속 전송해 인위적으로 부하를 유발하는 기법