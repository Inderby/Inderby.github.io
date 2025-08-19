---
title: [Operating System.3 Computer System의 성장 역사와 멀티프로세싱 원리]
author: excelsiorKim
date: 2024-04-30 23:00:03 +0900
categories: [CS, OS]
tags: [CS, OS, OperatingSystem]
keywords: [CS, OS, OperatingSystem]
description: 오퍼레이팅 시스템(운영체제) 공부
toc: true
toc_sticky: true
---

### Main objectives of an OS

- Convenience(편리성) : 컴퓨터를 상용하기 쉬워진다.
- Efficiency(효율성) : 컴퓨터 자원을 효율적으로 사용할 수 있게 해준다.
- Ability to Evolve(유연한 진화) : 서비스를 방해하지 않고 새로운 시스템 기능을 효과적으로 개발, 테스트 및 도입할 수 있게 해준다.
  - 진화의 이유 : 새로운 하드웨어, 새로운 서비스, 버그 수정 등 OS와 하드웨어는 상호보안을 하며 진화해왔다.

# OS의 발전 역사

- OS의 중요한 발전 이유를 깨닫고, 어떠한 주요 기능들이 이러한 요구사항을 충족 시켜줬는지에 대해 알아볼 필요가 있기 때문이다.

## Serial Processing

- OS가 없고 인간이 OS의 역할을 했던 시기
- Job-to-Job Transition : 작업들 중간중간에 인간이 개입하여 Load/Unload를 직접함.
- 문제점 : Job-To-Job transition time이 길었다.
  1. 작업 중간 중간 인간의 개입이 필요하여 Setup time이 오래 걸렸다.
  2. Schedule time 수행 시간을 time slot으로 나눠 낭비되는 시간이 많았다.

## Simple Batch System

- 단순 일괄 처리 시스템 : 성격이 비슷한 작업을 묶어서 Setup time을 줄이고, job sequencing을 자동화 하는것을 목표로 함.
- Batch Monitor : 메인 메모리에 항상 상주해 있어야 하는 관리 프로그램
  - JCL(Job Control language)로 조작함.
- 이시기에 인식된 문제점과 요구사항들
  - Memory Protection(user <-> kernal)
  - I/O Protection(privileged instruction)
  - dual mode
  - cpu protection
    - prevent monopolizing CPU(CPU 독점 제한)
    - System Timer
  - Interrupts
  - I/O device controller
    - Synchronous I/O : read를 기다려야 된다는 문제 발생
    - Asynchronous I/O : write를 할시 데이터 무결성 문제 발생

## Multi-Programmed Batch System

- CPU 사용성을 높이고, 처리가 끝나지 않은 작업들을 메인 메모리에 유지하고, CPU의 Idle 상태를 피하고자 함.
- degree of multi-programming 증가 -> System utilization 증가
- 이 시기에 인식된 문제와 요구사항
  - Relocation(고정주소의 문제가 발생)
  - Memory protection(user - user)
  - MMU(Memory Management Unit) : Monitor가 시작 주소(base register)를 바꿔줌
  - multi-programming Example
    ![CPU-Response-img](/assets/img/2024-04-30-OS-3/CPU-response.png)
    - 위의 이미지에서 Mean Response Time의 계산 방법
      - 멀티 프로그래밍을 안했을 때 : 각 프로세스의 종료시간의 합 / 프로세스의 수 -> (5 + 20 + 30) / 3 = 18.3
      - 멀티 프로그래밍을 했을 때 : (5 + 15+ 10) / 3 = 10
    - CPU 가용성 계산
      - 멀티 프로그래밍을 안했을 때 : (CPU 사용률) \* (사용시간 / 총 시간)의 합 -> (70% \* 5/30 + 10% \* 15/30 + 10% \* 10/30) = 20%
      - 멀티 프로그래밍을 했을 때 : 90% \* 1/3 + 20 \* 1/3 + 10% \* 1/3 = 40%

## Time Sharing

- 회로의 발전, 사용자가 실행중인 프로그램과 상호작용하지 못하는 불편함이 발생.
- 하드웨어적 가격하락과 노동력의 가치 상승
- Interactive time sharing
  - 많은 사용자가 같은 기계를 동시에 사용할 수 있게 됨.
  - 각 작업은 Time Slice가 할당됨.(Preemption) <- 자원선점
  - read list가 등장함.
    ![Time-slice-img](/assets/img/2024-04-30-OS-3/Time-slice.png)

# Major advances in development

## Process

- 실행중인 프로그램을 말함.
- 프로세스가 유지하는 3가지 구성요소
  - **실행가능한 프로그램**
  - **프로그램과 관련된 데이터**
  - Execution Context(OS가 관리하기 위한 내부정보, Meta data)
    - process state : 프로세스의 현재 상태(실행 중, 대기 중, 준비됨 등)를 나타내는 정보
    - 프로그램 카운터(PC) : 다음에 실행할 명령의 주소를 나타내는 레지스터
    - 레지스터 상태 : 프로세스가 실행되는 동안 레지스터에 저장된 모든 정보를 포함하는 CPU 레지스터 세트 값.
    - stack pointer(SP) : 프로세스 스택의 현재 위치를 나타내는 레지스터
    - 메모리 관련 정보 : 메모리에서 프로세스 주소 공간의 위치, 크기 및 사용량에 대한 정보
    - I/O 상태 : 수행 중인 경우 프로세스의 I/O 작업에 대한 정보
    - 프로세스 우선순위 : 멀티 태스킹 운영체제에서 프로세스의 우선순위를 나타내는 정보

## Scheduling and Resource Management

- OS는 다양한 갯수의 queue를 유지한다.
  - Long-term queue
  - Short-term queue
  - I/O queue
- 스케줄링을 할 때 고려해야될 요소들
  - Fairness(공정성) : 대략적인 평등함과 공정함을 가지고 자원을 사용해야 한다.
  - Efficiency(효율성) : maximize throughput, minimize response time
  - Differential responsiveness(차별적 반응) : process마다 weight를 두는 방식

## Synchronization

- 별도의 게시물로 깊게 다룰 예정

## Memory management

- 이것또한 별도의 게시물로 깊게 다룰 예정

## Resource Protection

- 사용자가 특정한 권한이 필요한 무언가를 하려고 할때 OS procedure(절차)를 호출해야 된다.
- System Call Interface이 필요한 이유
  - 자원 보호 : 신뢰할 수 있는 대상(OS)와 신뢰할 수 없는 대상(user)를 구별할 수 있음
  - 안정성 : 사용자의 buggy 프로그램을 막을 수 있음(Resource hogs : infinite loop, exhaust memory)
  - 보안 : read/write을 OS권한으로 둠으로써 악성 프로그램으로부터 자원을 보호함.
- 최소의 System call 갯수를 유지하려고 노력함-> 이식성 상성, 보안성 상승
  - 이를 위해 대부분의 사용자는 직접적으로 system call을 하기 보다 API(Application Program Interface)를 사용한다.
- API를 통해 System Call을 호출하는 이유
  - OS의 유연성이 증가한다.
  - System call은 활용 난이도가 높으며, 라이브러리로 만들어 사용하기 편리하기 때문이다.
  - Java 또한 API를 통해 System Call을 활용한다.
- 사용자는 아래와 같은 이슈를 통해 커널 모드를 실행한다.
  - Interrupt
  - Exception
  - System call
