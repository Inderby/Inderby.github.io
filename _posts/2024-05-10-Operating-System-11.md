---
title: [Operating System.11 Deadlock]
author: excelsiorKim
date: 2024-05-10 23:19:03 +0900
categories: [CS, OS]
tags: [CS, OS, OperatingSystem]
keywords: [CS, OS, OperatingSystem]
description: 오퍼레이팅 시스템(운영체제) 공부
toc: true
toc_sticky: true
---

### Priority Inversion(우선 순위 역전)

- Priority Inversion(우선순위 역전)은 병행 프로그래밍에서 발생할 수 있는 동기화 문제 중 하나이다.
- 이 문제는 낮은 우선순위의 태스크가 높은 우선순위의 태스크보다 먼저 실행되거나 높은 우선순위의 태스크를 블로킹하는 상황을 말한다.

1. Priority Inversion의 개념:

   - 우선순위가 높은 태스크가 우선순위가 낮은 태스크에 의해 블로킹되는 현상이다.
   - 우선순위가 낮은 태스크가 공유 자원을 점유하고 있을 때, 우선순위가 높은 태스크가 해당 자원을 요청하면 우선순위 역전이 발생한다.
   - 우선순위가 높은 태스크는 우선순위가 낮은 태스크가 공유 자원을 해제할 때까지 대기해야 한다.

2. Priority Inversion의 발생 원인:

   - 공유 자원에 대한 동기화 문제: 우선순위가 낮은 태스크가 공유 자원을 점유하고 있을 때, 우선순위가 높은 태스크가 해당 자원을 요청하면 우선순위 역전이 발생한다.
   - 부적절한 동기화 메커니즘 사용: 우선순위를 고려하지 않는 동기화 메커니즘(예: 뮤텍스)을 사용할 때 우선순위 역전이 발생할 수 있다.
   - 우선순위 기반 스케줄링: 우선순위 기반 스케줄링에서는 우선순위가 높은 태스크가 우선적으로 실행되어야 하지만, 우선순위 역전으로 인해 실행이 지연될 수 있다.

3. Priority Inversion의 문제점:

   - 시스템의 실시간성 저하: 우선순위가 높은 태스크가 블로킹되면 시스템의 실시간 응답성이 저하된다.
   - 성능 저하: 우선순위 역전으로 인해 중요한 태스크의 실행이 지연되어 전체 시스템의 성능이 저하될 수 있다.
   - 불필요한 자원 점유: 우선순위가 낮은 태스크가 공유 자원을 오랫동안 점유하면 다른 태스크들이 자원을 사용하지 못하고 대기하게 된다.

4. Priority Inversion의 해결 방법:

   - Priority Inheritance Protocol (PIP):
     - 우선순위가 낮은 태스크가 공유 자원을 점유할 때, 해당 자원을 요청한 우선순위가 높은 태스크의 우선순위를 상속받는다.
     - 우선순위를 상속받은 태스크는 우선순위가 높아져서 빠르게 자원을 해제하고 우선순위가 높은 태스크에게 자원을 넘겨줄 수 있다.
   - Priority Ceiling Protocol (PCP):
     - 공유 자원마다 우선순위 상한(Priority Ceiling)을 설정한다.
     - 태스크가 공유 자원을 요청할 때, 해당 자원의 우선순위 상한보다 높은 우선순위를 가진 태스크만 자원을 획득할 수 있다.
     - 이를 통해 우선순위 역전을 방지하고 데드락을 회피할 수 있다.
   - Priority Boosting:
     - 우선순위가 낮은 태스크가 공유 자원을 해제할 때까지 해당 태스크의 우선순위를 일시적으로 높인다.
     - 우선순위를 높인 태스크는 빠르게 자원을 해제하고 우선순위가 높은 태스크에게 자원을 넘겨줄 수 있다.

