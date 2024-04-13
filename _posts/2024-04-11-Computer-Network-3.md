---
title: [Computer-Network 3. IPv4 Addresses]
author: excelsiorKim
date: 2024-04-11 20:40:03 +0900
categories: [CS, ComputerNetwork]
tags: [CS, ComputerNetwork]
keywords: [CS, ComputerNetwork]
description: 컴퓨터 네트워크 공부 기록
toc: true
toc_sticky: true
---

# IPv4

- IPv4(Internet Protocol version 4)는 인터넷에서 데이터를 주고받기 위해 사용되는 네트워크 계층 프로토콜이다.
- IPv4는 1981년에 도입된 이후로 현재까지 인터넷의 핵심 프로토콜로 사용되고 있다.
- IP주소는 전세계에서 유일하다.
- 고정 IP : 시스템이 꺼졌다 켜져도 IP주소가 바뀌지 않는 것
- IPv4의 주소 고갈로 인해 128bit를 사용하는 IPv6가 나온 상태이다.

### IPv4의 주요 특징

- 32비트 주소 체계: IPv4 주소는 32비트(4바이트)로 구성되며, 점으로 구분된 4개의 10진수로 표현된다. (예: 192.168.0.1)
- 주소 클래스: IPv4 주소는 A, B, C, D, E 다섯 개의 클래스로 나뉜다. 각 클래스는 네트워크 부분과 호스트 부분의 비트 수가 다르다.
- 서브넷 마스크: 서브넷 마스크는 IP 주소의 네트워크 부분과 호스트 부분을 구분하는 데 사용된다. 이를 통해 대규모 네트워크를 더 작은 서브넷으로 나눌 수 있다.
- 라우팅: IPv4는 패킷을 목적지로 전달하기 위해 라우팅 테이블을 사용한다. 각 라우터는 패킷의 목적지 IP 주소를 기반으로 다음 홉을 결정한다.
- 헤더 구조: IPv4 패킷 헤더는 버전, 헤더 길이, 서비스 유형(Type of Service), 전체 길이, 식별자, 플래그, 단편화 오프셋, TTL(Time to Live), 프로토콜, 헤더 체크섬, 출발지 주소, 목적지 주소 등의 필드로 구성된다.
- 주소 고갈 문제: IPv4는 약 43억 개의 주소 공간을 제공하지만, 인터넷 사용의 급증으로 인해 주소가 고갈되고 있다. 이 문제를 해결하기 위해 CIDR(Classless Inter-Domain Routing)과 NAT(Network Address Translation) 등의 기술이 도입되었다.

### IPv4를 여전히 사용하는 이유

- 호환성: 대부분의 네트워크 장비와 소프트웨어가 IPv4를 기반으로 설계되어 있기 때문에, IPv6로의 전환은 많은 비용과 노력이 필요하다.
- 기술적 복잡성: IPv6는 IPv4와 비교하여 기술적으로 더 복잡하며, 네트워크 관리자들이 새로운 프로토콜을 배우고 적용하는 데 시간이 걸린다.
- 비용: IPv6 장비와 소프트웨어로 업그레이드하는 데 상당한 비용이 소요되므로, 많은 조직에서는 IPv4를 계속 사용하는 것을 선호한다.
- NAT(Network Address Translation)의 사용: IPv4 주소 고갈 문제를 해결하기 위해 NAT가 널리 사용되고 있으며, 이는 IPv4 네트워크의 수명을 연장시키는 역할을 한다.
- 인식 부족: 일부 조직과 개인은 IPv6의 필요성과 이점에 대해 충분히 인식하지 못하고 있다.
- 정부 및 산업계의 동기 부여 부족: IPv6 전환을 가속화하기 위한 정부와 산업계의 인센티브와 정책이 부족한 상황이다.

### Classful Address

![IP-Address-Class-img](/assets/img/2024-04-11-Network-3/IP-Address-Class.png)

