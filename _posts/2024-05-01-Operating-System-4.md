---
title: [Operating System.4 Process Description and Control1]
author: excelsiorKim
date: 2024-05-01 13:00:03 +0900
categories: [CS, OS]
tags: [CS, OS, OperatingSystem]
keywords: [CS, OS, OperatingSystem]
description: 오퍼레이팅 시스템(운영체제) 공부
toc: true
toc_sticky: true
---

# Program VS Process

- Program : disk에 있는 binary sequence
- Process : code와 data가 메모리에 올라와 실행 중인 데이터(실행된 프로그램)

### The Memory layout of a program

![Memory-of-Layout-img](/assets/img/2024-05-01-OS-4/memory-layout-of-a-program.png)

- Stack : 지역변수, 매개변수, 함수의 주소가 저장되는 공간 위에서 아래로 증가함
- Heap : 프로세스 내에서 동적으로 할당되는 메모리 공간
- data
  - bss : 초기화 되지않은 전역 변수들이 저장되는 공간
  - initialized data : 전역 변수
- text : code가 저장되는 공간

### Execution Sequence

![Execution-Sequence-img](/assets/img/2024-05-01-OS-4/execution-sequence.png)
![call-stack-img](/assets/img/2024-05-01-OS-4/call-stack.png)

- 위의 그림처럼 특정 함수가 호출될 때 반환될 때 돌아올 주소를 stack에 저장한다.

### Implementation of Stack

![stack-img](/assets/img/2024-05-01-OS-4/stack.png)

- (사진에서는 편의를 위해 뒤집어 놓음)
- stack pointer : 스택의 맨위를 가리키는 레지스터. 프로세스가 스택에 데이터를 push하면 스택 포인터가 감소하고, 데이터가 스택에서 제거되면 스택 포인터가 증가한다.
- stack base : 메모리의 스택 영역에서 가장 낮은 주소. 스택 크기를 계산하고 할당된 메모리 영역을 넘는 스택을 방지하는데 사용
  - 프로세스가 생성될 때 설정되며 PCB(Process Control Block)에 저장
- stack limit : 메모리의 스택 영역에서 가장 높은 주소. overflow 방지
  - 프로세스가 생성될 때 설정되어 PCB(Process Control Block)에 저장

### Process Control Block(PCB)

- 여러 프로세스들이 여러 자원을 공유하기 때문에 이를 관리하기 위한 정보
  - running process가 Interrupt에 의해 제어권이 넘어갈 때 context data를 저장할 필요가 발생함. 이를 위해 존재하는 것이 PCB
- PCB의 구성요소
  - Process Identification(식별자)
  - Processor State Information(CPU의 상태)
  - Process Control Information(Process의 상태)

### Process Image

![Process-img](/assets/img/2024-05-01-OS-4/process-img.png)

- OS가 Process를 관리하기 위해 반드시 알아야 하는 것
  - process context
    - user program, data, stack
    - PCB

### Trace of Process

![Memory-ex-img](/assets/img/2024-05-01-OS-4/Memory-EX.png)

- 위의 이미지와 같은 메모리 상태가 존재할 때 실제로 프로세서는 아래와 같은 순서로 여러 프로세스들을 번갈아 실행한다.
  ![Trace-of-Process-img](/assets/img/2024-05-01-OS-4/Trace-of-Process.png)
- process switch는 mode switch가 선행적으로 필요하다.
- process switch가 일어나는 경우
  - Timer Interrupt
  - System Call(I/O Request)
  - Exception

### Two-State Process Model

![State-transition-Diagram-img](/assets/img/2024-05-01-OS-4/State-transition-diagram.png)
![Queueing-diagram-img](/assets/img/2024-05-01-OS-4/Queueing-Diagram.png)

- 운영체제에서 프로세스의 실행 상태를 설명하는 기본적인 모델이다.
- 프로세스는 두 가지 상태, 즉 Running 상태와 Not Running 상태 중에 하나일 수 있다.
- 두 상태를 전환 하게되는 이벤트
  - 프로세스가 CPU를 양보하거나(자발적 양보)
  - 타이머 인터럽트에 의해 선점되거나(비자발적 양보)
  - 입출력 작업이나 이벤트를 기다리는 경우(블로킹)
- 단점 : Queue에 있는 것 중에 I/O Request로 Block 상태인 Process를 구분하지 못하고 다시 Block된 Process를 실행할 수 있다.

### Five-State Process Model

![Five-State-Process-Model-img](/assets/img/2024-05-01-OS-4/Five-State-Process-Model.png)

1. New (새로운 상태):

   - 프로세스가 막 생성된 상태이다.
   - 프로세스가 아직 준비 큐에 추가되지 않은 상태이다.

2. Ready (준비 상태):

   - 프로세스가 CPU를 할당받을 준비가 된 상태이다.
   - 프로세스가 필요한 자원을 모두 가지고 있으며, CPU 스케줄러에 의해 선택되기를 기다리고 있다.
   - Ready 상태의 프로세스들은 준비 큐(Ready Queue)에 저장된다.

3. Running (실행 상태):

   - 프로세스가 현재 CPU를 점유하고 실행 중인 상태다.
   - 프로세스의 명령어들이 CPU에 의해 실행되고 있다.

4. Waiting/Blocked (대기/블로킹 상태):

   - 프로세스가 특정 이벤트나 자원을 기다리는 상태이다.
   - 예를 들어, 입출력 작업이 완료되기를 기다리거나, 다른 프로세스로부터 메시지를 받기 위해 대기하는 상태일 수 있다.
   - 프로세스는 대기 상태에서 이벤트가 발생하거나 자원을 획득하면 Ready 상태로 전환된다.

