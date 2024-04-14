---
title: [Computer-Network 4. Delivery and Forwarding of IP Packets]
author: excelsiorKim
date: 2024-04-12 20:40:03 +0900
categories: [CS, ComputerNetwork]
tags: [CS, ComputerNetwork]
keywords: [CS, ComputerNetwork]
description: 컴퓨터 네트워크 공부 기록
toc: true
toc_sticky: true
---

### Direct Delivery

![Direct-Delivery-img](/assets/img/2024-04-12-Network-4/Direct-Delivery.png)

- 송신자와 수신자가 같은 네트워크에 있을 때 사용되는 전달 방식
- 송신자는 수신자의 MAC 주소를 알아내기 위해 ARP(Address Resolution Protocol)을 사용한다.
- 송신자는 프레임에 수신자의 MAC 주소를 포함하여 데이터를 직접 전송한다.
- 데이터는 중간 노드(라우터)를 거치지 않고 송신자에서 수신자로 바로 전달 된다.
- 같은 LAN 내에서의 통신이 직접 전달에 해당된다.

### Indirect Delivery

![Indirect-Delivery-img](/assets/img/2024-04-12-Network-4/InDirect-Delivery.png)

- 송신자와 수신자가 서로 다른 네트워크에 있을 때 사용되는 전송 방식이다.
- 송신자와 수신자 사이에 하나 이상의 중간 노드(라우터)가 존재한다.
- 송신자는 수신자의 IP 주소를 기반으로 라우팅 테이블을 참조하여 다음 hop(라우터)의 MAC 주소를 알아낸다.
- 송신자는 프레임에 다음 hop의 MAC 주소를 포함하여 데이터를 전송한다.
- 데이터는 중간 노드(라우터)를 거쳐 수신자에게 전달된다. 각 라우터는 라우팅 테이블을 참조하여 적절한 다음 hop으로 데이터를 전달한다.
- 서로 다른 네트워크 간의 통신, 즉 WAN(Wide Area Network)에서의 통신이 간접 전달에 해당한다.

### Forwarding

- 패킷을 목적지까지 전달하는 과정을 말한다.
- 네트워크 장비가(라우터, 스위치 등) 수신한 패킷의 목적지 주소를 확인하고, 해당 목적지로 가는 최적의 경로를 선택해 패킷을 전송하는 것이 포워딩의 핵심이다.

### Next-hop method

![Next-Hop-method-img](/assets/img/2024-04-12-Network-4/Next-hop-method.png)

- 라우팅 테이블에서 패킷의 목적지 IP 주소에 대한 다음 홉(next hop)의 IP 주소를 직접 지정하는 방법
- 패킷의 목적지까지의 전체 경로를 명시하지 않고, 패킷이 다음에 전달되어야 할 라우터의 IP 주소만을 지정한다.
- 동작 원리
  1. 라우팅 테이블의 각 항목은 목적지 네트어크 주소, 서브넷 마스크, 그리고 다음 hop의 IP Address로 구성된다.
  2. 라우터는 수신한 패킷의 목적지 IP 주소를 라우팅 테이블의 목적지 네트워크 주소와 비교하여 가장 적합한 항목을 찾는다.
  3. 가장 적합한 항목의 다음 hop의 IP address로 패킷을 전달한다.
  4. 이 과정이 패킷이 목적지에 도달할 때까지 각 라우터에서 반복된다.
- 장점
  - 라우팅 테이블의 크기를 줄일 수 있다
  - 라우팅 결정 과정이 간단하고 빠르다.
  - 네트워크 토폴로지 변화에 유연하게 대응할 수 있다.

### Network-Specific Method

![Network-Specific-Method-img](/assets/img/2024-04-12-Network-4/Network-Specific-Method.png)

- 라우팅 테이블의 각 항목은 목적지 네트워크 주소와 해당 네트워크에 대한 다음 홉의 IP 주소로 구성된다.
- 목적지 네트워크 주소는 IP 주소의 네트워크 부분을 나타내며, 서브넷 마스크와 함께 사용된다.
- 라우터는 수신한 패킷의 목적지 IP 주소를 라우팅 테이블의 네트워크 주소와 비교하여 가장 적합한 항목을 찾는다.
- 가장 적합한 항목의 다음 홉 IP 주소로 패킷을 전달합니다.
- 이 방식은 목적지 네트워크에 속한 모든 호스트에 대한 경로를 하나의 항목으로 지정할 수 있어 라우팅 테이블의 크기를 줄일 수 있습니다.

### Host-Specific Method

![Host-Specific-Method-img](/assets/img/2024-04-12-Network-4/Host-Specific-Method.png)

- 라우팅 테이블의 각 항목은 특정 호스트의 IP 주소와 해당 호스트에 대한 다음 홉의 IP 주소로 구성된다.
- 라우터는 수신한 패킷의 목적지 IP 주소를 라우팅 테이블의 호스트 IP 주소와 정확히 일치하는 항목을 찾는다.
- 일치하는 항목의 다음 홉 IP 주소로 패킷을 전달한다.
- 이 방식은 특정 호스트에 대한 경로를 개별적으로 지정할 수 있어 더 세밀한 제어가 가능하다.
- 그러나 호스트 수가 많은 대규모 네트워크에서는 라우팅 테이블의 크기가 매우 커질 수 있다.