![IP-Address-Class-2-img](/assets/img/2024-04-11-Network-3/IP-Address-Class-2.png)

![IP-Address-Class-3-img](/assets/img/2024-04-11-Network-3/IP-Address-Class-3.png)

- Class A (0.0.0.0 - 127.255.255.255):
  - 첫 번째 옥텟의 최상위 비트는 항상 0입니다.
  - 네트워크 부분은 첫 번째 옥텟(8비트)이며, 호스트 부분은 나머지 세 개의 옥텟(24비트)입니다.
  - 사용 가능한 네트워크 수는 2^7 - 2 = 126개이며, 각 네트워크는 2^24 - 2 = 16,777,214개의 호스트를 수용할 수 있습니다.
- Class B (128.0.0.0 - 191.255.255.255):
  - 첫 번째 옥텟의 최상위 두 비트는 항상 10입니다.
  - 네트워크 부분은 첫 번째와 두 번째 옥텟(16비트)이며, 호스트 부분은 나머지 두 개의 옥텟(16비트)입니다.
  - 사용 가능한 네트워크 수는 2^14 = 16,384개이며, 각 네트워크는 2^16 - 2 = 65,534개의 호스트를 수용할 수 있습니다.
- Class C (192.0.0.0 - 223.255.255.255):
  - 첫 번째 옥텟의 최상위 세 비트는 항상 110입니다.
  - 네트워크 부분은 첫 번째, 두 번째, 세 번째 옥텟(24비트)이며, 호스트 부분은 마지막 옥텟(8비트)입니다.
  - 사용 가능한 네트워크 수는 2^21 = 2,097,152개이며, 각 네트워크는 2^8 - 2 = 254개의 호스트를 수용할 수 있습니다.
- Class D (224.0.0.0 - 239.255.255.255):
  - 첫 번째 옥텟의 최상위 네 비트는 항상 1110입니다.
  - Class D는 멀티캐스트 주소에 사용됩니다.
- Class E (240.0.0.0 - 255.255.255.255):
  - 첫 번째 옥텟의 최상위 네 비트는 항상 1111입니다.
  - Class E는 실험 및 연구 목적으로 예약되어 있습니다.

![IP-Address-img](/assets/img/2024-04-11-Network-3/IP-Example.png)

- 위의 사진에서는 180.8.x.x는 일정한 구간의 IP주소를 관리하는 영역이 존재한다.
- 해당 주소의 180.8.0.0(시작 주소)와 180.8.255.255(마지막 주소)는 특별한 주소이다.

### 서로 다른 network가 연결 됐을 때

![Internet-Sample-img](/assets/img/2024-04-11-Network-3/Internet-Sample.png)

### Network Address

![Network-Address-img](/assets/img/2024-04-11-Network-3/Network-Address.png)

- Network Mask
  ![Network-Mask](/assets/img/2024-04-11-Network-3/Network-Mask.png)

- 네트워크 마스크(서브넷 마스크) : 네트워크 부분과 호스트 부분을 구분하는 데 사용되는 32비트 숫자이다.
  - 마스크의 1로 설정된 비트는 네트워크 부분을, 0으로 설정된 비트는 호스트 부분을 나타낸다.

### Network 관리 방식

![Network-Management-img](/assets/img/2024-04-11-Network-3/Network-management.png)

- 라우터를 통해 각각의 서브넷에 할당된 IP주소 영역을 기준으로 데이터를 전송한다.
- 아래는 위의 네트워크에서 각각의 서브넷이 어떻게 니뉘어져있는지 bit로 표현한 것이다.
  ![Network-Subnet-img](/assets/img/2024-04-11-Network-3/Network-Subnet.png)

### 네트워크 마스트 vs 서브넷 마스크

![Network-Mask-and-Subnet-Mask-img](/assets/img/2024-04-11-Network-3/Network-Mask-and-Subnetwork-Mask.png)

### Classless Address

![Classless-Address-img](/assets/img/2024-04-11-Network-3/Classless-Address.png)

