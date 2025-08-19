---
title: [Computer-Network 2. Transmission Control Protocol]
author: excelsiorKim
date: 2024-04-02 20:40:03 +0900
categories: [CS, ComputerNetwork]
tags: [CS, ComputerNetwork]
keywords: [CS, ComputerNetwork]
description: 컴퓨터 네트워크 공부 기록
toc: true
toc_sticky: true
---

### TCP(전송 제어 프로토콜)

- 컴퓨터 네트워크에서 널리 사용되는 프로토콜로, 서로 다른 장치에서 실행되는 응용 프로그램 간에 안정적이고, 순서가 있으며 오류가 확인된 데이터 전송 스트림을 제공한다.
- TCP는 데이터 전송이 시작되기 전에 두 장치 사이에 가상 회선을 설정하는 **연결 지향 프로토콜**이다.
- TCP는 **3 way handshake** 라는 프로세를 사용하여 두 장치의 연결을 설정

### 3 way hand shake

![3-way-handshake-img](/assets/img/2024-04-02-Network-2/TCP-connection-1.png)

1. 첫 번째 장치는 SYN(Synchronize Sequence Numbers) 메시지를 두 번째 장치로 전송하여 연결을 시작하려고 함을 나타낸다. 이 세그먼트에는 클라이언트의 초기 시퀀스 번호(ISN)가 포함된다.
2. 두 번째 장치는 SYN-ACK(Acknowledgement) 메시지로 응답하여 SYN 메시지를 수신 했으며, 연결을 설정할 의향이 있음을 나타낸다. 이때 자신의 ISN을 포함한다.
   - 또한 서버는 클라이언트의 ISN에 1을 더한 값을 ACK 필드에 포함시켜 클라이언트 ISN을 확인한다.
3. 첫 번째 장치는 서버의 ISN에 1을 더한 값을 ACK(승인)메시지를 두번째 장치로 다시 전송하여 연결이 설정되었음을 나타낸다.

- 해당 연결 설정 시 갖는 이점
  - 양방향 통신을 위한 동기화가 이루어진다.
  - 연결 설정을 위한 시퀀스 번호 교환이 이루어진다.
  - 이전 연결의 중복 세그먼트를 방지할 수 있다.
  - 신뢰성 있는 전이중 데이터 전송 연결을 확립할 수 있다.

### TCP를 사용하는 Well-known Ports

![well-known-port](/assets/img/2024-04-02-Network-2/well-known-port.png)

- Well-Known Port : 0 ~ 1023 번호의 예약된 port. 특정 프로토콜이나 서비스에 대해 미리 할당된 포트번호이다.

### Stream delivery

![Stream-delivery](/assets/img/2024-04-02-Network-2/Stream-delivery.png)

- 명시적인 메시지 경계 없이 두 애플리케이션 간의 지속적인 데이터 흐름을 말한다.
- 스트림 전송에서 네트워크를 통해 전송할 수 있는 작은 세그먼트(패킷)로 분할한 뒤 boundary 없이 (개별 메시지 또는 패킷 간의 구분 없이) 바이트 연속으로 송수신한다.
- 그렇기 때문에 수신자가 들어오는 스트림 구분을 분석하고 특정 형식의 논리 또는 프로토콜을 사용하여 메시지 경계를 결정하고, 스트림에서 개발 메시지를 분리해야함을 의미한다.
- Sender는 데이터를 보내고 나서도 Buffer에 카피본을 갖고 있음(안 보내졌을 경우 다시 보내기 위함). 상대방이 받은 것을 확인하면 삭제
- Receiver는 Application으로 데이터가 보내지면 Buffer에서 지움.
- TCP 에서 사용

### Boundary delivery

- 각 메시지 사이에 명확한 경계가 있는 별개의 독립적인 메시지 또는 패킷으로 데이터를 전송하는 것을 말함.
- 각 메시지에 대한 길잉 및 기타 관련 정보를 지정하는 헤더와 함께 별도 단위로 전송
- 수신자는 연속적인 바이트 스트림을 구문 분석할 필요없이 각 메시지를 독립적인 단위로 간단히 읽을 수 있다.
- UDP에서 활용

### Numbering in TCP

- TCP의 모든 데이터들은 numbering 됨. 시작 숫자는 random으로 지정.
  - ex : 5000 byte를 전송하는 상황에서 세그먼트 크기는 1000 byte, 시작 번호 10001번인 경우
  - ![numbering-tcp](/assets/img/2024-04-02-Network-2/numbering-tcp.png)

### Segment

![segment-img](/assets/img/2024-04-02-Network-2/segment.png)

- Source port(16 bit) : 패킷을 보내는데 사용되는 발신자 장치의 포트 번호를 식별
- Destination port(16 bit) : 패킷이 전송되는 수신기 장치의 포트 번호를 식별
- Sequence number(32bit) : 전송되는 전체 데이터 스트림에서 현재 패킷의 첫번째 바이트 위치를 식별하는 번호를 포함.
- Acknowledgement number(32 bit) : 수신자가 송신자로부터 수신할 것으로 예상되는 다음 시퀀스 번호가 포함.
- 데이터 오프셋(HLEN, 4 bit) : 32bit 단어로 TCP 헤더의 길이 지정. 패킷에서 데이터가 시작되는 위치를 나타내는데 사용됨. (4 bit의 최대 표현은 15이므로 20 ~ 60 byte인 헤더의 길이를 표현하기 위해 HLEN값 \* 4를 하여 보낸다. 받을 때는 / 4를 하여 저장한다.)
  - ex : 20byte 헤더의 길이 -> HLEN : 5
- Reserved(6bit) : 향후 사용을 위해 예약되어 있으며, 0으로 설정
- Control bit(6bit) : 패킷의 동작을 제어하는 여러 플래그가 포함. SYN, ACK, FIN, RST 등의 플래그가 포함됨.
- Window Size(16bit) : 수신자로부터 승인을 기대하기 전에 송신자가 수신하고자 하는 데이터의 양을 byte 단위로 지정
- Check sum(16 bit) : 전체 TCP 헤더 및 데이터 필드에 대해 계산되는 Check sum 값이 포함. 전송중에 패킷에 오류를 감지하는데 사용됨
- Urgent pointer(16 bit) : 패킷 내의 긴급 데이터의 위치를 나타내는데 사용
- Option(가변 길이) : 패킷에 포함될 수 있는 선택적인 정보가 포함

