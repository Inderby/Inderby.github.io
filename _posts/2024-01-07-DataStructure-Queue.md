---
title: "Java 자료구조 : Queue"
date: 2024-01-07 16:00:00 +0900
author: excelsiorKim
categories: [CS, DataStructure]
tags: [Queue, java, DataStructure]
keywords: queue
description: java, cs, DataStructure
toc: true
---

### 큐의 구조

- 가장 먼저 넣은 데이터를 가장 먼저 꺼낼 수 있는 구조
  ![queue](/assets/img/2024-01-07-queue/queue.png)
- 이를 흔히 **FIFO**(First-In, First-Out) 방식이라고 부른다.

### 알아둘 용어

- **Enqueue** : 큐에 데이터를 넣는 기능
- **Dequeue** : 큐에서 데이터를 꺼내는 기능
- 사이트에서 시연해보고 싶다면 [여기](https://visualgo.net/en/list)를 클릭

### Java에서 Queue의 계층 구조

![queue-hierarchy](/assets/img/2024-01-07-queue/queue-hierarchy.png)

### Java에서 Queue 자료구조 사용하기

- Java에서는 기본적으로 java.util 패키지에 Queue 클래스를 제공하고 있다
  - Enqueue에 해당하는 기능으로 Queue 클래스에서는 `add(value)` 또는 `offer(value)` 메서드를 제공한다.
  - Dequeue에 해당하는 기능으로 `poll()` 또는 `remove()`메서드를 제공한다.
  - Queue클래스에 데이터 생성을 위해서는 java.util 패키지에 있는 `LinkedList` 클래스를 사용해야 한다.
  - Queue의 대기열 중에서 가장 앞쪽 원소를 확인하고 싶을 땐 `peek()`또는 `element()`를 사용하면 된다.
- 사용 예시

```java
import java.util.LinkedList;
import java.util.Queue;

// 자료형 매개변수를 넣어서, 큐에 들어갈 데이터의 타입을 지정해야 함
Queue<Integer> queue_int = new LinkedList<Integer>(); // Integer 형 queue 선언
Queue<String> queue_str = new LinkedList<String>(); // String 형 queue 선언

// 데이터 추가는 add(value) 또는 offer(value) 를 사용함.
queue_int.add(1);
queue_int.offer(2);

// Queue 인스턴스를 출력하면, 해당 큐에 들어 있는 아이템 리스트가 출력됨
System.out.print(queue_int)
//출력 : [1, 2]

// poll() 은 큐의 첫 번째 값 반환, 해당 값은 큐에서 삭제. 지금 상황에서는 1을 반환함
queue_int.poll();
queue_int.remove();
```

### 각 행위들의 시간 복잡도

- 아래에서 언급하는 단어들은 특정 함수명이 아닌 개념적인 행위를 의미한다.
- add : O(1)
- remove : O(1)
- get : O(n)
- Contains : O(n)

### 동일한 동작에 대해 함수가 두 개씩 있는 이유

- `add(e), remove(), element()`의 경우 정상적인 동작을 수행할 수 없을 때(예를 들어 remove를 하려고 하는데 queue가 비어있을 경우) `Exception`을 throw한다.
- `offer(e), poll(), peek()`의 경우는 `Exception`대신 `null 또는 false`를 반환한다.
- 때문에 코드를 작성하는 목적에 맞게 method set를 선택하여 사용할 수 있게 하도록 두 개씩 있는 것 같다.
- fixed-capacity queue의 경우 insert의 실패가 정상적인 동작일 수 있기 때문에 `offer(e)`를 쓰는 것이 바람직할 것 같다.

### 참고

- 큐가 많이 쓰이는 곳
  - 멀티 태스킹을 위한 프로세스 스케줄링 방식을 구현하기 위해 많이 사용됨
- 다양한 큐의 종류
  - PriorityQueue
  - ConcurrentLinkedQueue
  - ArrayBlockingQueue
  - LinkedBlockingQueue
  - PriorityBlockingQueue
  - DelayQueue
  - ArrayDeque
  - LinkedBlockingDeque
  - 위의 큐들은 하나 하나 다룰 예정이다
