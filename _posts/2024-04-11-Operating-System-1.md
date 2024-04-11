---
title: [Operating System.1 Computer System Overview 1]
author: excelsiorKim
date: 2024-04-11 18:26:03 +0900
categories: [CS, OS]
tags: [CS, OS, OperatingSystem]
keywords: [CS, OS, OperatingSystem]
description: 오퍼레이팅 시스템(운영체제) 공부
toc: true
toc_sticky: true
---

# 컴퓨터의 구성 요소와 역할

### CPU (Central Processing Unit)

- 컴퓨터의 두뇌 역할을 수행
- 프로그램 명령어를 해석하고 실행
- 연산 및 제어 기능 담당
- 레지스터, 제어장치, 산술논리장치(ALU)로 구성

### Memory

- 프로그램과 데이터를 일시적으로 저장하는 장치
- CPU가 빠르게 접근할 수 있는 작업 공간 제공
- RAM (Random Access Memory)과 ROM (Read-Only Memory)으로 구분
  - RAM: 휘발성 메모리, 읽기/쓰기 가능
  - ROM: 비휘발성 메모리, 읽기 전용, BIOS 등 저장

### I/O 장치

- 컴퓨터와 외부 장치 간의 데이터 입출력을 담당
- 입력 장치: 키보드, 마우스, 스캐너 등
- 출력 장치: 모니터, 프린터, 스피커 등
- 입출력 장치: 터치스크린, 네트워크 카드 등

### 버스(BUS)

- 컴퓨터 내부 구성 요소 간의 데이터 전송 통로
- 주소 버스, 데이터 버스, 제어 버스로 구성
  - 주소 버스: 메모리 주소 전송
  - 데이터 버스: 데이터 전송
  - 제어 버스: 제어 신호 전송
  - 버스 폭과 속도에 따라 시스템 성능 결정

### 보조 기억 장치(Secondary Storage Device)

- 하드 디스크, SSD, USB 드라이브, CD/DVD 등
- 비휘발성 (Non-Volatile): 전원이 꺼져도 데이터 유지
- 대용량 데이터 저장 가능
- 메모리에 비해 상대적으로 느린 데이터 액세스 속도
- 장기적인 데이터 저장 및 백업 용도로 사용

## Memory

- CPU가 직접 액세스하여 읽고 쓸 수 있음
- DRAM
  - 데이터를 전하로 저장하는 일종의 메모리. 데이터를 유지하려면 주기적인 새로고침이 필요하다.
  - DRAM은 SRAM보다 느리지만 비용 효율적, 단위 공간 당 더 많은 데이터를 저장할 수 있다.
- SRAM
  - 플립-플롭 회로를 사용하여 데이터를 저장. 새로 고칠 필요가 없다.
  - SRAM은 DRAM보다 빠르고, 전력소모가 적다. 그래서 캐시 메모리로 사용해서 시스템 성능을 향상 시킬 수 있다.
- SRAM과 DRAM은 휘발성(volatile) 메모리이다.

- Flash Memory
  - 비휘발성 메모리이다.
  - 충격과 진동에 대해 강한 내성을 가지고 있다.
  - 하드 디스크에 비해서 빠르다.
  - 특징 3가지
    - read/write의 performance가 대칭적이지 않다.
    - 제자리 쓰기가 불가능하다.
    - 제약된 내구성을 갖고 있다.

## 보조기억 장치

- Hard Disk Drive

  - 자성체를 이용해 0과 1로 데이터를 저장 가능(magnetic storage)
  - 비휘발성
  - 특정 영역의 데이터를 읽고 싶으면 read head라는 것이 해당 영역을 기계적으로 이동후(이동 시간 : Seek time) 읽음

- Solid State Disk(SSD)

  - Flash Memory 기반의 보조 기억 장치
  - Flash Translation Layer(FTL) : Flash Memory를 하드 디스크처럼 쓰기 위해 존재하는 층

- **I/O 접근, DRAM 접근도 최소화하여, 효율성을 극대화하기 위해 존재하는 것이 OS**

### Processor Register

- General Purpose Register(범용 레지스터) : 최적화된 레지스터를 사용함으로써 메모리 주소를 축소하기 위해 존재(이름이 없는 레지스터). CPU 내부에 있는 매우 빠른 저장 공간이다.
  - 연산을 위한 데이터 저장: 산술 및 논리 연산을 수행할 때, 피연산자를 레지스터에 일시적으로 저장한다. 이는 메모리에 직접 접근하는 것보다 훨씬 빠르다.
  - 메모리 주소 저장: 레지스터는 메모리 주소를 일시적으로 저장하는 데에도 사용된다. 예를 들어, 간접 주소 지정 모드에서는 레지스터가 메모리 주소를 담고 있다.
  - 중간 결과 저장: 복잡한 연산을 수행할 때, 레지스터는 중간 결과를 저장하는 데 사용됩니다. 이를 통해 불필요한 메모리 접근을 줄일 수 있다.
  - 상태 플래그 저장: 일부 레지스터는 CPU의 현재 상태를 나타내는 플래그 비트를 저장합니다. 예를 들어, 산술 연산 후 결과가 0인지, 음수인지 등의 정보를 저장한다.
  - 스택 포인터와 프로그램 카운터 저장: 스택 포인터(SP)와 프로그램 카운터(PC)는 전용 레지스터에 저장되며, 각각 현재 스택의 top과 다음에 실행될 명령어의 주소를 가리킨다.