5. 실제 사례:
   - Mars Pathfinder의 Priority Inversion 문제:
     - NASA의 Mars Pathfinder 프로젝트에서 우선순위 역전으로 인한 시스템 재부팅 문제가 발생했다.
     - 우선순위가 낮은 태스크가 공유 자원을 점유하고 있어 우선순위가 높은 태스크가 블로킹되었다.
     - 이로 인해 시스템이 응답하지 않는 상태가 되어 재부팅을 해야 했다.
     - 이 문제는 Priority Inheritance Protocol을 적용하여 해결되었다.

- Priority Inversion은 실시간 시스템이나 임베디드 시스템에서 특히 중요한 문제이다. 시스템의 실시간성과 안정성을 보장하기 위해서는 우선순위 역전을 방지하고 적절한 해결 방법을 적용해야 한다.

### Deadlock

- 데드락(Deadlock)은 병행 프로그래밍에서 두 개 이상의 프로세스나 스레드가 서로 상대방의 작업이 끝나기를 기다리며 무한히 블로킹되는 상황을 말한다.
- 데드락이 발생하면 관련된 프로세스들은 더 이상 진행할 수 없게 되며, 시스템 자원을 점유한 채로 무한정 대기하게 된다.
  ![deadlock-graph-img](/assets/img/2024-05-10-OS-11/Deadlock-graph.png)

1. 데드락의 개념:

   - 두 개 이상의 프로세스나 스레드가 서로 상대방의 자원을 점유한 채로 무한히 대기하는 상황이다.
   - 각 프로세스는 자신이 필요로 하는 자원을 얻기 위해 대기하지만, 다른 프로세스가 이미 해당 자원을 점유하고 있어 진행할 수 없게 된다.
   - 프로세스들은 서로의 자원을 해제하지 않고 계속 대기하므로 교착 상태에 빠지게 된다.

2. 데드락의 발생 조건 (Coffman 조건):

   - 상호 배제(Mutual Exclusion): 자원은 한 번에 한 프로세스만 사용할 수 있어야 한다.
   - 점유 대기(Hold and Wait): 프로세스가 이미 점유한 자원을 보유한 채로 다른 자원을 요청하고 대기할 수 있어야 한다.
   - 비선점(No Preemption): 프로세스가 점유한 자원은 프로세스가 자발적으로 해제하기 전까지는 다른 프로세스가 강제로 가져갈 수 없어야 한다.
   - 순환 대기(Circular Wait): 두 개 이상의 프로세스가 자원을 점유한 채로 서로 다른 프로세스가 점유한 자원을 요청하며 대기하는 순환 구조가 존재해야 한다.

3. 데드락의 문제점:

   - 시스템 자원의 낭비: 데드락이 발생하면 관련된 프로세스들이 자원을 점유한 채로 무한정 대기하므로 시스템 자원이 낭비된다.
   - 시스템 응답 불가: 데드락에 빠진 프로세스들은 더 이상 진행할 수 없으므로 시스템이 응답하지 않는 상태가 된다.
   - 데드락 검출 및 복구의 어려움: 데드락 발생 여부를 판단하고 복구하는 것은 쉽지 않은 작업이다.

4. 데드락의 해결 방법:
   - 데드락 예방(Deadlock Prevention):
     - 데드락 발생 조건 중 하나 이상을 제거하여 데드락이 발생하지 않도록 예방한다.
     - 자원 할당 정책을 수정하거나(hold and wait 방지), 프로세스의 자원 요청 순서(circular wait 방지)를 조정하는 등의 방법을 사용한다.
   - 데드락 회피(Deadlock Avoidance):
     - 자원 할당 시 시스템의 상태를 고려하여 데드락이 발생하지 않는 안전한 상태로만 자원을 할당한다.(safe-state의 기준이 필요)
     - 점유 중인 인스턴스와 점유 예정인 인스턴스를 고려하여 할당한다.
     - Banker's Algorithm과 같은 자원 할당 알고리즘을 사용하여 데드락을 회피한다.
   - 데드락 검출 및 복구(Deadlock Detection and Recovery):
     - 주기적으로 시스템을 모니터링하여 데드락 발생 여부를 검사한다.
     - 데드락이 검출되면 관련된 프로세스 중 일부를 강제 종료하거나 자원을 선점하여 데드락을 해소한다.
   - 무시(Ignorance):
     - 데드락 발생 가능성이 매우 낮거나 데드락 해결 비용이 높은 경우, 데드락을 무시하고 시스템을 구동할 수도 있다.