- Classless Addressing(클래스리스 주소 지정)은 IP 주소 할당 및 라우팅에 사용되는 현대적인 방법이다.
- 이전의 Classful Addressing(클래스 기반 주소 지정)의 단점을 극복하기 위해 도입되었다.

- 2^n = block 갯수, 2^(32-n) = 사용할 수 있는 IP 주소 갯수

- ex 1 : 167.199.170.82/27 일 경우
  - mask : 255.255.255.224(11100000)
  - mask와 IP 주소를 and 연산했을 때 : 167.199.170.64(01000000)-해당 서브넷의 시작 주소
  - 167.199.170.95(01011111) - last 주소
- ex 2 : 1000개의 주소를 사오려는 회사가 있을 때
  - 2^10 = 1024 -> 32 - n = 10 이 되어야 함. 그러므로 n = 22
  - 즉 18.14.12.0/22 라는 주소를 사오는 것이 적절하다.
- ex 3 : 어떤 회사가 130.34.12.64/26 라는 주소를 사왔고, 4개의 subnet을 만들려 할 때
  - 2^2 = 4 이기 때문에 26 + 2 = 8로 서브넷 마스크를 만들어 아래와 같은 서브넷을 만들 수 있다
  - 130.34.12.(01000000) -> 130.34.12.64/28
  - 130.34.12.(01010000) -> 130.34.12.80/28
  - 130.34.12.(01100000) -> 130.34.12.96/28
  - 130.34.12.(01110000) -> 130.34.12.112/28

### 크기가 다른 subnet을 만드는 경우

- ex 1 : 14.24.74.0/24 의 시작 주소를 가진 조직에서 sub block 120개, 60개, 10개인 3개의 서브넷을 만들려고 할 때.

  - 아래의 사진과 같이 큰것부터 작은 것 순서대로 진행해야됨. 그렇지 않으면 subnet ID가 바뀔 수 있다.
    ![Subnet-Example-1-img](/assets/img/2024-04-11-Network-3/Subnet-example-1.png)

- ex 2 : 70.12.100.128/26의 주소를 가진 조직에서 sub block 32개, 16개, 16개인 3개의 서브넷을 만들려고 할 때.
  ![Subnet-Example-2-img](/assets/img/2024-04-11-Network-3/Subnet-example-2.png)

- ex 3
  ![Sub-Group-img](/assets/img/2024-04-11-Network-3/SubGroup.png)
  - First Group : 2^6 \* 2^8 = 2^14
  - Second Group : 2^7 \* 2^7 = 2^14
  - Third Group : 2^7 \* 2^6 = 2^13
  - step 1 : group 별로 구분하기
    ![First-Step-img](/assets/img/2024-04-11-Network-3/first-step.png)
  - step 2 : group에서 subnet 분리하기
    ![Second-Step-img](/assets/img/2024-04-11-Network-3/second-step.png)

## Special Address

- IP 주소 중에는 특별한 용도로 사용되는 Special Address가 있다. 이러한 주소들은 일반적인 호스트 주소로 사용되지 않고, 네트워크 관리, 진단, 멀티캐스트 등의 목적으로 예약되어 있다.

### All-Zero Address

![All-Zero-Address-img](/assets/img/2024-04-11-Network-3/All-Zero-Address.png)

- 라우팅 테이블에서 0.0.0.0/0은 기본 경로를 나타내기도 한다.
- 혹은 DHCP클라이언트가 IP주소를 요청할 때 0.0.0.0을 출발지 주소로 사용한다.
  - 이는 클라이언트가 아직 자신의 IP 주소를 모르기 때문이다

### Broadcast Address

![Broadcast-Address-img](/assets/img/2024-04-11-Network-3/Broadcast-Address.png)

- 서브넷의 마지막 주소로, 해당 네트워크의 모든 호스트에게 데이터를 전송하는 데 사용된다.
  - 혹은 사진과 같이 IP주소의 모든 bit를 1로 설정하는 방식도 있다. 어차피 라우터에서 외부 인터넷으로 전송되지 않게 제한이 걸림