- Special Purpose Register
  - Program Counter (PC): 다음에 실행될 명령어의 메모리 주소를 저장하고 있다.
  - Stack Pointer (SP): 현재 스택의 top을 가리키는 주소를 저장하고 있다.
  - Flags Register: CPU의 현재 상태를 나타내는 플래그 비트들을 저장하고 있다. 예를 들면, Zero Flag(ZF), Carry Flag(CF), Overflow Flag(OF) 등이 있다.
  - Instruction Register (IR): 현재 실행 중인 명령어를 저장하는 데 사용된다.
  - Memory Address Register (MAR): 메모리에 접근할 때 사용할 주소를 저장하는 레지스터이다.(I/O 용도)
  - Memory Data Register (MDR): 메모리에서 읽어온 데이터나 메모리에 쓰여질 데이터를 일시적으로 저장하는 레지스터이다.(I/O 용도)

# Interrupt Mechanism

![Interrupt-Mechanism](/assets/img/2024-04-11-OS-1/Interrupt-Mechanism.png)

- 현재 프로그램을 중단하고, 발생한 이벤트에 맞는 Handler를 수행하는 것
- 인터럽트는 Masking 될 수 있다. (disable interrupt를 할 수 있다는 의미)
- 목적 : System Utilzation과 Timely Service
  - System Utilization 측면
    - CPU는 인터럽트 처리 동안에만 해당 작업에 집중할 수 있으므로, 대기 시간 동안 다른 작업을 수행할 수 있다.
    - 인터럽트 우선순위를 통해 중요한 이벤트를 먼저 처리할 수 있어, CPU 자원을 효율적으로 활용할 수 있다.
    - 폴링 방식과 달리, CPU가 지속적으로 장치 상태를 확인할 필요가 없어 불필요한 오버헤드를 줄일 수 있다.
      - 폴링 방식 : CPU가 주변 장치의 상태를 주기적으로 확인하여 입출력 작업을 처리하는 방법
  - Timely Service 측면
    - 인터럽트를 통해 중요한 이벤트를 신속하게 처리할 수 있어, 시스템의 반응성을 높일 수 있다.
    - 우선순위가 높은 인터럽트를 먼저 처리함으로써, 시간 제약이 있는 작업을 적시에 처리할 수 있다.
    - 주기적인 타이머 인터럽트를 활용하여, 시간 기반 스케줄링과 시간 관리 기능을 구현할 수 있다.

### Interrupt Processing

![Interrupt-Processing-img](/assets/img/2024-04-11-OS-1/Interrupt-Processing.png)

1. 인터럽트 발생: 주변 장치, 타이머, 예외 상황 등으로 인해 인터럽트가 발생한다.
2. 인터럽트 신호 전송: 인터럽트 소스가 CPU에 인터럽트 신호를 전송한다.
3. 인터럽트 승인: CPU가 현재 명령어를 완료한 후, 인터럽트를 승인한다.
4. 현재 상태 저장: CPU는 현재 실행 중인 프로세스의 상태(PC, 레지스터 값 등)를 스택에 저장한다.
5. ISR 실행: CPU는 해당 인터럽트에 대한 Interrupt Service Routine(ISR)으로 점프하여 인터럽트를 처리한다.
6. 상태 복원 및 복귀: ISR 실행이 완료되면, CPU는 스택에서 이전 상태를 복원하고 인터럽트가 발생하기 전 코드로 복귀한다.

### Interrupt and Exception

![Interrupt-and-Exception-img](/assets/img/2024-04-11-OS-1/Interrupt-and-Exception.png)

- 인터럽트는 두 종류로 나뉜다
  - Asynchronous Interrupt(External Interrupt) : H/W device와 같이 외부의 디바이스들로 인해 발생하는 Interrupt
  - Synchronous Interrupt(Internal Interrupt) : CPU에 의해 발생하는 Interrupt(System Call도 포함)
    - 해당 Interrupt는 프로그램 실행과 직접적인 연관이 있기 때문에 즉각처리 되어야한다.

### Multiple Interrupt

![Sequential-Interrupt-Processing-img](/assets/img/2024-04-11-OS-1/Sequential-Interrupt-Processing.png)

- Sequential Interrupt processing
  - 한 번에 하나의 인터럽트만 처리하는 방식이다.
  - 인터럽트가 발생하면, 현재 실행 중인 인터럽트 서비스 루틴(ISR)이 완료될 때까지 다른 인터럽트는 대기 상태에 놓인다.
  - ISR이 완료되면, 대기 중인 인터럽트 중 우선순위가 가장 높은 인터럽트가 처리된다.
  - 이 과정은 모든 인터럽트가 처리될 때까지 반복된다.
  - 장점: 구현이 간단하고, 인터럽트 처리 과정이 예측 가능하다.
  - 단점: 우선순위가 높은 인터럽트의 처리가 지연될 수 있어, 시스템 응답성이 떨어질 수 있다.
    ![Nested-Interrupt-Processing-img](/assets/img/2024-04-11-OS-1/Nested-Interrupt-Processing.png)
- Nested interrupt processing
  - 인터럽트 처리 도중에 우선순위가 더 높은 인터럽트가 발생하면, 현재 처리 중인 ISR을 일시 중단하고 높은 우선순위의 인터럽트를 먼저 처리하는 방식이다.
  - 높은 우선순위의 ISR이 완료되면, 이전에 중단되었던 낮은 우선순위의 ISR이 다시 실행된다.
  - 이 과정은 인터럽트 우선순위에 따라 반복되며, 중첩된 인터럽트 처리가 가능하다.
  - 장점: 우선순위가 높은 인터럽트를 신속하게 처리할 수 있어, 시스템 응답성이 향상된다.
  - 단점: 구현이 복잡하고, 인터럽트 처리 시간을 예측하기 어려울 수 있습니다. 또한, 중첩된 인터럽트로 인해 스택 오버플로우 등의 문제가 발생할 수 있다.
