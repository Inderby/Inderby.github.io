---
title: [Computer-Network 8. ARP(Address Resolution Protocol)]
author: excelsiorKim
date: 2024-06-05 19:40:03 +0900
categories: [CS, ComputerNetwork]
tags: [CS, ComputerNetwork]
keywords: [CS, ComputerNetwork]
description: 컴퓨터 네트워크 공부 기록
toc: true
toc_sticky: true
---

### ARP란?
![ARP](/assets/img/2024-06-06-Network-8/ARP.png)
- 네트워크 계층(IP 주소)과 데이터 링크 계층(MAC 주소) 간의 주소 매핑을 제공하는 프로토콜이다.
- ARP는 IPv4 네트워크에서 사용되며, IP 주소를 해당 노드의 MAC 주소로 변환하는 역할을 한다.
- Address mapping : logical address(IP. Source to Destination), physical address(mac. hop to hop)

### ARP Operation
![ARP-Operation](/assets/img/2024-06-06-Network-8/ARP-Operation.png)
- 송신 노드는 목적지 IP 주소를 확인한다.
- 송신 노드는 자신의 ARP 캐시(ARP Cache)에서 해당 IP 주소에 대응하는 MAC 주소를 검색한다.
- 만약 ARP 캐시에 해당 정보가 없다면, 송신 노드는 브로드캐스트 MAC 주소(FF:FF:FF:FF:FF:FF)를 사용하여 ARP 요청(ARP Request) 메시지를 네트워크에 브로드캐스트한다.
- ARP 요청 메시지에는 송신 노드의 IP 주소, MAC 주소, 그리고 목적지 IP 주소가 포함된다.
- 네트워크의 모든 노드는 ARP 요청 메시지를 수신하지만, 요청된 IP 주소를 가진 노드만 ARP 응답(ARP Reply) 메시지를 유니캐스트로 송신 노드에게 전송한다.
- ARP 응답 메시지에는 요청된 IP 주소에 해당하는 MAC 주소 정보가 포함된다.
- 송신 노드는 수신한 ARP 응답을 기반으로 자신의 ARP 캐시를 업데이트하고, 해당 MAC 주소를 사용하여 데이터를 전송한다.

### ARP Packet
![ARP-Packet](/assets/img/2024-06-06-Network-8/ARP-Packet.png)
- Operation Code
  - ARP : IP -> MAC
  - RARP : MAC -> IP

### ARP 예시
![ARP-Example](/assets/img/2024-06-06-Network-8/ARP-Example.png)


### ARP Component
![ARP-Component](/assets/img/2024-06-06-Network-8/ARP-Component.png)
- Output-Module
  - IP Packet이 들어오면 Cache table을 먼저 찾아봄
    - case1 : R로 기입되어 있으면 Cache에서 찾은 mac주소와 IP packet을 data link layer로 전송
    - case2 : 없으면 Cache에 P로 적어놓고, queue를 만들어 packet을 queue에 넣어 놓고, ARP request를 보냄
    - case3 : 있긴 있는데 P로 적혀져 있는 경우 이미 Packet에 의해 output 모듈이 일하고 있는 것이기 때문에 queue에 가서 대기
- Input-Module
  - ARP Packet이 들어오면 동작
    - case 1 : Table 탐색 후 State가 P 일 때, 해결 안된 상태인데 정보가 들어온 상황이므로, P를 R로 바꾸고. queue에서 대기하고 있는 패킷들을 꺼냄
    - case 2 : R일 때, 지금 들어온 Input을 가지고 최신 정보로 업데이트
    - case 3 : 아예 존재 안할 때, cache에 저장해놓음


- ARP 캐시(ARP Cache):
  - 각 노드는 ARP 캐시라는 메모리 공간을 유지하며, 여기에 IP 주소와 MAC 주소의 매핑 정보를 저장한다.
  - ARP 캐시는 일정 시간 동안 유지되며, 시간이 지나면 해당 항목은 캐시에서 제거된다(일반적으로 몇 분 정도).
  - ARP 캐시를 사용하여 최근에 통신한 노드의 MAC 주소를 신속하게 확인할 수 있어 불필요한 ARP 요청을 줄일 수 있다.

### 보안 고려 사항
- ARP는 보안 측면에서 취약점을 가지고 있다.
  - ARP 스푸핑(ARP Spoofing) : 공격은 공격자가 위조된 ARP 응답을 전송하여 트래픽을 가로채는 공격 기법이다.
- 이를 방지하기 위해 스태틱 ARP 항목 설정, ARP 검사(DAI, Dynamic ARP Inspection) 등의 보안 메커니즘을 사용할 수 있다.