- 데드락은 병행 프로그래밍에서 주의해야 할 중요한 문제 중 하나입니다.
- 데드락을 예방하고 해결하기 위해서는 시스템 설계 단계에서부터 데드락 발생 가능성을 고려하고, 적절한 동기화 메커니즘과 자원 할당 정책을 사용해야 한다.
- 또한, 데드락 검출 및 복구 메커니즘을 구현하여 데드락 발생 시 신속하게 대응할 수 있어야 한다.

### Resource Allocation Graphs

- 리소스의 종류
  - Reusable Resource : 재사용 가능한 자원
    - CPU, 메모리, 각종 디바이스
  - Consumable Resource : 소비되는 자원 : 생성될 수 있고 파괴될 수 있는 자원 - signal, message, interrupt
    ![Resource-Allocation-Graph-img](/assets/img/2024-05-10-OS-11/Resource-Allocation-graph.png)
  - 왼쪽의 그래프와 같이 순환 구조를 가지면서 single 인스턴스일 경우 deadlock의 위험이 있고, 오른쪽과 같은 경우 deadlock의 위험이 없다.

### Dining Philosopher Problem

- Dining Philosopher Problem은 병행 프로그래밍에서 동기화와 데드락을 설명하기 위해 자주 사용되는 고전적인 문제이다.
- 이 문제는 Edsger W. Dijkstra가 1965년에 처음 제시했으며, 공유 자원의 할당과 프로세스 간의 상호 작용을 추상화한 시나리오를 다룬다.
  ![Dining-Philosopher-img](/assets/img/2024-05-10-OS-11/Dining-Philosopher.png)

1. Dining Philosopher Problem의 개념:

   - 원형 식탁에 n명의 철학자들이 앉아 있다.
   - 각 철학자 사이에는 포크가 하나씩 놓여 있다.
   - 철학자들은 생각하는 것과 식사하는 것을 반복한다.
   - 식사를 하기 위해서는 왼쪽과 오른쪽에 있는 포크를 모두 들어야 한다.
   - 포크는 한 번에 한 명의 철학자만 사용할 수 있다.

2. 문제 상황:

   - 모든 철학자가 동시에 왼쪽 포크를 집으면 데드락이 발생한다.
   - 각 철학자가 왼쪽 포크를 집은 상태에서 오른쪽 포크를 기다리게 되면, 모든 철학자가 포크를 얻지 못한 채로 무한히 대기하게 된다.
   - 이 상황에서는 어떤 철학자도 식사를 할 수 없게 되며, 시스템이 교착 상태에 빠지게 된다.

