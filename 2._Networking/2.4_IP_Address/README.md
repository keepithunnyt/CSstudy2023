# 2.4 IP Address
- [2.4.1 ARP (Address Resolution Protocol)](#241-arp-address-resolution-protocol)


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