5. Terminated/Exit (종료 상태):
   - 프로세스가 실행을 완료하고 종료된 상태이다.
   - 프로세스가 모든 작업을 마치고 운영 체제에 의해 제거되는 상태이다.

- Five-State Process Model의 실제 구현 방식은 운영 체제마다 조금씩 다를 수 있지만, 일반적으로 다음과 같은 원리로 동작한다.

1. 프로세스 제어 블록 (PCB: Process Control Block):

   - 각 프로세스마다 PCB라는 자료구조를 유지한다.
   - PCB에는 프로세스의 상태, 프로세스 식별자(PID), CPU 레지스터 값, 메모리 할당 정보 등 프로세스 관련 정보가 저장된다.

2. 준비 큐 (Ready Queue):

   - Ready 상태의 프로세스들은 준비 큐에 저장된다.
   - 준비 큐는 일반적으로 링크드 리스트나 배열과 같은 자료구조로 구현된다.

3. 대기 큐 (Wait Queue):

   - Waiting/Blocked 상태의 프로세스들은 대기 큐에 저장된다.
   - 각 이벤트나 자원마다 별도의 대기 큐를 유지할 수 있다.

4. 스케줄러 (Scheduler):

   - 운영 체제의 스케줄러는 준비 큐에서 프로세스를 선택하여 CPU를 할당한다.
   - 스케줄링 알고리즘(예: FCFS, SJF, Priority, Round-Robin 등)을 사용하여 프로세스를 선택한다.

5. 컨텍스트 스위칭 (Context Switching):
   - 프로세스 간의 전환 시, 현재 실행 중인 프로세스의 상태를 저장하고 다음 실행할 프로세스의 상태를 복원하는 과정이다.
   - 컨텍스트 스위칭은 PCB를 사용하여 프로세스의 상태를 저장하고 복원한다.

- Five-State Process Model은 프로세스의 상태 변화를 추적하고 관리하는 데 사용되며, 운영 체제는 이를 기반으로 프로세스 스케줄링, 자원 할당, 동기화 등의 기능을 수행한다.
- 실제 구현에서는 큐, 리스트, PCB 등의 자료구조와 알고리즘을 사용하여 프로세스 상태를 관리하고 전환한다.

![Multi-Blocked-Queue-img](/assets/img/2024-05-01-OS-4/Multiple-blocked-Queues.png)

### Seven-State Process Model

![Seven-State-Process-Model-img](/assets/img/2024-05-01-OS-4/Seven-State-Process-Model.png)

- Swapping : 메모리가 부족하여 일부를 disk에 전달하는 동작
- Swap될 대상 Process 선정 기준 : blocked process, lower-priority ready process

- Suspended Ready (일시 중단된 준비 상태):

  - 프로세스가 메모리에서 스왑 아웃(Swap-out)되어 디스크에 저장된 상태이다.
  - 프로세스는 필요한 자원을 가지고 있지만, 현재 메모리에 로드되어 있지 않다.
    - 즉, 프로세스가 필요로 하는 자원(예 : 입력 데이터, 파일 등)을 모두 가지고 있는 상태
  - 메모리에 공간이 확보되면 스왑 인(Swap-in)되어 Ready 상태로 전환된다.

- Suspended Blocked (일시 중단된 블로킹 상태):

  - 프로세스가 특정 이벤트나 자원을 기다리는 동시에 메모리에서 스왑 아웃된 상태이다.
  - 프로세스는 필요한 이벤트가 발생하거나 자원을 획득한 후에도 메모리에 로드되어야 Ready 상태로 전환될 수 있다.

- **Seven-State Process Model의 실제 구현 방식**

  1. 프로세스 제어 블록 (PCB: Process Control Block):

     - 각 프로세스마다 PCB라는 자료구조를 유지한다.
     - PCB에는 프로세스의 상태, 프로세스 식별자(PID), CPU 레지스터 값, 메모리 할당 정보 등 프로세스 관련 정보가 저장된다.

  2. 준비 큐 (Ready Queue)와 대기 큐 (Wait Queue):

     - Ready 상태의 프로세스들은 준비 큐에 저장되고, Waiting 상태의 프로세스들은 대기 큐에 저장된다.
     - 큐는 일반적으로 링크드 리스트나 배열과 같은 자료구조로 구현된다.

  3. 스케줄러 (Scheduler):

     - 운영 체제의 스케줄러는 준비 큐에서 프로세스를 선택하여 CPU를 할당한다.
     - 스케줄링 알고리즘(예: FCFS, SJF, Priority, Round-Robin 등)을 사용하여 프로세스를 선택한다.

  4. 컨텍스트 스위칭 (Context Switching):

     - 프로세스 간의 전환 시, 현재 실행 중인 프로세스의 상태를 저장하고 다음 실행할 프로세스의 상태를 복원하는 과정이다.
     - 컨텍스트 스위칭은 PCB를 사용하여 프로세스의 상태를 저장하고 복원합니다.

  5. 스와핑 (Swapping):
     - 메모리 부족 시, 프로세스를 디스크로 스왑 아웃하고 필요 시 다시 메모리로 스왑 인하는 기술입니다.
     - 스와핑은 프로세스의 상태를 Suspended Ready나 Suspended Blocked로 변경합니다.
     - 스왑 인/아웃은 디스크와 메모리 간의 데이터 전송을 통해 이루어집니다.