### Encapsulation

![Encapsulation](/assets/img/2024-04-02-Network-2/Encapsulation.png)

- TCP payload(transport layer) : TCP에 있는 checksum은 head와 data 둘 다 검사
- IP payload(Network layer) : checksum으로 header만 검사
- Data-link layer payload : CRC가 붙음
  - CRC : 데이터 링크 레이어에서 데이터 전송오류를 감지하는데 사용되는 메커니즘. 수신자와 송신자의 CRC가 일치하지 않으면 오류로 간주한다.
    <br>
- header check sum : 헤더에 있는 모든 16비트 단어를 더한 다음 합계의 보수를 취하여 계산되는 16bit 값이다. 후에 송신자와 수신자의 체크섬 값과 비교하여 일치하지 않으면 오류가 발생한 것으로 간주한다.

### Control field

![control-field-img](/assets/img/2024-04-02-Network-2/control-field.png)

- URG : Urgent Pointer 필드가 유효하고 긴급 데이터가 패킷에 있음을 나타내는데 사용
- ACK : Acknowledgement number 필드가 유효하고 패킷이 이전에 수신한 패킷에 대한 승인임을 나타내는데 사용
- PSH : 수신 장치에 버퍼가 가득찰 때까지 기다리지 않고 즉시 응용 프로그램 계층으로 데이터를 push 해야함을 나타내는데 사용 (대화형 응용프로그램, 작은 데이터 전송, 버퍼 비우기, 마지막 데이터 전송 등에 사용)
- RST : 연결을 재설정하고 전송 중인 모든 데이터를 폐기해야 함을 나타내는데 사용
- SYN : 새로운 TCP 연결을 시작하는데 사용. 각 패킷을 식별하는데 사용하는 Sequence number를 동기화하기 위해 두 장치 간의 초기 handshake에 사용
- FIN : 송신자가 더이상 보낼 데이터가 없고 연결을 닫아야 함을 나타내는데 사용

### Cumulative ACK (TCP의 방식)

- 수신자는 발신자가 보내야하는 다음 예상 데이터 시퀀스 번호를 나타내는 ACK 번호와 함께 ACK을 보냄. 발신자의 packet이 사라질 경우 해당 ACK 넘버를 계속 받을 것으로 예상하고 ACK을 보낸다.
- 장점 : 효율적인 전송, 높은 신뢰성, 네트워크 혼잡 방지, 빠른 데이터 전송
- 단점 : 지연 문제(수신측에서 모든 패킷을 받아야 하므로, 모든 패킷이 수신될 때까지 대기 해야됨), 불필요한 재전송(일부 패킷이 재전송되어도 모든 패킷을 재전송해야 할 수 있음), 스트리밍의 지원이 어려움

### 함수 관점의 3-way handshake

