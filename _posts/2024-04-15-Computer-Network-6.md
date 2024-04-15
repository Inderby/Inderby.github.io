---
title: [Computer-Network 6. ICMP]
author: excelsiorKim
date: 2024-04-14 19:40:03 +0900
categories: [CS, ComputerNetwork]
tags: [CS, ComputerNetwork]
keywords: [CS, ComputerNetwork]
description: 컴퓨터 네트워크 공부 기록
toc: true
toc_sticky: true
---

# ICMP

![ICMP-Format-img](/assets/img/2024-04-15-Network-6/ICMP-Format.png)

- ICMP(Internet Control Message Protocol)는 IP(Internet Protocol) 네트워크에서 오류 보고와 진단 목적으로 사용되는 프로토콜이다.
- ICMP는 네트워크 장치 간에 제어 메시지를 전송하여 통신 문제를 식별하고 해결하는 데 도움을 준다

### Report-Type

![ICMP-Report-Type-img](/assets/img/2024-04-15-Network-6/ICMP-Report-Type.png)

### Error-Message의 Data Field

![Data-Field-img](/assets/img/2024-04-15-Network-6/Data-Field.png)

- ICMP는 IP Header의 바로 뒤에 위치하며, IP 페이로드의 첫 부분을 자신의 페이로드로 활용한다.
  - TCP Header를 직접 활용하진 않지만, IP 헤더와 그 뒤에 위치한 8byte의 데이터를 가져온다.
  - 해당 8byte는 상위 프로토콜의 헤더이다
- 예를 들어, TCP 세그먼트 전송 중에 오류가 발생하여 ICMP 메시지가 생성되는 경우:
  - ICMP 메시지는 원래 IP 패킷의 IP 헤더와 TCP 헤더의 처음 8바이트를 포함한다.
  - 이 정보는 ICMP 메시지의 페이로드에 포함되어 송신자에게 전달된다.
  - 송신자는 ICMP 메시지에 포함된 정보를 사용하여 어떤 TCP 세그먼트에서 오류가 발생했는지 식별할 수 있다.

### Destination-unreachable format

![Destination-Unreachable-Format-img](/assets/img/2024-04-15-Network-6/Destination-Unreachable-Format.png)

- 16가지의 에러 원인
  - 네트워크 도달불가(Network Unreachable) : code 0 - 목적지 네트워크로 가는 경로 없음. 목적지 주소가 라우팅 테이블에 없을 경우 및 디폴트 라우트가 없을 경우.
  - 호스트 도달불가(Host Unreachable) : code 1 - 최종 목적지 호스트에 도달할 수 없을 때(호스트 또는 라우터에서 생성됨)
  - 프로토콜 도달불가(Protocol Unreachable) : code 2 - 목적지 시스템에서 특정 프로토콜을 사용할 수 없다는 사실을 통보
  - 포트 도달불가(Port Ureachable) : code 3 - 목적지 호스트에서 특정 포트번호가 사용될 수 없음을 알림
  - 단편화 필요하지만 DF 설정됨(Fragmentation Required but DF bit is set) : code 4 – IP 데이터그램이 MTU가 작은 네트워크를 통과하려면 단편화되야 하는데, 라우터는 DF 비트가 셋팅된 것을 확인하고 그냥 폐기하고 송신측에 이를 통보
  - 목적지와의 통신이 관리적으로 금지됨 : code 13 - 어떤 이유든지 목적지가 통신을 원하지 않을 경우
    - 예를들면, 방화벽은 운용 정책에 위배되는 데이터그램을 의도적으로 폐기함. 이때, 오류메세지를 원천지에 보낼수도 아닐수도 있음
- 이 중에 code2, code3은 destination host에서만 생성될 수 있고, 그 외 에러는 router에서만 생성된다.
- 모든 문제들을 탐지하는 것은 아니라는 것에 주의

### Source-quench format

![Source-Quench-Format-img](/assets/img/2024-04-15-Network-6/Source-Quench-Format.png)

- 네트워크 혼잡이 발생했을 때 사용되는 메시지이다.
- ICMP Source Quench 메시지는 수신 호스트나 중간 라우터가 송신자에게 전송 속도를 줄이도록 요청할 때 발생한다.
- 발생 원인
  - 수신 호스트의 버퍼 오버플로우
  - 네트워크 혼잡
  - 실시간 트래픽 제어
- 하지만 현대 네트워크에서는 Source Quench 메시지의 사용이 권장되지 않는다.
  - 대신 TCP의 내장된 혼잡 제어 메커니즘과 같은 종단 간 혼잡 제어 기술이 더 널리 사용된다.
  - 때문에 많은 라우터와 호스트는 기본적으로 Source Quench 메시지 생성을 비활성화하고 있다.

### Time-Exceeded Message Format

![Time-Exceeded-Message-Format-img](/assets/img/2024-04-15-Network-6/Time-Exceeded-Message-Format.png)

- TTL이 0이 되었거나(code 0), Fragmentation된 packet들이 도착하지 않았을 경우(code1) 발생

### Parameter-Problem Message Format

![Parameter-Problem-img](/assets/img/2024-04-15-Network-6/Parameter-Problem-Message-Format.png)

- 값에 이상한 것들이 존재하는데 checksum에서 걸러지지 않은 경우
- 값을 버리면서 source에게 알려줌

### Redirection Concept

![Redirection-img](/assets/img/2024-04-15-Network-6/Redirection.png)

- 같은 네트워크에서 비효율적으로 갈 때 점진적으로 수정하게 하는 ICMP(Table Update)
  - 단, 같은 네트워크에 있는 경우만 해당

### Echo-Request, Reply Message

![Echo-Request-Reply-Message-img](/assets/img/2024-04-15-Network-6/Echo-Request-Reply-Message.png)

- IP 프로토콜이 정상적으로 동작하는지 확인할 때 사용

### Timestamp-Request, Reply

![Timestamp-format-img](/assets/img/2024-04-15-Network-6/Timestamp-message.png)

- RTT를 측정하기 위함
- source와 destination 장치의 시계들이 sync가 안맞을 때라도 보낼 수 있다.

### Trace route program

![Trace-Route-Program-img](/assets/img/2024-04-15-Network-6/Trace-Route-Program.png)

- S에서 D까지 가는데 중간에 있는 Router 들의 IP 주소를 알아옴
- RTT도 3번만에 구현함
- ICMP를 통해 구현
