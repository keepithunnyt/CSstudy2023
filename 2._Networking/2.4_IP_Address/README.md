# 2.4 IP Address

- [2.4.1 ARP (Address Resolution Protocol)](#241-arp-address-resolution-protocol)
- [2.4.2 홉바이홉 통신(hop by hop)](#242-홉바이홉-통신hop-by-hop)
  - [라우팅 테이블(routing table)](#라우팅-테이블routing-table)
  - [게이트웨이(gateway)](#게이트웨이gateway)
- [2.4.3 IP 주소체계](#243-ip-주소체계)
  - [클래스 기반 할당 방식(classful network addressing)](#클래스-기반-할당-방식classful-network-addressing)
  - [DHCP(Dynamic Host Configuration Protocol)](#dhcpdynamic-host-configuration-protocol)
  - [NAT(Network Address Translation)](#natnetwork-address-translation)
- [2.4.4 IP 주소를 이용한 위치 정보](#244-ip-주소를-이용한-위치-정보)

<br/>


---

# 2.4.1 ARP (Address Resolution Protocol)
- 컴퓨터 간의 통신은 IP 주소에서 ARP를 통해 MAC 주소를 찾아 통신한다.
- ARP를 통해 IP주소(가상주소)를 MAC주소(실제 주소)로 변환한다. (RARP는 반대 기능)
  1. 장치A가 ARP 브로드캐스트를 보내서, 원하는 IP주소에 해당하는 AMC주소를 찾는다. 
  2. 해당 주소에 맞는 장치 B가 ARP Reply 유니캐스트를 통해 MAC 주소를 반환하는 과정을 거쳐 IP주소에 맞는 MAC 주소를 찾는다.

  <br/>

- 용어
  - 브로드캐스트 : 송신 호스트가 전송한 데이터가 네트워크에 연결된 모든 호스트에 전송되는 방식
  - 유니캐스트 : 고유 주소로 식별된 하나의 네트워크 목적지에 1:1로 전송하는 방식


<br/>

---

# 2.4.2 홉바이홉 통신(hop by hop)
- IP 주소를 통해 통신망에서 각 패킷이 여러 라우터를 건너가는 모습을 비유적으로 표현
- 통신 장치 내의 라우팅 테이블의 IP를 통해, 시작 주소부터 도착 주소까지 도달하는 통신
  - 라우팅 : IP 주소를 찾아가는 과정


## 라우팅 테이블(routing table)
- 송신지에서 수신지까지 도달하기 위해 사용 
- 목적지 정보와 방법이 들어있는 리스트
- 게이트웨이, 해당 목적지에 도달하기 위해 거쳐야 할 다음 라우터 정보를 가지고 있음

## 게이트웨이(gateway)
- 서로 다른 통신망, 프로토콜을 사용하는 네트워크 간의 통신을 가능하게 하는 컴퓨터나 소프트웨어를 일컬음
- netstat -r 명령어를 통해 게이트웨이 확인 가능

<Br/>

---

# 2.4.3 IP 주소체계
- IPv4 : 32비트를 8비트 단위로 점을 찍어 표기
- IPv6 : 64비트를 16비트 단위로 점을 찍어 표기


## 클래스 기반 할당 방식(classful network addressing)
- IP 주소를 네트워크/호스트 주소의 바이트 길이에 따라 A ~ E의 5개 클래스로 구분
  - A, B, C는 일대일 통신으로 사용
  - D는 멀티캐스트 통신
  - E는 예비용으로 사용
- 구분 주소 : 맨 왼쪽 비트 (A는 0, B는 10)
- 호스트 주소 : 컴퓨터에 부여할 수 있는 주소
- 브로드캐스트 주소 : 가장 마지막. 네트워크에 속해있는 모든 컴퓨터에 데이터를 보내는 주소

- 단점 : 사용하는 주소보다 버리는 주소가 많았음.

## DHCP(Dynamic Host Configuration Protocol) 
- IP 주소 및 기타 통신 매개변수를 자동으로 할당하기 위한 네트워크 관리 프로토콜
- 다수의 라우터와 게이트웨이에 DHCP 기능이 있음.

## NAT(Network Address Translation)
- 패킷이 라우팅 장치를 통해 전송되는 동안, 패킷의 IP 주소 정보를 수정하여 IP주소를 다른 주소로 매핑하는 방법
- NAT로 공인IP와 사설IP로 나눠서 많은 주소를 처리함
- 여러 대의 호스트가 하나의 공인 IP 주소를 사용하여 인터넷에 접속하기 위해 NAT를 사용한다. (인터넷 공유기에 NAT 기능이 탑재되어 있다.)
- 외부에 드러나는 IP주소를 다르게 할 수 있어서 네트워크에 대한 일정 수준의 보안이 가능
- 단점 : 여러 멍이 동시에 인터넷에 접속하므로 실제 호스트 수에 따라 속도가 느려질 수 있다.


<br/>

---

# 2.4.4 IP 주소를 이용한 위치 정보
- IP 주소를 통해 동, 구 까지 위치 추적이 가능하다
  - https://mylocation.co.kr
