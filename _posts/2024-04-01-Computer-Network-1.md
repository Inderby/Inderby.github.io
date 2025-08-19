---
title: [Computer-Network 1. The OSI Model and the TCP/IP Protocol Suite]
author: excelsiorKim
date: 2024-04-01 18:10:03 +0900
categories: [CS, ComputerNetwork]
tags: [CS, ComputerNetwork]
keywords: [CS, ComputerNetwork]
description: 컴퓨터 네트워크 공부 기록
toc: true
toc_sticky: true
---

### 인터넷

- 컴퓨터로 연결하여 TCP/IP protocol 이라는 통신 프로토콜을 이용해 정보를 주고받는 컴퓨터 네트워크

### OSI 7 Layer

![OSI-7-layer-img](/assets/img/2024-04-01-Network-1/OSI-7-layer.png)

- ISO 단체가 만든 OSI Layer 데이터 통신을 module화 한것
- Layer7(애플리케이션 계층)
  - header에 특정 리소스에 대한 http get 요청과 같은 서버에 대한 실제 요청이 포함(message를 주고 받음)
- Layer6(presentation 계층)
  - 데이터 표현, 암호화 및 압축을 담당.
  - 해당 레이어의 헤더에 첨부된 콘텐츠에는 데이터 형식 정보가 포함.
- Layer5(Session 계층)
  - 서로 다른 장치에서 실행되는 응용 프로그램 간의 통신을 설정하고 유지한다.
  - 이 계층의 헤더에 첨부된 콘텐츠에는 세선 ID와 동기화 지점이 포함된다.
- Layer4(전송 계층)
  - header에 요청 응용 프로그램과 서버 프로그램의 소스 및 대상 포트번호가 포함. (ex : 80 - HTTP, 443 - HTTPS)
  - end-to-end 통신을 제공하고, 신뢰성 있는 데이터 전송을 보장한다.
  - 세그멘테이션, 오류 제어, 흐름 제어, 혼잡 제어 등의 기능을 수행한다.
  - TCP, UDP가 이 계층에서 동작함.
  - segment 송수신.
- Layer3(Network 계층)
  - header에 요청 장치와 서버의 소스 및 대상 IP 주소가 포함
  - Datagram을 주고 받음.
  - 서로 다른 네트워크 간의 데이터 전송을 담당한다.
  - 논리적 주소지정, 라우팅, 패킷 분할 및 재조립 등의 기능을 수행
  - IP 프로토콜이 대표적이다.
- Layer2(Data link)
  - 요청 장치와 서버의 소스 및 대상 mac 주소가 포함
  - Frame을 주고 받음
  - 프레임 동기화, 오류 제어, 흐름 제어 등의 기능을 수행한다.
- Layer1(물리 계층) : 자세한 설명은 생략한다.

### 용어 정리

- Port Number : 컴퓨터 내에 있는 Application들을 구별해주기 위한 숫자
- StoD(Source to Destination) : 네트워크를 통해 한 장치(소스)에서 다른 장치(목적지)로 데이터를 전송하는 프로세스를 의미
- Hop to Hop : 한 네트워크 노드에서 다음 노드로 데이터 패킷이 이동하는 것을 의미. 각 노드는 라우터, 스위치 또는 게이트웨이와 같은 네트워크의 일부인 장치를 나타냄
- mac 주소 : 네트워크 세그먼트 내에 통신에서 네트워크 주소로 사용하기 위해 네트워크 인터페이스 컨트롤러(nic)에 할당된 고유식별자. 즉, 로컬 네트워크 내의 다른 장치와 통신을 용이하게 하는 고유 식별자

### 네트워크 전달 방식

- 중앙 제어 전달 방식(Circuit Switching Network) : 중앙 컨트롤러(예 : 스위치 또는 라우터)가 네트워크를 통과하는 모든 패킷에 대한 포워딩 결정을 담당함. 컨트롤러는 각 패킷의 대상 주소를 검색하고 미리 정의된 규칙 및 구성에 따라 패킷이 대상에 도달하는 최상의 경로를 결정(즉, 출발 전 경로를 알고 가는 것. 경로가 보장 된다.). **하지만 낭비가 심하다, 연결 지향**
- 목적지 주소 전달 방식(Packet Switching Network) : 각 네트워크 장치가 각 패킷의 대상 주소만을 기중으로 독립적으로 전달 결정을 내림.(중앙 컨트롤러가 필요하지 않기 때문에 중앙 제어 전달 방식보다 간단하고, 비용이 적게듬, 비연결지향, 인터넷이 선택한 방식)

### 각종 네트워크 장치들

- Repeater : 신호를 다시 증폭 시켜주는 용도
- Hub : 인터페이스가 여러개 있는 repeater(여러 곳에 output이 존재)
- Bridge : repeater 역할 + 필터링
- switch : hub + 필터링

### logical address 전달 방식

![logical-address=img](/assets/img/2024-04-01-Network-1/logical-address.png)

### network 전달 과정

![network-transport-img](/assets/img/2024-04-01-Network-1/network-trans-img.png)
