---
title: [Operating System.8 Threads and Synchronization]
author: excelsiorKim
date: 2024-05-05 17:19:03 +0900
categories: [CS, OS]
tags: [CS, OS, OperatingSystem]
keywords: [CS, OS, OperatingSystem]
description: 오퍼레이팅 시스템(운영체제) 공부
toc: true
toc_sticky: true
---

### Process and Thread

- Process : 실행 상태에 있는 프로그램
- 운영체제가 바라보는 프로세스의 단위
  1. 자원 소유권 단위(Memory, I/O device, file)
  2. 실행/스케줄링의 단위(execution sequence -> thread)

### Multi-threading

- single process 내에서 multiple한 execution sequence 자원
  - resource sharing의 장점이 생긴다
- multiple한 execution sequence들이 concurrent하게 진행된다.
  - multi process보다 multi thread가 overhead가 적게 든다.

### Concurrency(동시성)

- 다수의 task들이 동시에 진행하게끔 하는 성질
- parallelism과는 다른 의미이다.
- single core에서는 Interleaving(끼어들기)이라고 부름.(parallelism X)
  ![interleaving-img](/assets/img/2024-05-04-OS-8/interleaving.png)
- multi core에서는 overlapping이라고 부름.(parallelism O)
  ![overlapping-img](/assets/img/2024-05-04-OS-8/overlapping.png)

### Motivation(등장 배경)

- 여러 작업이 있는 Application에서, 다른 작업들이 blocking 되지 않고 계속 진행될 수 있도록 하기 위함.
- 웹 서버의 예시
  ![diagram-img](/assets/img/2024-05-04-OS-8/web-server-diagram.png)
  ![variable-solution-img](/assets/img/2024-05-04-OS-8/variable-solution.png)

### Multithreading

![Multithreading-img](/assets/img/2024-05-04-OS-8/multihreading.png)

- 장점
  - 빠른 응답성 : 프로그램의 일부가 차단되거나 장시간 작업을 수행하는 경우에도 프로그램이 계속 실행되도록 할 수 있다.
  - creation, communication, switching, termination cost 감소 : resource sharing을 통해 virtual memory(data 영역) 공유가 가능하기 때문에 비용이 감소된다.
  - parallel processing : 1개의 process를 여러 개의 thread로 partition 후 multi core에게 thread 단위로 실행 요청하여 성능을 극대화 할 수 있다.

### Amdal's Law

- speed up <= 1/(s+ (1-s)/n)
  - s : 공유 영역
  - n : 멇티 코어의 수
- divide할 수 없는 영역이 존재하기 때문에 process를 여러 개의 작업으로 쪼개도 일정 수준이상으로 속도 향상을 할 수 없는 법칙
- ex : 2개의 프로세서를 16개의 멀티 프로세서로 실행 시킬 때
  ![Example-multithreading-img](/assets/img/2024-05-04-OS-8/example-multi-thread.png)
  - 2 \* 8 = 16 일때 speed up이 최고이지만, 예상과는 다르게 8배 빨라지진 않음
  - 또한 8개 이후로 rescheduling overhead로 인해 오히려 속도가 감소함

### Multithreading을 할 때 고려사항

- 작업 식별 : task들은 서로 독립적이므로 병렬로 실행 가능해야 함.
- 균형 : 작접이 동일한 가치의 동일한 작업을 수행하는지 확인해야 함.
- 데이터 종속성 : 동기화 문제를 해결해야 함.

### Multithread Process Model

![Mutlithreaded-Process-Model-img](/assets/img/2024-05-04-OS-8/Multihreading-Process-Model.png)

- 각 스레드마다 분리된 stack 영역을 갖고 그 외에 heap data, code 영역은 공유한다.
- TCB(Thread COntrol Block)
  - 독립된 실행 흐름이 필요하기 때문에 필요한 데이터 block
  - thread가 생성될 때 별도의 stack과 TCB가 생성된다.
    ![TCB-img](/assets/img/2024-05-04-OS-8/TCB.png)