3. 해결 방법:

   - Resource Hierarchy (자원 계층 구조) 방식:
     - 각 포크에 고유한 번호를 할당합니다.
     - 철학자들은 항상 번호가 낮은 포크부터 집도록 합니다.
     - 이렇게 하면 순환 대기 조건이 깨지므로 데드락을 예방할 수 있습니다.

   ```java
   public class DiningPhilosophers {
      public static void main(String[] args) throws Exception {
          Philosopher[] philosophers = new Philosopher[5];
          Object[] fork = new Object[philosophers.length];

          for (int i = 0; i < fork.length; i++) {
              fork[i] = new Object();
          }

          for (int i = 0; i < philosophers.length; i++) {
              Object leftFork = fork[i];
              Object rightFork = fork[(i + 1) % fork.length];
   s
              if (i == philosophers.length - 1) {
                  // 마지막 철학자는 순서를 바꿔서 교착 상태를 피한다.
                  philosophers[i] = new Philosopher(rightFork, leftFork);
              } else {
                  philosophers[i] = new Philosopher(leftFork, rightFork);
              }

              Thread t = new Thread(philosophers[i], "Philosopher " + (i + 1));
              t.start();
          }
      }
   }

   class Philosopher implements Runnable {
    private final Object leftFork;
    private final Object rightFork;

    public Philosopher(Object leftFork, Object rightFork) {
        this.leftFork = leftFork;
        this.rightFork = rightFork;
    }

    @Override
    public void run() {
        while (true) {
            // Thinking
            synchronized (leftFork) {
                synchronized (rightFork) {
                    // Eating
                }
            }
        }
    }
   }
   ```

   - Arbitrator (중재자) 방식:
     - 중재자를 두어 포크의 할당을 제어합니다.
     - 철학자가 포크를 요청하면 중재자가 포크의 가용성을 확인하고 할당 여부를 결정합니다.
     - 중재자는 교착 상태를 방지하도록 포크를 할당합니다.

   ```java

   class Philosopher implements Runnable {
    private final Arbitrator arbitrator;
    private final int id;

    public Philosopher(Arbitrator arbitrator, int id) {
        this.arbitrator = arbitrator;
        this.id = id;
    }

    @Override
    public void run() {
        while (true) {
            // Thinking
            arbitrator.requestForks(id);
            // Eating
            arbitrator.releaseForks(id);
        }
    }
   }
   ```

   ```java
     class Arbitrator {
     private final boolean[] forks;

           public Arbitrator(int numPhilosophers) {
               forks = new boolean[numPhilosophers];
           }

           public synchronized void requestForks(int id) {
               while (forks[id] || forks[(id + 1) % forks.length]) {
                   try {
                       wait();
                   } catch (InterruptedException e) {
                       Thread.currentThread().interrupt();
                   }
               }
               forks[id] = true;
               forks[(id + 1) % forks.length] = true;
           }

           public synchronized void releaseForks(int id) {
               forks[id] = false;
               forks[(id + 1) % forks.length] = false;
               notifyAll();
           }

     }
   ```

- Chandy/Misra 알고리즘:

  - 각 철학자는 포크를 요청할 때 다른 철학자에게 메시지를 보냅니다.
  - 포크를 사용할 수 있을 때까지 메시지를 기다립니다.
  - 포크를 사용한 후에는 대기 중인 철학자에게 포크 사용 가능 메시지를 보냅니다.
  - 이 방식은 데드락을 회피하며, 기아 상태도 방지할 수 있습니다.

  ```java
  import java.util.concurrent.BlockingQueue;
  import java.util.concurrent.LinkedBlockingQueue;

  class Philosopher implements Runnable {
      private final int id;
      private final BlockingQueue<Message> queue;
      private final Philosopher[] philosophers;

      public Philosopher(int id, Philosopher[] philosophers) {
          this.id = id;
          this.queue = new LinkedBlockingQueue<>();
          this.philosophers = philosophers;
      }

      @Override
      public void run() {
          while (true) {
              // Thinking
              requestForks();
              // Eating
              releaseForks();
          }
      }

      private void requestForks() {
          philosophers[(id + 1) % philosophers.length].queue.add(new Message(id, State.REQUEST));
          philosophers[(id - 1 + philosophers.length) % philosophers.length].queue.add(new Message(id, State.REQUEST));
          while (!(queue.contains(new Message((id + 1) % philosophers.length, State.REPLY))
                  && queue.contains(new Message((id - 1 + philosophers.length) % philosophers.length, State.REPLY)))) {
              try {
                  queue.take();
              } catch (InterruptedException e) {
                  Thread.currentThread().interrupt();
              }
          }
      }

      private void releaseForks() {
          philosophers[(id + 1) % philosophers.length].queue.add(new Message(id, State.REPLY));
          philosophers[(id - 1 + philosophers.length) % philosophers.length].queue.add(new Message(id, State.REPLY));
      }
  }

  class Message {
      private final int id;
      private final State state;

      public Message(int id, State state) {
          this.id = id;
          this.state = state;
      }

      // equals and hashCode methods
  }

  enum State {
      REQUEST, REPLY
  }
  ```

