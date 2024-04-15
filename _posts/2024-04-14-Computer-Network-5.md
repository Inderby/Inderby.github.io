---
title: [Computer-Network 5. IPv4]
author: excelsiorKim
date: 2024-04-14 20:40:03 +0900
categories: [CS, ComputerNetwork]
tags: [CS, ComputerNetwork]
keywords: [CS, ComputerNetwork]
description: 컴퓨터 네트워크 공부 기록
toc: true
toc_sticky: true
---

### IP Datagram

![IP-Datagram-img](/assets/img/2024-04-14-Network-5/IP-Datagram.png)

- Version (4 bits):
  - IP 프로토콜의 버전을 나타낸다 (예: IPv4 또는 IPv6).
- Internet Header Length (IHL) (4 bits):
  - IP 헤더의 길이를 32비트 단위로 나타낸다.
  - 헤더 길이는 최소 20바이트(5 x 32비트)부터 최대 60바이트(15 x 32비트)까지 가능하다.
- Differentiated Services Code Point (DSCP) (6 bits):
  - QoS(Quality of Service)를 위해 사용되는 필드이다.
  - 패킷의 우선순위와 혼잡 제어 정책을 결정하는 데 사용된다.
- Explicit Congestion Notification (ECN) (2 bits):
  - 네트워크 혼잡 상태를 알리는 데 사용되는 필드이다.
  - 라우터가 혼잡을 감지하면 이 필드를 설정하여 송신자에게 혼잡 상태를 알린다.
- Total Length (16 bits):
  - IP 데이터그램의 전체 길이를 바이트 단위로 나타낸다.
  - 헤더와 페이로드를 포함한 전체 길이로, 최대 65,535바이트까지 가능하다.
- Identification (16 bits):
  - 단편화된 패킷을 식별하기 위한 필드이다.
  - 단편화된 패킷들은 동일한 Identification 값을 가진다.
  - 재조합 목적으로 사용한다.