- 호스트 부분이 모두 1로 설정됨. (예: 192.168.1.255/24)
- 네트워크 내의 모든 호스트에게 동시에 메시지를 보낼 때 사용됩니다.

### Loop Back Address

![Loop-Back-Address-img](/assets/img/2024-04-11-Network-3/Loop-Back-Address.png)

- 127.0.0.0/8 범위의 주소로, 호스트 자신을 가리키는 데 사용된다.
- 가장 일반적으로 사용되는 루프백 주소는 127.0.0.1이다.
- 네트워크 연결 없이 자신의 컴퓨터에서 네트워크 서비스를 테스트할 때 유용하다.

### Link-Local Address

- 169.254.0.0/16 범위의 주소로, 자동 IP 주소 설정에 사용된다. (APIPA)
- DHCP 서버로부터 IP 주소를 받을 수 없을 때, 호스트가 자동으로 이 범위에서 주소를 할당한다.
- 동일한 링크(로컬 네트워크) 내에서만 통신할 수 있다.

### Multicast Address

- 224.0.0.0 ~ 239.255.255.255 범위의 주소로, 그룹 통신에 사용된다.
- 멀티캐스트 그룹에 가입한 호스트들에게 동시에 데이터를 전송할 때 사용된다.
- 예를 들어, 224.0.0.1은 모든 호스트를 나타내는 멀티캐스트 주소이다.

### NAT

![NAT-img](/assets/img/2024-04-11-Network-3/NAT.png)

- NAT(Network Address Translation)는 IP 네트워크에서 주소 변환을 수행하는 기술이다.
- NAT는 주로 사설 IP 주소를 공인 IP 주소로 변환하여 인터넷과 통신할 수 있게 해주는 역할을 한다.
- 이를 통해 제한된 공인 IP 주소를 효율적으로 사용할 수 있으며, 네트워크 보안도 향상시킬 수 있다.
- 주요 기능
  - 주소 변환
    - NAT는 사설 IP 주소를 공인 IP 주소로 변환하여 인터넷과 통신할 수 있게 해준다.
    - 일반적으로 라우터나 방화벽에서 NAT를 수행한다.
    - 사설 네트워크에서 나가는 패킷의 출발지 주소를 공인 IP 주소로 변환하고, 돌아오는 패킷은 다시 사설 IP 주소로 변환한다.
  - 주소 절약:
    - NAT를 사용하면 하나의 공인 IP 주소를 여러 개의 사설 IP 주소가 공유할 수 있다.
    - 이를 통해 제한된 공인 IP 주소를 효율적으로 사용할 수 있다.
    - 특히 IPv4 주소 고갈 문제를 완화하는 데 도움이 된다.
  - 보안 강화:
    - NAT는 사설 네트워크의 내부 구조와 주소를 외부에 노출하지 않는다.
    - 외부에서는 NAT 디바이스의 공인 IP 주소만 볼 수 있으므로, 내부 네트워크를 직접 공격하기 어려워진다.
    - 이는 네트워크 보안을 강화하는 데 도움이 된다.
  - 유연한 네트워크 설계:
    - NAT를 사용하면 사설 네트워크의 IP 주소 체계를 자유롭게 설계할 수 있다.
    - 공인 IP 주소와 충돌하지 않는 한, 사설 네트워크 내에서 원하는 대로 IP 주소를 할당할 수 있다.
    - 이는 네트워크 관리와 확장을 용이하게 해준다.
  - NAT의 유형
    - SNAT(Source NAT)는 사설 네트워크에서 나가는 패킷의 출발지 주소를 변환하는 것이다.
    - DNAT(Destination NAT)는 들어오는 패킷의 목적지 주소를 변환하는 것이다.
    - PAT(Port Address Translation)는 포트 번호를 이용하여 여러 사설 IP 주소를 하나의 공인 IP 주소로 변환하는 방식이다.