### Real-World Routing

![Default-Routing-img](/assets/img/2024-04-12-Network-4/Default-Routing.png)

- 라우터는 일반적으로 network-specific 방식과 host-specific 방식을 모두 지원하며, 두 방식을 혼합하여 사용할 수 있다.
- 라우팅 테이블에서 가장 구체적인 항목(host-specific)부터 가장 일반적인 항목(default route) 순으로 패킷의 목적지와 일치하는 항목을 찾는다.
- 이를 통해 라우터는 효율적이고 유연한 패킷 전달을 수행할 수 있다.

## Classless Forwarding

![Classless-Forwarding-img](/assets/img/2024-04-12-Network-4/Classless-Forwarding.png)

- Classless Forwarding은 CIDR(Classless Inter-Domain-Routing) 기반의 라우팅 방식으로, IP 주소를 클래스로 구분하지 않고 네트워크 부분의 길이를 가변적으로 지정할 수 있다.
- 이를 통해 IP 주소 공간을 더욱 효율적으로 사용하고, 유연한 네트워크 설계가 가능하다.

![Classless-Forwarding-Example-img](/assets/img/2024-04-12-Network-4/Classless-Forwarding-Example.png)

- 위의 사진과 같은 네트워크 구성이 존재할 때, R1 라우터의 라우팅 테이블은 아래와 같이 같이 구성된다.
  ![Classless-Forwarding-Routing-Table-img](/assets/img/2024-04-12-Network-4/Classless-Forwarding-Routing-Table.png)

- 라우팅 테이블의 구성 방식

  - 각 항목은 네트워크 주소, 서브넷 마스크, 다음 홉의 IP 주소로 구성된다.
  - 네트워크 주소와 서브넷 마스크는 CIDR 표기법으로 표현된다 (e.g., 192.168.1.0/24).
  - 서브넷 마스크는 네트워크 부분의 길이를 나타내며, IP 주소와 비트 단위로 AND 연산을 수행하여 네트워크 주소를 추출한다.

- 라우팅 테이블을 보고 네트워크 구성 추론 예제
  ![Routing-Table-Example-img](/assets/img/2024-04-12-Network-4/Routing-Table-Example.png)

  - 위와 같은 라우팅 테이블이 존재할 때 아래와 같은 네트워크 구성을 가지고 있다는 것을 추론할 수 있다.
    ![Routing-Table-Answer-img](/assets/img/2024-04-12-Network-4/Routing-Table-Answer.png)

### Address Aggregation

![Address-Aggregation-img](/assets/img/2024-04-12-Network-4/Address-Aggregation.png)

- 주소 집계(Address Aggregation)는 CIDR(Classless Inter-Domain Routing)에서 사용되는 기술로, 여러 개의 작은 네트워크 주소 블록을 하나의 큰 네트워크 주소 블록으로 결합하는 것을 말한다.
- 이를 통해 라우팅 테이블의 크기를 줄이고, 네트워크 성능을 향상시킬 수 있다.
- 즉, 공통점을 찾아서 Routing Table을 줄이는 기법이다.

- 위의 이미지에서 Organization1 ~ 4까지 140.24.7.X/24 라는 공통적인 네트워크 주소를 갖기 때문에 R2 라우터를 R1 라우터 앞에 배치하여 활용할 수 있다.

### Longest Prefix Match(LPM)

![Longtest-Prefix-Matching-img](/assets/img/2024-04-12-Network-4/Longest-Prefix-Matching.png)

- 라우터가 수신한 패킷의 목적지 IP 주소에 대한 가장 적합한 경로를 찾는 방법
- LPM은 라우팅 테이블에서 목적지 IP 주소와 가장 길게 일치하는 네트워크 접두사를 가진 항목을 선택함
- 즉, 가장 긴 mask부터 배치하여 위에서부터 순서대로 매칭 시켜본다.(그러다가 최초로 매칭되는 것이 가장 긴 매칭)

- 동작원리

  1. 라우터는 수신한 패킷의 목적지 IP 주소를 라우팅 테이블의 각 항목과 비교한다.
  2. 비교는 네트워크 주소와 서브넷 마스크를 사용하여 수행된다.
  3. 목적지 IP 주소와 네트워크 주소를 비트 단위로 AND 연산하여 일치 여부를 확인한다.
  4. 여러 항목이 목적지 IP 주소와 일치할 수 있기 때문에, 이 중에서 가장 길이가 긴 네트워크 접두사(서브넷 마스크)와 일치하는 항목을 선택한다.
     - 접두사 길이가 가장 긴 항목이 가장 구체적인 경로를 나타내므로, 이를 우선적으로 선택합니다.
  5. 선택된 항목의 다음 hop으로 패킷을 전송한다.

