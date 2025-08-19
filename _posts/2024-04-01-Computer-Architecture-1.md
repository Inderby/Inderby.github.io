---
title: [Computer-Architecture 1. Computer Abstractions and Technology]
author: excelsiorKim
date: 2024-04-01 16:10:03 +0900
categories: [CS, ComputerArchitecture]
tags: [CS, ComputerArchitecture]
keywords: [CS, ComputerArchitecture]
description: 컴퓨터 구조 공부 기록
toc: true
toc_sticky: true
---

### 컴퓨터의 종류

- personal computer : 범용 목적의 컴퓨터
- server computer : 네트워크 기반, 높은 수행 능력, 높은 신뢰성
- super computer : 수학적, 과학적 문제를 해결하기 위한 목적의 높은 연산력을 가진 컴퓨터
- embedded computer : 장치의 구동을 책임지는 컴퓨터, 자원들이 제약적이다.

### 컴퓨터의 성능에 영향을 미치는 것들

- algorithm : affects instruction count
- programming language, compiler, architecture : determine number of machine instructions executed per operation
- processor and memory system : determine how fast instructions are executed
- I/O system(including OS) : determine how fast I/O operations are executed

### 성능을 개선 시키는 7가지 아이디어

- Use abstraction to simplify design
- Make the common case fast
- performance via parallelism
- performance via pipelining
- performance via prediction
- hierarchy of memories
- dependability via redundancy

### Below Your Program

- application software
  - written in high-level language(HLL, ex : C, C++, Java)
- System software
  - Compiler : translates HLL code to machine code
  - Operating System : service code
    - handling input/output
    - managing memory and storage
    - scheduling tasks & sharing resources
- Hardware
  - processor, memory, I/O controller
    ![layer-img](/assets/img/2024-04-01-Architecture-1/layer-img.png)

### Levels of Program Code

- High-level language
  - Level of abstraction closer to problem domain
  - provides for productivity and portability
- Assembly language
  - Textual representation of instructions(기계어와 동일하지만 사람이 읽을 수 있는 언어)
- Hardware representation
  - Binary digits(bits)
  - Encoded instructions and data

### Inside the Processor(CPU)

- Data path : performs operations on data(데이터 운반)
- Control : sequences datapath, memory(datapath를 조정)
- cache memory (SRAM): 자주사용하는 것을 저장
- CPU core : 연산 기능 담당

### 컴퓨터의 성능을 판단하는 기준

- 응답시간(Response Time, execute time) : 어떤 일을 하는데 걸리는 시간 (일반적으로 이것을 성능으로 여김)
- Through put : 단위 시간 동안 얼마나 많은 시간을 할 수 있는지
  1. 더 빠른 버전의 프로세서로 교체 할 경우 : response time 증가, through put 증가
  2. 프로세서 하나 더 추가 : through put 중가, response time 증가 혹은 감소
- performance(성능) = 1 / (response time)

### Execution Time 측정

- elapse time : 프로그램을 시작해서 끝날 때 까지, 시스템에서 소요한 모든 시간 (processing, I/O, OS, Idle time 등 전반적인 것을 포함)
- CPU time : 해당 일을 하는데 걸린 순수 시간(I/O 시간 제외). 즉, 해당 프로그램이 돌아가는데 CPU를 차지한 시간 + kernal이 돌아가는 시간

### CPU의 clock

- 주기적으로 1과 0이 반복되는 신호
- clock의 포현
  - clock period(clock cycle time) : 1이 반복되는 주기
  - clock frequency(rate) : 1초 동안 반복되는 클락 수 = 1 / (clock period)
    ![clock-rate=img](/assets/img/2024-04-01-Architecture-1/clock-rate.png)
- CPU Time = CPU clock cycles \* clock cycle time = cpu clock cycle / clock rate
- 즉, 성능(performance)를 개선시키기 위해선
  - clock cycle 줄이기
  - clock rate 높이기

### Instruction Count and CPI

- Instruction Count : program과 system이 수행한 명령들의 수
- CPI(Cycle Per Instruction) : Instruction 하나 수행하는데 걸리는 시간
- Clock Cycles = Instruction Count \* CPI
- 단, Instruction 하나를 수행하는데 여러 Cycle이 걸릴 수 있다.
- clock cycle을 줄이는 방법
  1. Instruction Count를 줄인다.(ISA를 잘 설계해 고급 Instruction을 만든다.)
  2. CPI를 줄인다.

### Multi processor

- 하나의 processor chip에 여러 개의 core가 들어감
- 그럼에 따라 병렬 프로그래밍이 명시적으로 필요해짐