- Resource Ordering (자원 순서 지정) 방식:
  - 철학자들이 포크를 집는 순서를 지정합니다.
  - 예를 들어, 홀수 번호의 철학자는 왼쪽 포크부터 집고, 짝수 번호의 철학자는 오른쪽 포크부터 집도록 합니다.
  - 이렇게 하면 순환 대기 조건이 깨지므로 데드락을 예방할 수 있습니다.

```java
class Philosopher implements Runnable {
    private final Object leftFork;
    private final Object rightFork;
    private final int id;

    public Philosopher(Object leftFork, Object rightFork, int id) {
        this.leftFork = leftFork;
        this.rightFork = rightFork;
        this.id = id;
    }

    @Override
    public void run() {
        while (true) {
            // Thinking
            if (id % 2 == 0) {
                synchronized (leftFork) {
                    synchronized (rightFork) {
                        // Eating
                    }
                }
            } else {
                synchronized (rightFork) {
                    synchronized (leftFork) {
                        // Eating
                    }
                }
            }
        }
    }
}
```

- 이외에도 5개의 식탁에 4명만 앉을 수 있게 하는 방식, 두 포크를 동시에 들게 하는 방식 등으로 문제를 해결 할 수도 있다.
- Dining Philosopher Problem은 제한된 자원을 공유하는 프로세스 간의 동기화와 데드락 문제를 잘 보여주는 예시이다.
- 이 문제를 해결하기 위해서는 데드락 발생 조건을 깨뜨리거나, 자원 할당을 적절히 제어하는 방법을 사용해야 한다.

### Resource Allocation Graph Algorithm