- LPM을 효율적으로 수행하기 위한 라우팅 테이블의 순서

  - 접두사 길이가 가장 긴 항목(가장 구체적인 항목)부터 시작한다.
  - 접두사 길이가 동일한 항목들은 네트워크 주소의 크기에 따라 정렬될 수 있다(선택 사항).
  - 접두사 길이가 짧아지는 순서로 항목들을 배치한다.
  - 가장 마지막에는 기본 경로(0.0.0.0/0)가 위치함.

- 번외 : 트라이 구조를 활용한 효율적인 탐색
  - 라우팅 테이블의 크기가 커질 경우, 선형 탐색은 비효율적일 수 있다.
  - 트라이(Trie) 구조를 사용하여 라우팅 테이블을 저장하면 탐색 시간을 단축할 수 있다.
  - 트라이는 각 비트 단위로 분기하는 트리 구조로, 네트워크 주소의 비트 패턴에 따라 노드를 구성한다.
  - 탐색 시에는 목적지 IP 주소의 비트를 순서대로 따라가며, 가장 깊이 일치하는 노드를 찾는다.
  - 이진 트라이(Binary Trie), 패트리샤 트라이(Patricia Trie) 등의 변형된 트라이 구조를 사용하여 메모리 사용량을 최적화할 수 있다.

### Multi-Protocol Label Switching(MPLS)

![MPLS-img](/assets/img/2024-04-12-Network-4/MPLS.png)

- 패킷 전달 성능을 향상시키고 트래픽 엔지니어링을 가능하게 하는 네트워크 기술이다.
- MPLS는 라우터에서 IP 주소 기반의 패킷 전달 방식 대신에 짧은 고정 길이의 레이블(label)을 사용하여 패킷을 전달한다.
- MPLS의 header는 Label, Exp, S, TTL로 이루어져 있다.
- Label 번호를 Global하게 synch를 맞춰서 관리해야하는 것이 불편하여 local에서 변경하고 정할 수 있도록 사용하는 방식
- 주요 동작 원리
  - 레이블 기반 전달:
    - MPLS는 패킷의 헤더에 레이블을 추가하여 전달한다.
    - 레이블은 고정 길이(일반적으로 20비트)이며, 패킷의 전달 경로와 서비스 품질(QoS) 등의 정보를 포함한다.
    - 레이블은 라우터 간 연결(link)마다 로컬로 의미를 갖는다.
  - 레이블 스위칭 라우터(LSR):
    - MPLS 네트워크에서 패킷을 전달하는 라우터를 LSR(Label Switching Router)이라고 한다.
    - LSR은 수신한 패킷의 레이블을 검사하고, 레이블 스위칭 테이블(LFIB, Label Forwarding Information Base)을 참조하여 다음 홉으로 패킷을 전달한다.
    - LSR은 패킷의 레이블을 교체(swap), 푸시(push), 또는 팝(pop)할 수 있다.
  - 레이블 분배 프로토콜(LDP):
    - MPLS는 레이블 분배 프로토콜(LDP, Label Distribution Protocol)을 사용하여 LSR 간에 레이블 정보를 교환한다.
    - LDP는 LSR 간의 레이블 매핑 정보를 동적으로 설정하고 유지 관리한다.
    - LDP는 레이블 스위칭 경로(LSP, Label Switched Path)를 설정하는 데 사용된다.
  - 트래픽 엔지니어링(TE):
    - MPLS는 트래픽 엔지니어링을 지원하여 네트워크 자원을 효율적으로 활용할 수 있다.
    - 레이블을 사용하여 패킷의 전달 경로를 명시적으로 설정할 수 있다.
    - 네트워크 관리자는 트래픽 흐름을 제어하고, 로드 밸런싱을 수행하며, 네트워크 혼잡을 완화할 수 있다.
  - 서비스 품질(QoS) 지원:
    - MPLS는 레이블에 QoS 정보를 포함할 수 있어, 차등화된 서비스 품질을 제공할 수 있다.
    - 레이블을 기반으로 패킷을 분류하고, 우선순위를 할당하며, 대역폭을 예약할 수 있다.
    - 이를 통해 실시간 트래픽이나 중요한 애플리케이션에 대한 서비스 품질을 보장할 수 있다.
- 장점
  - 탐색이 엄청 빨라짐
  - link가 깨지만 다른 Path로 복구한다.
- MPLS는 기존의 IP 라우팅과 호환되며, 다양한 네트워크 계층 프로토콜(예: IPv4, IPv6, ATM 등)과 함께 사용할 수 있다.
- MPLS는 대규모 ISP 네트워크와 기업 네트워크에서 광범위하게 사용되어 네트워크 성능 향상, 트래픽 엔지니어링, VPN(Virtual Private Network) 서비스 등을 제공한다.
  ![MPLS-Forwarding-Table-img](/assets/img/2024-04-12-Network-4/MPLS-Forwarding-Table.png)
