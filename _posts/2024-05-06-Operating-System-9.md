---
title: [Operating System.9 Synchronization 1]
author: excelsiorKim
date: 2024-05-06 17:19:03 +0900
categories: [CS, OS]
tags: [CS, OS, OperatingSystem]
keywords: [CS, OS, OperatingSystem]
description: 오퍼레이팅 시스템(운영체제) 공부
toc: true
toc_sticky: true
---

### Synchronization이란?

- 멀티스레딩 환경에서 여러 스레드가 공유 자원에 동시에 접근할 때 발생할 수 있는 문제를 해결하기 위한 메커니즘이다.
- Synchronization의 목적
  - 데이터 무결성 보장 : 여러 스레드가 공유 자원을 동시에 접근하고 수정할 때, 데이터의 일관성과 무결성을 유지한다.
  - 경쟁 상태(race condition) 방지 : 두 개 이상의 스레드가 공유 자원에 동시에 접근하여 예측할 수 없는 결과를 초래하는 상황을 방지한다.
  - 상호 배제(Mutual Exclusion) 보장 : 한 스레드가 공유 자원을 사용하는 동안 다른 스레드의 접근을 막아 동점적으로 사용할 수 있도록 한다.

### Race Condition

- shared resource에 접근하는 스레드들의 결과가 비결정적이고, 재현 불가능할 때를 말함.
  - 즉, 실행 결과가 타이밍에 의해 결정되는 상태를 말함.
- 멀티 스레드의 경우 CPU scheduler에 의한 interleaved로 인해 발생한다.
- 스레드 수와 iteration의 수가 증가할 수록 이러한 race condition의 확률이 올라간다.
- single operation이라 하더라도 multi-instruction을 가질 수 있다.
  - ex : `++` operation은 Load, Add, Store이라는 세가지 instruction으로 이뤄져있다.

### Critical resource(임계자원, shared resource)

- 한 번에 하나의 thread만 사용할 수 있는 자원
- 메모리 영역을 대상으로 하는 단어

### Critical Section & Mutual Exclusion

- **critical section(임계 영역, critical region)** : critical resource를 접근하는 코드 조각들
- 코드 영역을 대상으로 하는 단어

- **Mutual Exclusion(상호 배제)** : critical section에 한 번에 하나의 thread만 진입할 수 있도록 하는 행위

### Critical Section Problem

- 3가지 조건을 만족해야 멀티 스레드를 사용하기 적합한 상황이 된다.

  - Mutual Exclusion(상호 배제)
  - Progress(진행 조건) : 알고리즘적 오류로 서로 눈치만 보는 상황이 없어야 됨
  - Bounded waiting(한정 대기) : 대기하면 나머지 쓰레드들이 언젠간 실행되어야 함

- 틀린 코드 1
  ![Naive-approach1-img](/assets/img/2024-05-06-OS-9/naive-approach1.png)
- 틀린 코드 2
  ![Naive-approach2-img](/assets/img/2024-05-06-OS-9/naive-approach2.png)
- 위의 두 코드들은 race condition이 될 가능성을 내포한 코드이다.
- Peterson's
  ![Peterson's-img](/assets/img/2024-05-06-OS-9/Peterson-approach.png)
  - busy waiting, re-ordering 문제가 발생한다.
  - busy waiting : Spin Lock 기법이라고도 불리며, 스레드가 공유 자원을 획득할 때까지 계속해서 루프를 돌며 자원의 가용 여부를 확인한다.

### Disabling Interrupts

- Uni process에서만 사용할 수 있는 방법
  ![Disabling-Interrupt-img](/assets/img/2024-05-06-OS-9/Disabling-Intr.png)
- 장점 : 단순함, 확실함
- 단점 : Interrupt의 장점을 해침, 보안적 취약점이 될 수 있음

### Compare And Swap instruction(CAS)

- disabling interrupt는 멀티 프로세서 환경에서 사용할 수 없기 때문에 생겨난 hardware적인 지원
- lock 메커니즘을 지원하기 위해 CAS라는 추가적인 instruction을 제공함
  ![CAS-img](/assets/img/2024-05-06-OS-9/CAS.png)
- 주어진 값과 메모리 주소에 저장된 값을 비교하여 같을 경우 해당 값을 새롭게 수정한 뒤 기존 의 값을 반환하는 형식.
- 예를 들어 lock.flag의 값이 0이면 1로 바꾼 뒤, 0을 반환, lock.flag값이 원래 1이었으면 무한 반복 수행
- 장점 : 단순함, single과 multi processor 둘 다 사용 가능한 기능이다.
- 단점 : busy waiting, fairness가 떨어짐(타이밍에 의해 결정됨) -> starvation 발생 가능

### Semaphore

- 공유 자원에 대한 접근 제어와 스레드 간의 동기화를 위해 사용되는 메커니즘이다.
- 내부적으로 카운터를 가지고 있으며(변수 1개가 필요, 동시에 사용 가능한 자원 수를 나타내는 변수), 스레드는 Semaphore를 통해 자원을 획득하고 해제한다.
- `P()` : `semWait()`이며 내부적인 카운터를 1 감소 시킨다.(lock에 해당 하는 행위) Semaphore의 값이 0이면 스레드는 Semaphore 값이 증가할 때까지 블로킹(대기) 상태가 된다.
- `S()` : `semSignal()`이며 내부적으로 카운터를 1 증가 시킨다.(unlock에 해당 하는 행위) 대기 중인 스레드가 있다면 그 중 하나를 깨워서 실행 가능한 상태로 만든다.
  ![Semaphore-img](/assets/img/2024-05-06-OS-9/Semaphore.png)
- Busy waiting을 피하기 위해 semaphore를 기다리고 있는 프로세스가 저장된 queue를 사용해 구현 됐다.
- Binary Semaphore : 변수를 0 또는 1로만 상뇽하여 lock, unlock을 표시, mutex lock으로 알려짐
  ![Binary-Semaphore-img](/assets/img/2024-05-06-OS-9/Binary-Semaphore.png)
  - Strong Semaphore이다.
    - FIFO와 같이 순서가 보장되는 것
- Counting Semaphore : 변수의 값이 제한 없음, general semaphore라고 알려짐.
  ![Counting-Semaphore-img](/assets/img/2024-05-06-OS-9/Counting-Semaphore.png)