- claim edge : request resource를 할 수도 있는 추청된 edge
- assignment edge : 실제로 resource 할당된 상태의 edge
- claim edge가 assignment edge로 바뀌었을 때 **Cycle**을 생성하지 않으면 허용
- 리소스-할당-그래프 알고리즘은 각 리소스 유형의 다수의 인스턴스를 갖는 리소스 할당 시스템에 적용할 수 없다
- 방지 방법
  - Process Initiation Denial : 시스템에 새로운 thread나 process가 진입할 때 maximum number of instance를 가정하고 선언할지 말지 결정
    ![Process-Initiation-Denial-img](/assets/img/2024-05-10-OS-11/processs-Initiation-Denial.png)
    - 지금 가지고 있는 자원(Rj)보다 지금 시작되는 자원(C(n+1)) + 실행중인 자원(Sum of C(ij))이 작아야 새로운 process를 시작.(worst-case 를 가정한 수식이기 때문에 시작 단계에서 최대 요구량을 체크한다. 즉, 비효율적으로 동작할 수 있다.)
  - Resource Allocation Denial(banker's algorithm) : request를 할 때마다 safe-state인지 아닌지 판단하는 것

### Banker's Algorithm

- Banker's Algorithm은 운영 체제에서 deadlock을 방지하기 위해 사용되는 자원 할당 알고리즘 중 하나이다.
- 1965년 Edsger W. Dijkstra가 제안한 이 알고리즘은 프로세스가 자원을 요청할 때, 시스템이 safe state를 유지할 수 있는지 확인하고 자원을 할당한다.

- 주요 개념:

  1. Safe State: 모든 프로세스가 필요한 최대 자원을 할당받고 실행을 완료할 수 있는 상태.
  2. Unsafe State: deadlock이 발생할 가능성이 있는 상태.
  3. Available: 현재 사용 가능한 자원의 수.
  4. Max: 각 프로세스가 필요로 하는 최대 자원의 수.
  5. Allocation: 각 프로세스에 현재 할당된 자원의 수.
  6. Need: 각 프로세스가 추가로 필요한 자원의 수. (Need = Max - Allocation)

- 동작 과정:

  1. 프로세스가 자원을 요청합니다.
  2. 요청된 자원이 Available보다 작거나 같은지 확인합니다.
  3. 요청된 자원이 해당 프로세스의 Need보다 작거나 같은지 확인합니다.
  4. 2, 3 조건을 만족하면, 자원을 가상으로 할당하고 시스템이 safe state인지 확인합니다.
  5. Safe state이면, 자원을 실제로 할당하고 Available과 Allocation을 갱신합니다.
  6. Unsafe state이면, 자원 할당을 보류하고 프로세스는 대기 상태가 됩니다.
     ![Banker-Algorithm-img](/assets/img/2024-05-10-OS-11/Banker-Algorithm.png)

- Banker's Algorithm은 자원 할당 시뮬레이션을 통해 미리 deadlock 가능성을 판단하므로, 시스템을 항상 safe state로 유지할 수 있다.
- 하지만 이 알고리즘은 프로세스의 최대 자원 요구량을 미리 알아야 하므로, 모든 상황에 적용하기 어려울 수 있다. 또한, 자원 할당 시뮬레이션에 오버헤드가 발생할 수 있다.

- 그럼에도 불구하고, Banker's Algorithm은 deadlock 방지를 위한 중요한 이론적 기반을 제공하며, 다양한 시스템에서 응용되고 있다. 특히 제한된 자원을 공유하는 임베디드 시스템이나 실시간 시스템 등에서 유용하게 활용될 수 있다.

### Banker's Algorithm

- 시스템에 A, B, C 세 종류의 자원이 각각 10, 5, 7개씩 있고, P1, P2, P3, P4, P5 다섯 개의 프로세스가 있다고 가정해 봤을 때.
- 각 프로세스의 Max, Allocation, Available, Need는 다음과 같다.

  ```
        A B C
  Max
  P1    7 5 3
  P2    3 2 2
  P3    9 0 2
  P4    2 2 2
  P5    4 3 3

  Allocation
  P1    0 1 0
  P2    2 0 0
  P3    3 0 2
  P4    2 1 1
  P5    0 0 2

  Available
        3 3 2
  ```

- 이 상태에서 P1이 (1, 0, 2)의 자원을 요청한다고 가정해 봤을 때.

  1. 요청된 자원 (1, 0, 2)는 Available (3, 3, 2)보다 작거나 같다.
  2. 요청된 자원 (1, 0, 2)는 Need (7, 4, 3)보다 작거나 같다.

- 따라서 P1에 자원을 가상으로 할당하고, 시스템이 safe state인지 확인한다.

- P1에 자원을 할당한 후의 상태는 다음과 같다.

  ```
  Available
        2 3 0

  Allocation
  P1    1 1 2
  ```

- 이제 모든 프로세스가 필요한 최대 자원을 할당받고 실행을 완료할 수 있는지 확인한다.

- 예를 들어, P1, P3, P4, P2, P5 순서로 프로세스를 실행할 수 있습니다.

1. P1 실행 완료 후, Available은 (3, 4, 2)가 됩니다.
2. P3 실행 완료 후, Available은 (12, 4, 4)가 됩니다.
3. P4 실행 완료 후, Available은 (14, 6, 6)이 됩니다.
4. P2 실행 완료 후, Available은 (16, 6, 6)이 됩니다.
5. P5 실행 완료 후, Available은 (16, 6, 8)이 됩니다.

- 모든 프로세스가 실행을 완료할 수 있으므로, 시스템은 safe state입니다. 따라서 P1에 실제로 자원을 할당하고, Available과 Allocation을 갱신한다.

- 이와 같이 Banker's Algorithm은 자원 할당 요청이 있을 때마다 시뮬레이션을 통해 safe state를 유지할 수 있는지 확인하고, 가능한 경우에만 자원을 할당한다.