- Flags (3 bits):
  - 단편화와 관련된 플래그 비트이다.
  - 첫 번째 비트는 예약되어 사용되지 않는다.
  - 두 번째 비트(DF, Don't Fragment)는 단편화 허용 여부를 나타낸다.
  - 세 번째 비트(MF, More Fragments)는 더 많은 단편화 패킷이 있는지 나타낸다.(즉, 뒤에 분할된 Packet들이 더 있다는 뜻)
- Fragment Offset (13 bits):
  - 단편화된 패킷의 상대적인 위치를 나타내는 필드이다.
  - 8바이트 단위로 표현되며, 원래 패킷에서의 오프셋을 나타낸다.
- Time to Live (TTL) (8 bits):
  - 패킷의 수명을 나타내는 필드이다.
  - 패킷이 네트워크에서 무한히 순환하는 것을 방지하기 위해 사용된다.
  - 패킷이 라우터를 통과할 때마다 TTL 값이 1씩 감소하며, 0이 되면 패킷은 폐기된다.
- Protocol (8 bits):
  - 상위 계층 프로토콜(예: TCP, UDP, ICMP 등)을 나타내는 필드이다.
  - 목적지에서 패킷을 적절한 프로토콜로 전달하는 데 사용된다.
- Header Checksum (16 bits):
  - IP 헤더의 무결성을 검사하기 위한 필드이다.
  - 헤더의 오류를 감지하고 폐기하는 데 사용된다.
- Source IP Address (32 bits):
  - 송신자의 IP 주소를 나타낸다.
- Destination IP Address (32 bits):
  - 수신자의 IP 주소를 나타낸다.
- Options (가변 길이):
  - 추가적인 옵션 정보를 포함할 수 있는 필드이다.
  - 옵션의 길이와 내용은 가변적이며, 필요에 따라 사용된다.
- Padding (가변 길이):
  - 헤더의 길이를 32비트의 배수로 맞추기 위한 필드이다.
  - 옵션 필드의 길이에 따라 패딩이 추가된다.

### Frame

![Frame-img](/assets/img/2024-04-14-Network-5/Frame.png)

- Datagram은 Frame에서 보는 Payload 부분이다
- 만약 Frame의 data 부분이 46byte이라하면 이를 맞춰주기 위한 Padding이 붙는다.
- 예시 : IP header(20 byte) + TCP header(20 byte) + Data(1 byte)일 경우 총 41byte로 46byte를 맞추지 못하는 경우가 발생한다. 이를 위해 5byte의 padding을 붙여준다.
  - 어차피 실제 데이터의 크기는 total length를 알려주는 헤더 부분이 존재한다.

### Fragmentation

- 데이터그램의 단편화(Fragmentation)는 네트워크에서 전송할 수 있는 최대 크기인 MTU(Maximum Transmission Unit)보다 큰 패킷을 작은 조각으로 나누는 과정이다.
- 이때 Header는 분할되지 않고 data 부분만 분할된다

- 동작 과정

  1. 패킷 크기 확인:
     - 라우터는 수신한 IP 데이터그램의 크기를 확인한다.
     - 데이터그램의 크기가 출력 링크의 MTU보다 크면 단편화가 필요하다.
  2. 단편화 수행:
     - 라우터는 원래의 IP 데이터그램을 MTU에 맞는 작은 조각으로 나눈다.
     - 각 조각은 원래 데이터그램의 일부 데이터를 포함한다.
     - 각 조각은 자신만의 IP 헤더를 가지며, 원래 데이터그램의 헤더 정보를 상속받는다.
  3. 헤더 필드 설정:
     - 각 조각의 IP 헤더에는 단편화 관련 필드가 설정된다.
     - Identification 필드: 원래 데이터그램의 식별자와 동일한 값으로 설정된다.
     - Flags 필드: 마지막 조각을 제외한 모든 조각에는 MF(More Fragments) 비트가 설정된다.
     - Fragment Offset 필드: 원래 데이터그램에서의 조각의 상대적인 위치를 8바이트 단위로 나타낸다.
  4. 조각 전송:
     - 라우터는 생성된 각 조각을 독립적인 IP 데이터그램으로 취급하여 전송한다.
     - 각 조각은 네트워크를 통해 개별적으로 라우팅되며, 서로 다른 경로를 통해 전송될 수 있다.
  5. 조각 재조립:
     - 목적지 호스트는 수신한 조각들을 원래 데이터그램으로 재조립한다.
     - 조각들은 Identification 필드를 기준으로 그룹화되며, Fragment Offset을 사용하여 원래 순서대로 배치된다.
     - 모든 조각이 도착하면 원래의 IP 데이터그램이 복원되며, 상위 계층 프로토콜로 전달된다.

- 예제
  ![Fragmentation-Example-img](/assets/img/2024-04-14-Network-5/Fragmentation-Example.png)
  - MTU : 1420 = 1400 + 20(header size)인 상황
  - 13bit로 16bit를 표현하기 위해 8을 곱하거나 나눔
    ![Detailed-Fragmentation-Example-img](/assets/img/2024-04-14-Network-5/Detail-Fragmentation-Example.png)
    - Fragment1을 보면 Frag bit에 1을 표시함으로써 뒤에 단편화된 데이터가 더있음을 알림.
    - Fragment2의 경우 다른 network로 갔을 때 MTU가 1420보다 작기 때문에 한번 더 분할된 모습을 볼 수 있다.

### Option

- Option Format
  ![Option-img](/assets/img/2024-04-14-Network-5/Option.png)
- categories of Option
  ![Categories-of-Option-img](/assets/img/2024-04-14-Network-5/Categories-of-Option.png)

![End-Op=img](/assets/img/2024-04-14-Network-5/End-Op.png)

- End of Option List (EOL):
  - 옵션 타입: 0
  - 길이: 1 바이트
  - 설명: 옵션 리스트의 끝을 나타내는 데 사용됩니다. 더 이상 옵션이 없음을 의미한다.

![No-Op-img](/assets/img/2024-04-14-Network-5/No-Op.png)

- No Operation (NOP):
  - 옵션 타입: 1
  - 길이: 1 바이트
  - 설명: 옵션 리스트에서 사용되지 않는 공간을 채우기 위한 옵션이다. 주로 패딩 목적으로 사용된다.

![Record-Route-img](/assets/img/2024-04-14-Network-5/Record-Route-Option.png)
![Record-Route-Example-img](/assets/img/2024-04-14-Network-5/Record-Route-Option-Example.png)

- Record Route (RR):
  - 옵션 타입: 7
  - 길이: 가변 (최대 40 바이트)
  - 설명: 패킷이 지나는 경로상의 라우터 IP 주소를 기록하는 옵션이다. 각 라우터는 자신의 나가는쪽 IP 주소를 옵션 필드에 추가한다.

![Loose-Source-img](/assets/img/2024-04-14-Network-5/Loose-Source-Route.png)

- Loose Source and Record Route (LSRR):
  - 옵션 타입: 131
  - 길이: 가변 (최대 40 바이트)
  - 설명: 패킷이 지정된 경로를 느슨하게 따르도록 하는 옵션이다. 지정된 IP 주소를 통과해야 하지만, 중간에 다른 라우터를 경유할 수 있다.

![Strict-Source-img](/assets/img/2024-04-14-Network-5/Strict-Source-Route.png)
![Strict-Source-Example-img](/assets/img/2024-04-14-Network-5/Strict-Source-Route-Example.png)

- Strict Source and Record Route (SSRR):
  - 옵션 타입: 137
  - 길이: 가변 (최대 40 바이트)
  - 설명: 패킷이 지정된 경로를 엄격하게 따르도록 하는 옵션이다. 지정된 IP 주소만을 통과해야 하며, 중간에 다른 라우터를 경유할 수 없다.
  - 지정된 경로가 안리마ㅕㄴ packet을 버림(순서도 지켜야함)

![TimeStamp-img](/assets/img/2024-04-14-Network-5/Timestamp-Option.png)
![TimeStamp-Flag-img](/assets/img/2024-04-14-Network-5/TImeStamp-Flag-Option.png)
![TimeStamp-Option-Example-img](/assets/img/2024-04-14-Network-5/Timestamp-Example.png)

- Timestamp:
  - 옵션 타입: 68
  - 길이: 가변 (최대 40 바이트)
  - 설명: 패킷이 지나는 경로상의 라우터에서 타임스탬프를 기록하는 옵션이다. 각 라우터는 자신의 시간 정보를 옵션 필드에 추가한다.
- Router Alert:
  - 옵션 타입: 148
  - 길이: 4 바이트
  - 설명: 중간 라우터에게 패킷을 특별히 처리하도록 알리는 옵션이다. 주로 RSVP(Resource Reservation Protocol)나 IGMP(Internet Group Management Protocol)에서 사용된다.

### IP Components

![IP-Components-img](/assets/img/2024-04-14-Network-5/IP-Component.png)

- 위의 이미지는 상위 계층에서 하위 계층으로 전달되는 과정과 하위 계층에서 상위 계층으로 전달되는 과정을 나타내는 다이어그램이다.
- IP: 인터넷 프로토콜로, 패킷의 주소 지정과 라우팅을 담당한다.
- Header-adding module: IP 패킷에 헤더를 추가하는 모듈입니다. 상위 계층에서 받은 데이터에 IP 헤더를 붙여 IP 패킷을 생성한다.
- Processing module: IP 패킷을 처리하는 모듈로, 라우팅 테이블을 참조하여 패킷의 다음 목적지를 결정한다.
- Fragmentation module: IP 패킷의 크기가 MTU(Maximum Transmission Unit)보다 클 경우, 패킷을 작은 조각으로 분할하는 모듈이다.
- Reassembly module: 분할된 IP 패킷 조각들을 원래의 패킷으로 재조립하는 모듈이다.
- Forwarding module: 라우팅 테이블을 기반으로 IP 패킷을 적절한 출력 인터페이스로 전달하는 모듈이다.
- Routing table: 네트워크 토폴로지 정보와 경로 정보를 저장하는 테이블로, IP 패킷의 다음 홉을 결정하는 데 사용된다.
- Reassembly table: 분할된 IP 패킷 조각들을 추적하고 재조립하는 데 필요한 정보를 저장하는 테이블이다.
- MTU table: 각 인터페이스의 MTU 값을 저장하는 테이블로, 패킷 분할 여부를 결정하는 데 사용된다.

### Reassembly Table

![Resassembly-Table-img](/assets/img/2024-04-14-Network-5/Reassembly-Table.png)

- Fragmentation된 Packet 들을 재조합.
- offset으로 순서 파악 후, linked list로 관리 한다.
- packet이 다 도착할 때 까지 기다리다가 time-out되면 다버림(network layer는 재전송 및 packet-loss-detect 기능이 없음)