### Thread Creation

![Thread-creation-img](/assets/img/2024-05-04-OS-8/Thread-Creation.png)

- 기존의 프로세스에서 새로운 프로세스를 생성하는 것보다 내부에서 새로운 스레드를 생성하는게 더 적은 비용이 든다.
- 두 스레드 간에 switching은 더 좋은 성능을 보여준다.

### Multithreading Models

![Mutlithreading-model-img](/assets/img/2024-05-04-OS-8/Mutlithreading-Model.png)

- KLT(Kernel-Level-Thread) : thread management(생성, 스위칭, 터미네이션)을 kernel이 담당한다.
  - 특징 :
    - 커널 공간에서 스레드를 관리하고 스케줄링한다.
    - 커널이 스레드의 생성, 삭제, 전환을 담당한다.
    - 스레드 간의 동기화와 통신은 커널 수준에서 이루어진다.
    - 커널이 KLT를 인식하고 관리한다.
  - 장점 :
    - 한 스레드가 블로킹되어도 다른 스레드는 계속 실행될 수 있다.
    - 다중 프로세서 시스템에서 병렬성을 활용할 수 있다.
    - 커널 자원에 직접 접근할 수 있다.
    - 커널 수준의 스케줄링과 동기화 기능을 사용할 수 있다.
  - 단점 :
    - model switch에 의한 성능 저하
    - 스레드 관리 오버헤드가 크다
    - 사용자 수준에서의 스레드 제어가 제한적이다.
- ULT(User-Level-Thread) : kernel이 모르게 user level에서 threading
  - 특징 :
    - 사용자 공간에서 스레드를 관리하고 스케줄링 한다.
    - 커널의 개입 없이 스레드 생성삭제, 전환이 가능하다
    - 스레드 간의 동기화와 통신은 사용자 수준에서 이루어진다.
    - 커널은 ULT의 존재를 인식하지 못한다.
  - 장점 : 빠르다(model switch 비용이 없음). 확장성이 좋고, 커스터마이징 가능
  - 단점 :
    - 기존의 커널이 관리했을 때의 장점을 fully 이용할 수 없다.
    - kernel에서 block되면 user-level thread는 다 같이 block된다.
    - 다중 프로세서 시스템에서는 병렬성을 활용할 수 없다.
- ULT와 KLT는 각각의 장단점을 가지고 있으므로, 시스템의 요구사항과 특성에 따라 적절한 방식을 선택해야 한다.
- 일반적으로 ULT는 가벼운 스테드 구현에 적합하고, KLT는 커널 자원에 대한 접근이 필요하거나 다중 프로세서 시스템에서의 병렬성이 중요한 경우 사용된다.
- 일부 운영체제에서는 이러한 ULT와 KLT를 혼합한 하이브리드 방식을 사용한다.
- Java에서의 멀티 스레딩은 기본적으로 Kernel level thread이다.
  - 하지만 `ForkJoinPool`과 `CompletableFuture`를 통해 가상스레드(ULT)를 구현하여 사용할 수 있다.

### Posix Threads(Pthreads) Interface

- basic pthread functions
  - thread-management
    - `pthread_create` : create child thread
    - `pthread_exit` : thread termination
    - `pthread_join` : wait for thread termination
    - `pthread_self` : return the calling thread ID
  - 동기화 관련 API
    - `pthread_mutex_lock` : lock critical section
    - `pthread_mutex_unlock` : unlock critical section
    - `pthread_cond_wait` : wait for condition signal
    - `pthread_cond_signal` : wake up one waiting thread

### Thread Synchronization

- race condition이 발생할 수 있기에 동기화 기법이 중요하다.
- race condition : shared resource에 접근하는 스레드들의 결과가 비결정적이고, 재현불가능한 상태.
  - single core에서도 발생 가능하다.