![3-way-handshake-function-img](/assets//img/2024-04-02-Network-2/3-way-handshake-function.png)

- socket function : 새로운 소켓을 생성하는데 사용. 도메인(IPv4, IPv6, 또는 로컬 통신), 소켓 유형(스트림 지향, 또는 데이터그램 지향) 및 프로토콜의 세가지 매개변수 사용. socket의 고유 식별자인 socket descriptor를 반환함.
- bind function : 소켓을 특정 IP 주소 및 포트 번호와 연결하는데 사용. socket descriptor, IP주소와 port 번호를 포함한 구조체 정보, 구조체의 크기 등을 매개 변수로 사용. (해당 기능은 일반적으로 서버에서 특정 port에 바인딩하고 들어오는 연결을 수신하는데 사용)
- listen function : 연결된 소켓을 Listen 상태로 전환하여 들어오는 연결을 허용하는데 사용
- accept function : 서버에서 클라이언트로부터 들어오는 연결을 수락할 때 사용. block 함수. (SYN + ACK에 대한 client의 ACK이 와야 return)
- connect function : 클라이언트가 원격 서버에 대한 연결을 설정하는데 사용. block 함수이다.(client가 보낸 SYN에 대한 SYN + ACK이 와야 return)

### 종료 과정의 4 way-handshake

![Termination-3-way-handshake](/assets/img/2024-04-02-Network-2/termination-3-way-handshake.png)

1. 클라이언트가 FIN 패킷을 전송

- 클라이언트가 더 이상 데이터를 보낼 것이 없음을 알리기 위해 서버에 FIN 패킷을 보낸다.
- 클라이언트는 **FIN-WAIT-1** 상태가 되어 서버의 확인을 기다린다.

2. 서버가 FIN 패킷에 대해 확인(ACK)
   - 서버는 FIN 패킷을 받고 클라이언트에게 확인(ACK) 패킷을 보낸다.
   - 서버는 **CLOSE-WAIT** 상태가 되어 연결 종료 준비를 한다.
3. 서버가 FIN 패킷을 전송
   - 서버가 모든 데이터를 보낸 후에는 클라이언트에게 FIN 패킷을 보내 종료과정을 시작한다.
   - 서버는 **LAST-ACK** 상태가 되어 클라이언트의 확인을 기다린다.
4. 클라이언트가 서버의 FIN에 대해 확인(ACK)
   - 클라이언트는 서버의 FIN 패킷에 대해 확인(ACK)패킷을 보낸다.
   - 클라이언트는 **TIME-WAIT** 상태가 되어 지정된 시간(일반적으로 최대 세그먼트 수명의 2배)동안 기다린다. 이는 서버로부터 지연된 패킷을 받고 확인하여 연결이 완전히 종료되기 전에 처리하기 위함이다.
   - 서버는 ACK 패킷을 받고 연결을 종료하여 **CLOSED** 상태가 된다.

- **TIME-WAIT** 기간이 지나면 클라이언트도 **CLOSED** 상태가 되어 TCP 연결이 완전히 종료 된다.
- Time-Wait이 필요한 이유
  1. 지연된 패킷 처리 : 네트워크 환경에서 패킷 지연이나 패킷 손실이 발생할 수 있다. TIME-WAIT 상태는 연결이 종료된 후에도 지연된 패킷이 도착할 가능성을 고려한다. 이 상태에서 클라이언트는 지연된 마지막 패킷을 수신하고 확인 응답을 보낼 수 있다. 이를 통해 지연된 패킷이 새 연결에 영향을 미치는 것을 방지할 수 있다.
  2. 동일 소켓 재사용 방지 : TIME-WAIT 기간 동안 해당 소켓은 사용할 수 없다. 이는 동일한 소켓 쌍(IP 주소와 포트 번호 조합)이 새로운 연결에 사용되는 것을 일시적으로 막아준다. 이렇게 하면 이전 연결의 지연된 패킷이 새 연결에 영향을 미치지 않는다.
  3. 신뢰성 있는 연결 종료 : TIME-WAIT 상태는 클라이언트와 서버 양쪽에서 연결 종료 프로세스를 완료할 수 있도록 보장한다. 이 기간 동안 양측은 모든 데이터를 주고받고, 확인 메시지를 교환하여 안전하게 연결을 종료할 수 있다.
  4. 대기 시간 제공 : TIME-WAIT 상태에서는 일정 시간(통상 최대 세그먼트 수명의 2배) 동안 기다리게 된다. 이 대기 시간은 지연된 패킷이 도착할 가능성을 줄이고, 이전 연결의 잔여 패킷을 처리할 수 있는 기회를 제공한다.

### Deny a connection

![Denying-a-Connection](/assets/img/2024-04-02-Network-2/Deny-connection.png)

### Abort a connection

![Aborting-a-Connection](/assets/img/2024-04-02-Network-2/Aborting-connection.png)

### State transition diagram

- TCP 프로토콜 다이어그램

![State-transition-diagram](/assets/img/2024-04-02-Network-2/State-Transition-diagram.png)

- State for TCP
  ![State-for-TCP](/assets/img/2024-04-02-Network-2/State-of-TCP.png)

- Time-line diagram for TCP
  ![Time-line-for-TCP](/assets/img/2024-04-02-Network-2/Time-line-diagram-TCP.png)

# Windows in TCP

- Sliding Window 방식을 활용한다.
- TCP는 데이터 전송의 각 방향에 대해 두 개의 윈도우(송신 윈도우와 수신 윈도우)를 사용한다. 즉, 양방향 통신에는 총 4개의 윈도우가 사용된다.

### Sending window in TCP

![Sending-window-in-TCP](/assets/img/2024-04-02-Network-2/Sending-window-in-TCP.png)

- 송신 윈도우(Send Window) :
  - 송신측에서 사용하는 윈도우로, 송신할 수 있는 데이터의 양을 제어한다.
  - 송신 윈도우의 크기는 수신측에서 알려주는 수신 윈도우의 크기에 따라 결정된다.
  - 송신측은 한 번에 송신 윈도우의 크기만큼 보낼 수 있다.
  - 수신측에서 데이터를 받고 ACK(확인 응답)을 보내면, 송신 윈도우가 이동하여 새로운 데이터를 보낼 수 있다.
  - window size = n (RTT 시간 동안 한꺼번에 보낼 수 있는 크기 : receive window size- sended size) = 상대방이 받을 수 있는 양

### Receive window in TCP

![Receive-window-in-TCP](/assets/img/2024-04-02-Network-2/Receive-window-in-TCP.png)

- 수신 윈도우는 수신측에서 사용하는 윈도우로, 수신할 수 있는 데이터의 양을 제어한다.
- 수신 윈도우의 크기는 수신측의 버퍼 크기와 처리 속도에 따라 결정된다.
- 수신측은 수신 윈도우 크기만큼의 데이터를 받을 수 있다.
- 수신된 데이터를 처리하면 수신 윈도우가 이동하여 새로운 데이터를 받을 수 있다.
- 수신 윈도우 크기는 ACK 패킷에 포함되어 송신측에 전달된다.

### example of flow control

![Flow-Control-img](/assets/img/2024-04-02-Network-2/flow-control.png)

# Silly Window Syndrome(SWS)

- TCP에서 발생할 수 있는 병목 현상을 가리키는 용어이다. 이는 슬라이딩 윈도우 방식의 문제점 중 하나로 아래와 같은 현상이 발생한다.

### 송신측의 전송 단위가 작을 때

- 송신측이 한번에 전송하는 데이터의 크기(최대 세그먼트 크기)가 작을 때 발생하는 문제이다.
- 이는 보내는 데이터에 비해서 헤더가 크기 때문에 문제가 발생한다.

![Nagle-Algorithm](/assets/img/2024-04-02-Network-2/Nagle-Algorithm.png)

- 해결책 : Nagle 알고리즘
  - TCP에서 작은 패킷들의 전송을 최적화하기 위해 고안된 기법이다. 이 알고리즘의 목적은 작은 패킷들을 버퍼링하여 더 큰 패킷으로 만든 후 한꺼번에 전송함으로써 네트워크 효율성을 높이는 것이다.
  - 원리
    1. 보낼 데이터가 MSS(Maximum Segment Size)로 정의된 크기 만큼 쌓이면 상대편에게 무조건 전송
    2. 보낼 데이터가 MSS보다 작은 경우, 이전에 보낸 데이터에 대한 ACK이 오기를 기다림. ACK이 도달하면 보낼 데이터가 MSS보다 작더라도 상대에게 보냄
  - 이때 첫번째 전송은 사이즈가 작더라도 보낸다.
  - 장점
    - 작은 패킷들을 버퍼링하여 더 큰 패킷으로 만듦으로써 네트워크 효율성이 향상된다.
    - TCP 연결 설정 및 세션 종료 시 불필요한 지연을 방지할 수 있다.
  - 단점
    - 지연 시간이 증가할 수 있습니다. 특히 인터렉티브 애플리케이션에서 문제가 될 수 있다.
    - 버퍼 오버플로우가 발생할 수 있다.
  - 단점을 방지하기 위해 이러한 옵션을 활성/비활성화 할 수 있다.

### 수신측의 버퍼가 작을 때

- 수신측의 application이 버퍼를 너무 천천히 비우면서 발생하는 문제
  - 해결 방법
    1. 수신 버퍼 증가
    2. 윈도우 스케일 옵션 사용
    3. 지연 ACK 정책
       - MSS 크기 만큼 비거나, 버퍼의 반이 되어야 ACK을 보내는 방식
    4. MSS 크기 증가

### SYN Flooding

![SYN-Flooding-img](/assets/img/2024-04-02-Network-2/SYN-Flooding.png)

- TCP 연결 과정을 악용하는 대표적인 DoS(Denial of Service, 서비스 거부) 공격 방식 중 하나이다.
- 공격 과정
  - 공격자는 가상의 IP 주소를 사용하여 SYN 패킷을 보낸다.
  - 서버는 SYN-ACK 패킷 응답을 기다리며 연결 요청 정보를 백로그 큐에 저장한다.
  - 하지만 공격자는 이에 대한 ACK 패킷 응답을 보내지 않는다.
  - 결과적으로 서버의 백로그 큐가 가득 차게 되어 정상 연결 요청을 더 이상 받아들이지 못하게 된다.
- 이러한 공격을 완화하기 위해서는 아래와 같은 방법이 있다.
  - SYN 쿠키 사용: 일시적인 백로그 큐 활용
  - 최대 백로그 큐 크기 증가
  - SYN 플러드 탐지 및 차단
  - 서버 과부하 시 연결 요청 제한 등

### TCP의 Normal operation

![Normal-Operation](/assets/img/2024-04-02-Network-2/Normal-Operation.png)

- Rule 1. Sending Buffer에 보통 보낼게 있으면 데이터랑 ACK이랑 같이 보냄
- Rule 2. 데이터를 받아서 ACK을 보내야 할 때 또 보낼 게 있는지 잠시 대기함(Delayed ACK)

  1. ACK 패킷 수 감소
     - 매 데이터 패킷 마다 ACK을 보내면 많은 수의 ACK 패킷이 발생한다.
  2. 프로세서 부하 감소
     - 빈번한 ACK 생성은 불필요한 프로세서 부하를 유발한다
  3. 패킷 결합(Packet Coalescing)

- Rule 3. Rule2에 의해 기다리던 도중 ACK이 기준치만큼 누적되면 바로 누적된 ACK을 한번에 보낸다.(일반적으로 200ms 또는 연이은 두 패킷 수신시)
  - 10개당 ACK 하나도 가능하지만 ACK이 유실되면 데이터 10개가 없어지는 것이므로 안전을 위해 2-3개씩 묶는다

![Lost-Segment](/assets/img/2024-04-02-Network-2/Lost-Segement.png)

- Rule 4. 순서가 바뀌면 바로 ACK을 보낸다.
- Rule 5. 유실되어 못받은 데이터를 받게 되면 바로 응답을 보냄
  - 중복 ACK 전송 : 송신측은 예상한 순서번호와 다른 ACK를 받으면 해당 ACK를 무시하고 대신 이전에 전송한 마지막 세그먼트에 대한 ACK를 기대하고 재전송 타이머를 가동시킨다.
  - 빠른 재전송 (Fast Retransmit) : 수신측은 예상하지 않은 순서번호의 세그먼트를 받으면 중복 ACK를 전송한다. 송신측은 연속 3개의 중복 ACK를 받으면 해당 세그먼트를 재전송한다.(한 번의 중복으로는 lost라고 확신하기 애매하기 때문) 이를 통해 패킷 손실에 신속히 대응할 수 있다.
  - 선택적 확인응답 (Selective Acknowledgment, SACK) : SACK 옵션을 사용하면 수신된 데이터 범위를 정확히 알릴 수 있다. 송신측은 SACK 정보를 활용하여 손실된 세그먼트만 선별적으로 재전송할 수 있다.
  - ACK 순서 재정렬 (ACK Reordering) : 일부 구현에서는 도착 순서가 바뀐 ACK 패킷의 순서를 재정렬하는 기능을 제공한다. 일정 시간 동안 도착한 ACK를 버퍼링하고 순서대로 처리한다.
- UDP는 이런 케어가 없기 때문에 중간에 없어진 것에 대한 책임을 지지 않는다.(ex : 라이브 스트리밍)

### Lost acknowledgement corrected by responding a Segment

![Responding-a-Segment-img](/assets/img/2024-04-02-Network-2/responding-a-Segment.png)

- Rule 6. 중복된 패킷을 받으면 지금까지 받은 것에 대한 ACK을 바로 전송함.

### Lost Acknowledgement

![Lost-Acknowledge-img](/assets/img/2024-04-02-Network-2/Lost-acknowledgement.png)

- 중간에 수신측에서 수신에 대한 응답으로 보냈던 ACK이 유실됐어도, 해당 ACK보다 높은 번호의 ACK을 받게되면 그동안의 데이터는 잘 받은 것이 보장됨 (cumulative의 장점)

### ACK 하나가 없어지면 데드락이 발생하는 케이스

- case 1 : 송신측에서 모든 데이터를 다 전송한 뒤 FIN 패킷을 보낸 뒤, 수신 측에서 해당 패킷을 수신하고 ACK패킷을 보냈지만 이것이 유실된 경우 양측은 무한정 대기를 할 수 있다.
- case 2 : 수신측의 rwnd(receive window size)가 0 일 때 해당 패킷을 송신측에서 받고, 다시 보내달라는 요청이 올 때까지 대기하고 있는 상황에서, 버퍼에 공간이 생긴 수신측이 보낸 ACK 유실될 경우

- 위의 두 케이스는 time-expired 전략을 이용해 일정 시간이 지난 뒤에 상대측의 반응이 없으면 다시 패킷을 보내는 방식으로 해결할 수 있다.

### 혼잡제어(Congestion Control)

![Congestion-Control-img](/assets/img/2024-04-02-Network-2/Congestion-Control.png)

- TCP connection1과 TCP connection2가 통과하는 라우터 R이 존재하는 상황이다.
- 이때 local 환경이 아니라면 중앙 통제가 없기 때문에 라우터에서 데이터의 양이 많은지 적은지 판단할 수 없다.
  - 이로 인해 두 커넥션이 사이좋게 50Mbps 씩 데이터를 전송하는 것이 불가능하다.
- 왜냐하면 Pack Switching의 컨셉이 인터넷에 접속했을 땐 Best-Effort 방식이기 때문이다.
  - 최선은 다하지만 보장하진 못한다.
- 라우터 R의 output 크기가 100인데 input이 200이 들어올 경우 Buffer에 input을 쌓아둔다. 그러다가 Buffer 마저 꽉차게 된다면 Router가 해당 이후 데이터들을 버린다.

### Packet delay and Network Load

![Packet-Delay-And-Network-Load-img](/assets/img/2024-04-02-Network-2/Delay-Load-graph.png)

- x축 : 보내는 시간 당 데이터 양
- y축 : receiver가 받는데 걸리는 시간
- Capacity : 전송 용량
- 보내는 시간 당 데이터의 양이 전송 용량의 근처로 가게 될 때 Delay는 기하급수적으로 증가하게 된다.

### Throughput vs Network Load

![Throughput-vs-Network-Load-graph-img](/assets/img/2024-04-02-Network-2/Throughput-vs-Network-Load.png)

- x축 : 보내는 시간 당 데이터 양
- y축 : 파일 하나를 받는 속도
- Capacity를 넘어가게 되면서 혼잡이 발생하여 Packet lose가 발생한다.
- 그로 인해 Throughput은 감소하게 된다.(일시적인 receiving rate는 늘어날 수 있다)

### Congestion Avoidance : additive increase

![Congestion-avoidance-additive-increase-img](/assets/img/2024-04-02-Network-2/Congestion-Avoidance-Additive-Increase.png)

- rwnd : receiver의 window size(한번에 수신할 수 있는 최대 데이터의 양)
- cwnd : sender에서 유지하는 window size. 네트워크 혼잡 상황에 따라 가변적이다.(송신자가 한번에 전송할 수 있는 최대 데이터의 양)
  - TCP 송신자는 rwnd와 cwnd 중 작은 값을 실제 전송 윈도우로 사용한다.(이는 혼잡제어와 흐름제어 둘을 고려한 선택이다)
- 네트워크가 혼잡하지 않고 패킷이 성공적으로 전송되고 확인되면 TCP는 Additive Increase 메커니즘을 사용한다.
- 이 단계에서 발신자는 패킷 손실 없이 각 왕복 시간(RTT)마다 고정된 양(일반적으로 하나의 최대 세그먼트 크기, MSS)만큼 혼잡 윈도우 크기를 점진적으로 증가시킨다.
- 혼잡 윈도우가 선형적으로 증가하기 때문에 일반적으로 RTT당 하나의 MSS씩 증가하므로 증가는 가산적이다.

### Slow Start, Exponential increase

![Slow-Start-Exponential-Increase-img](/assets/img/2024-04-02-Network-2/Slow-Start-Exponential-Increase.png)

- TCP 연결이 설정되면 일반적으로 작은값(1MSS)으로 초기화 된다.
  - 이를 통해 네트워크에 부하를 주지 않고 사용 가능한 대역폭을 점진적으로 늘릴 수 있다.
- exponential increase(지수 증가)
  - Slow Start 단계에서 발신자는 수신된 각 ACK에 대해 혼잡 윈도우 크기를 1MSS만큼 증가시킨다.
  - 이는 각 RTT마다 혼잡 윈도우가 두 배로 증가함을 의미한다(즉, 지수 함수적 증가).
  - 이 지수 증가를 통해 발신자는 사용 가능한 대역폭을 빠르게 탐색하고 활용할 수 있다.

### Congestion Example

![Congestion-Example-img](/assets/img/2024-04-02-Network-2/Congestion-Example.png)

- 해당 예시에서는 cwnd의 최솟값을 1로 설정해둔 상태이며, 첫번째 Slow Start 구간에서 Threshold (이 예시에서는 16으로 지정)이후로 addictive increase를 하고 있는 것을 볼 수 있다.
- 그러다가 RTTs 8 지점에서 Time-out이 발생하였다.
  - 혼잡이 심해 3 ACK duplicate도 안되는 상황으로 인식함. window size = packet 1로 변환 후, Threshold를 time-out 발생 당시 size의 절반(이 예시에서는 20에서 발생하였으므로 threshold가 10으로 설정됨)으로 설정 후, Slow Start 방식으로 증가.
- RTT 14 지점에서 3 ACK duplicate로 인한 혼잡 detect -> fast-retransmission 방식을 수행 한 뒤, 윈도우 사이즈를 반으로 줄임 -> 보내는 양 절반 -> 스피드 절반
  - 모든 사용자가 절반으로 줄음
  - 이후에 AI(Addictive Increase) 방식으로 증가

### TCP Congestion Policy summary

![TCP-Congestion-Policy-Summary-img](/assets/img/2024-04-02-Network-2/TCP-Congestion-Policy-Summary.png)

### TCP Fair

![TCP-Fair-img](/assets/img/2024-04-02-Network-2/TCP-Fair.png)

- TCP는 인터넷 사용자들 간의 공평한 대역폭 공유를 목표로 하는 개념이다. 이는 다양한 TCP 연결이 네트워크 리소스를 공평하게 공유할 수 있도록 설계되어 있다.
- 사용자의 초기 윈도우 크기가 다르더라도 TCP Fair의 주요 매커니즘은 아래와 같다.

1. AIMD 혼잡 제어
   - TCP Fair는 AIMD(Addictive Increase/Multiplicative Decrease, 혼잡제어 알고리즘)에 기반한다.
   - AIMD는 네트워크가 혼잡하지 않을 때는 혼잡 윈도우 크기를 선형적으로(Additive Increase) 증가시키고, 혼잡이 감지되면 혼잡 윈도우 크기를 배수로(Multiplicative Decrease) 감소시킨다.
   - 알고리즘은 경쟁하는 연결들이 가용 대역폭을 공평하게 공유하도록 하는 수렴 속성을 가지고 있다.
2. RTT 공평성
   - TCP Fair는 서로 다른 Round-Trip-Times(RTTs)를 가진 연결 간의 공평성도 고려한다.
     - RTT가 긴 연결은 혼잡 윈도우를 조정하는데 더 많은 시간이 걸리므로 RTT가 짧은 연결에 비해 불리할 수 있다.
   - 이러한 불공평함을 해결하기 위해 RTT 차이를 고려하여 혼잡 윈도우 업데이트를 조정한다.
3. 슬로우 스타트와의 상호 작용
   - Slow Start 메커니즘과 상호 작용하여 초기 윈도우 크기가 다른 연결 간의 공평성을 보장한다.
   - Slow Start 중에 윈도우 크기가 작은 연결은 더 빠르게 증가하여 더 큰 초기 윈도우를 가진 연결을 따라잡을 수 있습니다. -일단 모든 연결이 Slow Start 임계값에 도달하면 AIMD 혼잡 제어가 적용되어 장기적인 공평성을 보장한다.
4. 공평성 지수
   - TCP Fair의 성능은 Jain의 공평성 지수와 같은 메트릭을 사용하여 평가할 수 있다..
   - 공평성 지수는 경쟁하는 연결들 간의 대역폭 할당이 얼마나 공평한지 측정한다.
   - 공평성 지수가 1에 가까울수록 모든 연결에 대역폭이 완벽하게 공평하게 분배되었음을 나타낸다.

### TCP vs UDP 정리

- TCP
  - 연결지향적 프로토콜로, 통신을 시작하기 전에 발신자와 수신자 간에 연결을 설정한다.
  - 신뢰할 수 있는 데이터 전송을 보장한다. 손실된 패킷은 재전송되고, 순서가 잘못된 패킷은 재정렬 된다.
  - 혼잡 제어와 흐름 제어 메커니즘을 사용해 데이터 전송 속도를 조절하고 네트워크 혼잡을 방지한다.
  - 데이터 무결성을 위해 check-sum을 사용한다.
  - 3-way 핸드셰이크를 사용하여 연결을 설정하고, 4-way 핸드셰이크를 사용하여 연결을 종료한다.
  - 이메일, 웹 브라우징, 파일 전송 등 신뢰성이 중요한 애플리케이션에 적합합니다.
  - 단점 :
    - UDP에 비해 더 많은 오버헤드와 지연 시간이 발생합니다.
    - 연결 설정 및 종료에 추가 시간이 소요됩니다.
    - 실시간 애플리케이션에는 적합하지 않을 수 있습니다.
- UDP
  - 비연결형 프로토콜로, 발신자와 수신자 간에 연결을 설정하지 않는다.
  - 신뢰할 수 없는 데이터 전송을 제공합니다. 패킷 손실, 중복 또는 순서 잘못됨이 발생할 수 있다.
  - 흐름 제어나 혼잡 제어 메커니즘이 없다.
  - 체크섬을 사용하여 기본적인 오류 검사를 수행한다.
  - 빠르고 효율적인 데이터 전송을 제공한다.
  - VoIP, 온라인 게임, 라이브 비디오 스트리밍 등 실시간 애플리케이션에 적합하다.
  - 장점 :
    - TCP보다 오버헤드가 적고 지연 시간이 짧다.
    - 연결 설정이 필요하지 않아 빠른 데이터 전송이 가능하다.
    - 실시간 애플리케이션에 적합하다.
    - 멀티캐스트 및 브로드캐스트 통신을 지원한다.
  - 단점 :
    - 신뢰할 수 없는 데이터 전송을 제공한다.
    - 패킷 손실, 중복 또는 순서 잘못됨이 발생할 수 있다.
    - 흐름 제어나 혼잡 제어가 없어 네트워크 혼잡이 발생할 수 있다.
    - 데이터 무결성을 보장하지 않는다.

### P2P(Peer-to-Pear)

- 중앙 서버 없이 개별 노드 간에 직접 통신하고 리소스를 공유하는 분산 네트워크 아키텍처이다. P2P 네트워크에서 각 노드는 클라이언트와 서버의 역할을 동시에 수행한다.
- P2P 방식의 동작 원리
  1. 노드 발견: P2P 네트워크에 참여하려는 노드는 다른 노드를 찾기 위해 부트스트랩 노드나 분산 해시 테이블 (DHT)과 같은 메커니즘을 사용한다.
  2. 연결 설정: 노드는 다른 노드와 직접 연결을 설정하여 데이터를 공유하고 통신한다.
  3. 리소스 공유: 각 노드는 자신의 리소스 (파일, 대역폭 등)를 네트워크의 다른 노드와 공유한다.
  4. 데이터 전송: 노드는 다른 노드와 직접 데이터를 주고받으며, 중앙 서버를 거치지 않는다.
  5. 중복성과 분산: P2P 네트워크는 리소스와 데이터의 중복 사본을 유지하여 가용성과 내결함성을 향상시킨다.
- P2P 방식의 부정적인 영향
  - 대역폭 소비: P2P 애플리케이션은 많은 양의 데이터를 전송하므로 네트워크 대역폭을 상당히 소비할 수 있다. 이는 다른 애플리케이션의 성능에 영향을 미칠 수 있다.
  - 네트워크 혼잡: P2P 트래픽은 네트워크 혼잡을 유발할 수 있다. 많은 노드가 동시에 데이터를 전송하면 네트워크 용량을 초과하여 전반적인 성능 저하를 초래할 수 있다.
  - NAT와 방화벽 문제: P2P 통신은 종종 NAT (Network Address Translation)와 방화벽을 통과하는 데 어려움을 겪는다. 이로 인해 연결 설정과 데이터 전송에 문제가 발생할 수 있다.
  - 보안 위험: P2P 네트워크는 악성 노드에 취약할 수 있다. 악성 노드는 다른 노드를 공격하거나, 민감한 정보를 수집하거나, 불법 콘텐츠를 배포할 수 있다.
  - 저작권 문제: P2P 네트워크는 종종 저작권으로 보호된 자료의 불법 공유에 사용된다. 이는 법적 및 윤리적 문제를 야기할 수 있다.
  - 트래픽 형태 예측 어려움: P2P 트래픽은 중앙 집중식 모델보다 예측하기 어려울 수 있다. 이는 네트워크 용량 계획과 관리를 어렵게 만든다.
  - ISP와의 마찰: P2P 트래픽으로 인해 ISP (Internet Service Provider)는 네트워크 혼잡과 대역폭 소비 문제에 직면할 수 있다. 이로 인해 ISP가 P2P 트래픽을 제한하거나 차단하는 등의 조치를 취할 수 있다.

### TCP Timer

- 종류와 역할
  - 재전송 타이머(Retransmission Timer)
    - 역할 : 송신자가 세그먼트를 전송한 후, 수신자로부터 확인 응답(ACK)을 받지 못한 경우 재전송을 트리거 한다.
    - 동작 : 세그먼트가 전송될 때 재전송 타이머가 시작되며, ACK를 받으면 타이머가 중지된다. 타이머가 만료되면 해당 세그먼트가 재전송된다.
    - 타임아웃 값: 동적으로 조정되며, 일반적으로 RTT (Round-Trip Time)의 추정값과 편차를 기반으로 계산된다.
  - 지속 타이머(Persistence Timer)
    - 역할 : 수신자의 수신 윈도우 크기가 0이 되어 송신자가 더 이상 데이터를 전송할 수 없을 때 사용된다.
    - 동작 : 수신자가 윈도우 크기를 0이라고 광고하면 지속 타이머가 시작된다. 타이머가 만료되면 송신자는 크기가 1인 프로브 세그먼트(Prove Segment)를 전송하여 수신자의 윈도우 크기 업데이트를 요청한다.
    - 타임아웃 값 : 일반적으로 재전송 타임아웃(RTO)의 배수로 설정된다.
  - Keep-alive Timer
    - 역할: 장시간 유휴 상태인 연결을 검사하고 연결이 여전히 활성 상태인지 확인한다.
    - 동작: 일정 기간 동안 연결에서 데이터 교환이 없으면 키프얼라이브 타이머가 시작된다. 타이머가 만료되면 송신자는 키프얼라이브 프로브 세그먼트를 전송하여 연결 상태를 확인한다.
    - 타임아웃 값: 일반적으로 2시간으로 설정되지만, 애플리케이션에 따라 조정할 수 있다.
  - 시간 대기 타이머(Time-Wait Timer)
    - 역할: 연결 종료 프로세스 중에 지연된 세그먼트가 네트워크에서 사라지도록 보장한다.
    - 동작: 능동 클로저가 마지막 FIN을 전송하고 해당 FIN에 대한 ACK를 받으면 TIME-WAIT 타이머가 시작된다. 이 기간 동안 소켓은 재사용할 수 없다.
    - 타임아웃 값: 일반적으로 MSL (Maximum Segment Lifetime)의 두 배로 설정된다 (일반적으로 2분).
    - FIN-WAIT-2 Timer
      - 역할: 능동 클로저가 수동 클로저의 FIN을 기다리는 동안 무기한 대기하는 것을 방지한다.
      - 동작: 능동 클로저가 자신의 FIN에 대한 ACK를 받으면 FIN-WAIT-2 타이머가 시작된다. 타이머가 만료되면 연결이 강제로 종료된다.
      - 타임아웃 값: 일반적으로 애플리케이션에 따라 설정된다 (예: 10분).
- 타이머는 TCP의 신뢰할 수 있는 데이터 전송, 흐름 제어, 연결 관리 메커니즘에 중요한 역할을 한다.
- 각 타이머는 특정 문제를 해결하고 프로토콜의 효율성과 안정성을 보장하는 데 도움을 준다.

### RTT 계산 방법(Jacobson의 알고리즘)

![RTT-Example-img](/assets/img/2024-04-02-Network-2/RTT-Example.png)

- 각 RTT들의 역할
  - SRTT는 평균 RTT를 추적하여 네트워크 지연을 고려한다.
  - DevRTT는 RTT의 변동성을 추적하여 지연 변화에 대처한다.
  - RTO는 SRTT와 DevRTT를 조합하여 적절한 재전송 타임아웃을 설정한다.

1. Smoothed RTT (SRTT) 계산:
   - SRTT는 측정된 RTT 값의 가중 이동 평균이다.
   - 매 RTT 측정 시, 다음 공식을 사용하여 SRTT를 업데이트합니다: SRTT = (1 - α) _ SRTT + α _ RTT_sample
   - 여기서 α는 smoothing factor로, 일반적으로 0.125 (1/8)로 설정된다.
   - 이 공식은 과거 SRTT와 최신 RTT 샘플을 가중 평균하여 SRTT를 점진적으로 조정하는 방식이다.
2. RTT Deviation (DevRTT) 계산:
   - DevRTT는 측정된 RTT와 SRTT 간 차이의 가중 이동 평균이다.
   - 매 RTT 측정 시, 다음 공식을 사용하여 DevRTT를 업데이트한다: DevRTT = (1 - β) _ DevRTT + β _ |SRTT - RTT_sample|
   - 여기서 β는 smoothing factor로, 일반적으로 0.25 (1/4)로 설정된다.
   - 이 공식은 과거 DevRTT와 최신 RTT 샘플과 SRTT 간 차이의 절댓값을 가중 평균하여 DevRTT를 점진적으로 조정한다.
3. Retransmission Timeout (RTO) 계산:
   - RTO는 재전송 타이머의 타임아웃 값을 나타냅니다.
   - RTO는 SRTT와 DevRTT를 사용하여 다음 공식으로 계산됩니다: RTO = SRTT + 4 \* DevRTT
   - 이 공식은 SRTT에 DevRTT의 4배를 더하여 RTO를 설정합니다.
   - DevRTT의 배수 (일반적으로 4)는 안전 마진을 제공하여 불필요한 재전송을 방지합니다.
   - 일반적으로 RTO에는 하한값 (예: 1초)과 상한값 (예: 60초)이 설정됩니다.

- Jacobson의 알고리즘이라고도 하며, TCP의 널리 사용되는 재전송 타이머 계산 방법이다.

### Karn's algorithm

- TCP의 재전송 타이머를 최적화하기 위해 제안된 알고리즘이다.
- 재전송된 세그먼트의 RTT샘플을 사용하여 재전송 타이머를 업데이트 할 때 발생하는 모호성을 해결한다.
- 재전송의 모호성 문제
  - 재전송된 세그먼트에 대한 ACK를 받으면, 이 ACK가 원래 세그먼트에 대한 것인지 재전송된 세그먼트에 대한 것인지 확신할 수 없다.
  - 이러한 모호성으로 인해 재전송된 세그먼트의 RTT 샘플을 사용하여 재전송 타이머를 업데이트하면 부정확한 타이머 값이 계산될 수 있다.
- Karn's algorithm의 동작
  - 세그먼트가 전송될 때 마다 시퀀스 번호와 타임스탬프를 기록한다.
  - 세그먼트가 재전송되면 해당 세그먼트의 RTT 샘플을 수집하지 않는다.
  - ACK를 받으면 해당 ACK가 원래 세그먼트에 대한 것인지 확인한다.
    - 원래 세그먼트에 대한 ACK인 경우 RTT 샘플을 사용하여 SRTT와 DevRTT를 업데이트한니다.
    - 재전송된 세그먼트에 대한 ACK인 경우 RTT 샘플을 버리고 SRTT와 DevRTT를 업데이트하지 않는다.
  - 재전송 후 첫 번째 ACK를 받을 때까지 재전송 타이머의 값을 두 배로 늘린다(exponential backoff).
  - 새로운 세그먼트가 성공적으로 전송되고 해당 ACK를 받으면 재전송 타이머를 정상적으로 계산한다.
- 해당 알고리즘을 통해 얻을 수 있는 이점
  - Karn's algorithm의 이점
    - 재전송 모호성 문제를 해결하여 부정확한 RTT 샘플이 재전송 타이머 계산에 영향을 주지 않도록 한다.
    - 재전송 후 exponential backoff를 사용하여 네트워크 혼잡 시 재전송 빈도를 줄인다.
    - 정상적인 전송에서는 정확한 RTT 샘플을 사용하여 재전송 타이머를 최적화한다.

### TCP Options

![TCP-Options-img](/assets/img/2024-04-02-Network-2/Option.png)

- Single-byte 옵션
  - End of option list(EOL) : 옵션 리스트의 끝을 표시한다.
  - No Operation(NOP) : 옵션 필드의 정렬을 위해 사용한다.
- Multiple-byte 옵션(2byte 이상으로 구성된 옵션, 옵션 종류, 옵션 길이, 옵션 데이터로 이루어져 있다. 각 옵션의 길이는 가변적이며, 옵션 길이 필드에 명시된다.)

  ![MSS-option-img](/assets/img/2024-04-02-Network-2/MSS-option.png)

  - Maximum segment size (MSS):

    - MSS 옵션은 TCP 세그먼트의 최대 크기를 협상하는 데 사용된다.
    - connection이 되면 더이상 못바꿈(초기 SYN, SYN+ACK 과정에서 설정)
    - 이 옵션은 4바이트로 구성되며, 옵션 종류 (Kind=2), 옵션 길이 (Length=4), MSS 값으로 이루어진다.
    - MSS 값은 수신자가 수용할 수 있는 최대 세그먼트 크기를 나타낸다.

      ![Window-scale-factor-option-img](/assets/img/2024-04-02-Network-2/Window-scale-factor.png)

  - Window scale factor
    - Window scale factor 옵션은 TCP 윈도우 크기를 확장하는 데 사용된다.
    - 이 옵션은 3바이트로 구성되며, 옵션 종류 (Kind=3), 옵션 길이 (Length=3), Shift count로 이루어진다.
    - Shift count는 윈도우 크기를 왼쪽으로 이동시키는 비트 수를 나타낸다.

  ![Timestamp-option-img](/assets/img/2024-04-02-Network-2/Timestamp-option.png)

  - Timestamp
    - Timestamp 옵션은 TCP 세그먼트에 타임스탬프를 추가하여 RTT 측정 정확도를 향상시키고 PAWS를 구현하는 데 사용된다.(파일이 길어서 seq number가 한바퀴 돌았을 때, 혼동하는 것을 방지함)
    - 이 옵션은 10바이트로 구성되며, 옵션 종류 (Kind=8), 옵션 길이 (Length=10), Timestamp value와 Timestamp echo reply로 이루어진다.
  - SACK-permitted:
    - SACK-permitted 옵션은 TCP 연결에서 SACK 옵션의 사용을 협상하는 데 사용된다.
    - 이 옵션은 2바이트로 구성되며, 옵션 종류 (Kind=4), 옵션 길이 (Length=2)로 이루어진다.
      ![SACK-img](/assets/img/2024-04-02-Network-2/SACK-option.png)
  - SACK
    - SACK 옵션은 선택적 확인 응답을 사용하여 TCP의 손실 복구 메커니즘을 개선한다.
    - 이 옵션은 가변 길이로 구성되며, 옵션 종류 (Kind=5), 옵션 길이, 수신한 블록의 Left edge와 Right edge로 이루어진다.
    - SACK 옵션은 수신자가 수신한 세그먼트의 불연속 블록을 송신자에게 알리는 데 사용된다.
    - SACK 옵션은 수신자가 이미 확인응답(ACK)을 보낸 세그먼트 이후에 수신한 불연속적인 세그먼트 블록에 대한 정보를 제공하는 데 사용된다.

### SACK 옵션의 주요 기능

1. 불연속 세그먼트 확인
   - 수신자는 수신한 세그먼트의 불연속 블록을 송신자에게 알릴 수 있다.
   - 수신자는 SACK 옵션을 사용하여 수신한 세그먼트의 범위를 명시적으로 나타냅니다.
   - 이를 통해 송신자는 어떤 세그먼트가 성공적으로 수신되었는지 알 수 있다.
2. 선택적 재전송
   - SACK 정보를 바탕으로 송신자는 손실된 세그먼트만 선택적으로 재전송할 수 있다.
   - 이는 불필요한 재전송을 줄이고 손실 복구 과정을 효율적이게 만든다.
     - 특히 다중 세그먼트 손실이 발생한 경우에 효과적이다.
3. 빠른 손실 복구
   - SACK 정보를 활용하면 송신자는 손실된 세그먼트를 더 빨리 파악하고 재전송할 수 있다.
   - 이는 손실 복구 시간을 단축 시킨다.
4. 네트워크 효율성 향상
   - SACK 옵션을 사용하면 불필요한 재전송을 줄일 수 있으므로 네트워크 대역폭을 효율적으로 활용할 수 있다.

### SACK의 구체적인 예시

- 송신자가 1, 2, 3, 4, 5, 6, 7, 8의 8개 세그먼트를 전송했고, 수신자가 1, 2, 4, 5, 7, 8을 수신했다고 가정할 때
- 수신자는 다음과 같이 SACK 옵션을 포함한 ACK 패킷을 송신자에게 전송한다.
  - ACK 번호: 3 (다음으로 기대하는 시퀀스 번호)
    - SACK 옵션:
      - 블록 1: 시작 = 4, 끝 = 5
      - 블록 2: 시작 = 7, 끝 = 8
- 송신자는 해당 패킷을 받고 3과 6 패킷이 손실 됐다는 걸 알 수 있다.
  - ACK 번호 (3)으로부터 세그먼트 1과 2가 성공적으로 수신되었음을 알 수 있다.
  - SACK 옵션으로부터 세그먼트 4, 5, 7, 8이 성공적으로 수신되었음을 알 수 있다.
  - ACK 번호와 SACK 블록 사이의 간격 (3과 4-5 사이, 5와 7-8 사이)에 위치한 세그먼트 3과 6이 손실되었음을 추론할 수 있다.

### 해당 게시물에 대하여

- 해당 게시물은 컴퓨터 네트워크 수업 시간에 배운 내용을 정리한 것이며, 학기 중에 수업을 마무리 해야하는 시간제약이 있기에 TCP 프로토콜에 대해 생략되거나 빠진 내용이 다수 있을 수 있다.
- 이러한 빠진 부분은 지속적으로 게시물을 업데이트하며 채울 예정이다.
