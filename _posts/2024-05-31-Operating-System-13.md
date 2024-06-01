---
title: [Operating System.13 Virtual Memory]
author: excelsiorKim
date: 2024-05-31 15:19:03 +0900
categories: [CS, OS]
tags: [CS, OS, OperatingSystem]
keywords: [CS, OS, OperatingSystem]
description: 오퍼레이팅 시스템(운영체제) 공부
toc: true
toc_sticky: true
---

# Virtual Memory

![Virtual-Memory](/assets/img/2024-05-31-OS-13/Virtual-Memory.png)

- Knuth's estimate : 10%의 code가 전체 실행의 90%를 차지한다.
- 때문에 locality를 활용하여 virtual memory를 관리하기 위한 Policy를 정한다.
- 장점 : multi programming의 정도를 높일 수 있다. 메모리 크기에 제약을 벗어날 수 있다.

### Virtual Memory의 필요성

- 물리적 메모리 제약 극복 : 물리 메모리보다 큰 주소 공간을 프로그램에게 제공할 수 있다.
- 메모리 관리 효율성 향상 : 프로그램의 실행에 필요한 부분만 물리 메모리에 로드한다.
- 프로세스 간 메모리 보호 : 각 프로세스에게 독립적인 가상 주소 공간을 제공한다.
- 프로그램 실행 유연성 : 프로그램의 주소 공간이 연속적일 필요가 없다.
- 멀티 프로그래밍 지원 : 여러 프로그램을 동시에 실행할 수 있는 독립된 환경을 제공한다.

## Policy의 종류

![Policy](/assets/img/2024-05-31-OS-13/Policy.png)

### Fetch Policy

- 메인 메모리에 Page를 언제 옮겨 놓을지 결정하는 것
- Demand Paging : Page를 필요로 할 때 그 페이지를 메모리에 포함시키는 방식
  - 처음 시점에 Page fault가 불가피하다. 하지만 시간이 지날수록 Fault 확률이 줄어든다.
- Prepaging : 프로세스가 실제로 페이지를 요청하기 전에 미리 페이지를 메모리로 가져오는 방식이다.
  - 프로세스의 실행 패턴이나 이전 실행 기록을 분석하여 앞으로 필요할 것으로 예상되는 페이지를 미리 메모리에 로드한다.
  - 그러나 Prepaging은 예측이 잘못될 경우 불필요한 메모리 사용과 오버헤드를 발생시킬 수 있다.

### Placement Policy

- 이전 장에서 학습했던 First-fit, Next-fit, Best-fit을 말함.
- Segmentation System에서는 중요하지만, Paging System에서는 frame의 크기가 fix되기 때문에 외부 단편화가 없으므로 의미 없다.

### Replacement Policy

- 가용 영역이 더이상 없을 경우 교체가 필요한데 이러한 교체 대상을 지정하는 것
- 가까운 미래에 가장 안쓰일 것 같은 것을 고름(History를 보고 결정한다)
- Frame Locking : 몇몇 frame들은 locking하여 교체 대상에서 제외될 수 있다.
- 정책들의 평가 척도 : Page fault 수, overhead

### Optimal Page Replacement

- 구현할 수 없는 최적의 교체 알고리즘(미래를 알고 있다는 가정이 들어간 것)
- ex : 4개의 frame을 가지고 있는 process
  ![Optimal-Page-Replacement](/assets/img/2024-05-31-OS-13/Optional-Page-Replacement.png)
  - initial fault 4개를 제외한 2개의 추가적인 page fault만 발생함.

### FIFO(First-In First-Out)

![FIFO](/assets/img/2024-05-31-OS-13/FIFO.png)

- 위의 이미지에 경우 6번의 추가적인 fault가 발생했다
- 먼저 들어와 있던 Page는 먼저 Replace되는 방식
- 단점 : 오래전에 들어왔지만 hot page일 때는 반복적으로 out된다.
- Belady's anomaly 현상 존재
  - page를 저장할 수 있는 frame이 증가 했음에도 page fault수가 증가하는 현상

### LRU(Least Recently Used)

![LRU](/assets/img/2024-05-31-OS-13/LRU.png)

- 위의 이미지를 보면 4번의 추가 fault가 발생했다.
- 최근에 적게 사용된 것을 victim으로 선정하는 알고리즘
- 단점 : 실제 구현하는데 있어서 page 주소를 유지하고, 모든 reference를 업데이트 해야하는 비용이 든다.

### Clock(Second Chance)

![Clock](/assets/img/2024-05-31-OS-13/Clock.png)

- 환경 buffer를 활용한다
- Page들이 사용될때 마다 reference bit를 사용해 1로 세팅한다.
- moving pointer를 사용해 reference bit가 1이면 0으로 세팅, 원래 0이면, victim으로 선정한다.
- Page가 initial 될때 1로 세팅
- 찾는데 오래 걸린다는 것은 메모리가 부족하다는 것이다. 금방찾는것은 메모리 낭비가 심하다는 것이다.

### 4가지 성능 비교

![Compare](/assets/img/2024-05-31-OS-13/Compare.png)

## Clean Policy

- 변경된 Page를 언제 disk에 기록할지 결정하는 정책
- Page-in, Page-out이라는 2가지 페이지 이동이 필요
- dirty-bit를 이용해 수정되지 않은 페이지는 불피룡한 Page-out을 하지 않도록 하는 방법
- Pre-Cleaning : disk 부하가 적을 때, Replace Policy에 의해 교체되기 전에 사전에 write 하는 방식
  - 장점 : 페이지 교체 시 디스크 쓰기 지연이 발생하지 않는다.
  - 단점 : 메모리에 남아 있기 때문에 중복적인 I/O가 발생할 경우 이전에 write한 것은 무용지물이 된다.
- Demand Cleaning : write을 미뤄놨다가 실제로 replace될 때 Disk에 Write한다.
  - 장점 : 붎필요한 Cleaning Operation을 줄일 수 있다.
  - 페이지 교체 시 디스크 쓰기 지연이 발생할 수 있으며, 이로 인해 페이지 폴트 처리 시간이 늘어날 수 있다.

### Page Buffer

- 2번의 Page transfer로 인해 응답성이 떨어지는 것을 막고자 생긴 기법
- 디스크와 메모리 사이에서 페이지를 임시로 저장하는 버퍼이다.
- Page Buffer는 페이지 입출력 성능을 향상시키고, 디스크 액세스를 최소화하는 역할을 한다.
- 페이지 캐싱:

  - Page Buffer는 최근에 액세스된 페이지를 메모리에 캐싱한다.
  - 페이지가 Page Buffer에 존재하면, 디스크에서 페이지를 읽어오는 대신 Page Buffer에서 직접 페이지를 가져올 수 있다.
  - 이를 통해 디스크 액세스 횟수를 줄이고, 페이지 접근 속도를 향상시킬 수 있다.

- 쓰기 버퍼링:

  - Page Buffer는 변경된 페이지를 즉시 디스크에 쓰지 않고, 일정 시간 동안 버퍼에 저장한다.
  - 이를 통해 여러 페이지에 대한 쓰기 작업을 묶어서 한 번에 처리할 수 있다.
  - 쓰기 버퍼링은 디스크 쓰기 횟수를 줄이고, 쓰기 성능을 향상시킨다.

- 지연 쓰기(Write-Back):

  - Page Buffer는 변경된 페이지를 즉시 디스크에 쓰지 않고, 일정 시간 동안 버퍼에 유지한다.
  - 페이지가 다시 액세스되면 버퍼에서 직접 읽어올 수 있다.
  - 변경된 페이지는 버퍼에서 제거되거나, 시스템이 유휴 상태일 때 디스크에 쓴다.
  - 지연 쓰기는 불필요한 디스크 쓰기를 줄이고, 시스템 성능을 향상시킨다.

- 페이지 교체와의 상호작용:
  - Page Buffer는 페이지 교체 알고리즘과 상호작용한다.
  - 페이지 교체 알고리즘은 Page Buffer에 있는 페이지 중 교체 대상을 선택한다.
  - 선택된 페이지가 변경되었다면, Page Buffer는 해당 페이지를 디스크에 쓰고 메모리에서 제거한다.

### Enhance Second-Chance

- 기존의 Second-Chance 알고리즘에서 modify bit를 추가로 고려하여 계산하는 방식
- (reference bit, modify bit)의 경우
- (0,0) neither recently used nor modified
  - best page to replace
- (0,1) not recently used but modified
  - needs write-out (“dirty” page) ➔ lead to additional I/O
- (1,0) recently used but “clean”
  - probably used again soon
- (1,1) recently used and modified
  - used soon, needs write-out

### Thrashing

![Thrashing](/assets/img/2024-05-31-OS-13/Thrashing.png)

- 가상 메모리 시스템에서 발생하는 성능 저하 현상 중 하나로, 과도한 페이지 폴트(Page Fault)로 인해 프로세스의 실행이 지연되는 상태를 말한다.
- Thrashing이 발생하면 시스템은 페이지 교체에 대부분의 시간을 소비하게 되며, 실제 작업은 거의 진행되지 않는다.
- 원인
  - 부족한 메모리 자원:
    - Thrashing은 시스템의 메모리 자원이 프로세스의 요구량보다 부족할 때 발생한다.
    - 프로세스들이 실행되기 위해 필요한 페이지들을 메모리에 올리려고 하지만, 메모리 용량이 충분하지 않으면 페이지 폴트가 빈번히 발생한다.
  - 과도한 페이지 폴트:
    - Thrashing 상태에서는 프로세스들이 자주 페이지 폴트를 일으키게 된다.
    - 페이지 폴트가 발생하면 운영체제는 디스크에서 필요한 페이지를 메모리로 로드해야 한다.
    - 그러나 메모리 부족으로 인해 로드된 페이지는 곧바로 교체되어야 하므로, 다시 페이지 폴트가 발생하는 상황이 반복된다.
- 문제점

  - CPU 이용률 저하:
    - Thrashing 상태에서는 프로세스들이 페이지 폴트를 처리하는 데 대부분의 시간을 소비한다.
    - CPU는 페이지 교체와 관련된 작업에 매우 많은 시간을 할애하게 되므로, 실제 작업을 수행하는 데 사용되는 CPU 시간은 크게 감소한다.
    - 이로 인해 전체 시스템의 성능이 크게 저하된다.
  - 프로세스 실행 지연:

    - Thrashing으로 인해 프로세스들은 페이지 폴트를 기다리는 데 많은 시간을 소비한다.
    - 프로세스의 실행이 지연되고, 응답 시간이 길어지며, 사용자에게 시스템이 멈춘 것처럼 느껴질 수 있다.

  - 시스템 자원 낭비:
    - Thrashing 상태에서는 디스크 I/O와 CPU 시간이 페이지 교체에 과도하게 사용된다.
    - 이는 시스템 자원의 낭비를 초래하며, 다른 프로세스의 성능에도 부정적인 영향을 미친다.

## Resident Set Policy

- 가상 메모리 시스템에서 프로세스의 메모리 상주 집합(Resident Set)을 관리하기 위한 정책이다.
- Resident Set은 프로세스가 현재 메모리에 상주하는 페이지들의 집합을 의미한다.
- 프로세스의 메모리 요구량과 시스템의 메모리 자원을 고려하여 프로세스의 Resident Set을 동적으로 조정한다.

- 목표

  - 메모리 사용 효율성 향상:
    - Resident Set Policy는 프로세스의 실제 메모리 요구량에 맞게 Resident Set의 크기를 조정한다.
    - 불필요한 페이지를 메모리에서 제거하고, 필요한 페이지를 메모리에 유지함으로써 메모리 사용 효율성을 높인다.
  - 페이지 폴트 최소화:

    - Resident Set Policy는 프로세스의 작업 집합(Working Set)을 고려하여 Resident Set을 관리한다.
    - 작업 집합은 프로세스가 일정 시간 동안 자주 접근하는 페이지들의 집합을 의미한다.
    - Resident Set에 작업 집합에 속하는 페이지들을 유지함으로써 페이지 폴트를 최소화할 수 있다.

  - Thrashing 방지:
    - Resident Set Policy는 프로세스 간의 메모리 경쟁을 관리하여 Thrashing을 방지한다.
    - 프로세스의 Resident Set 크기를 적절히 조정하여 메모리 부족으로 인한 과도한 페이지 폴트를 방지한다.

### Resident Set Policy의 종류

- Fixed Resident Set Size:

  - 프로세스의 Resident Set 크기를 고정된 값으로 설정한다.
  - 프로세스는 지정된 크기의 Resident Set을 메모리에 상주시킬 수 있다.
  - 메모리 부족 상황에서는 페이지 교체 알고리즘을 사용하여 Resident Set 내의 페이지를 교체한다.

- Variable Resident Set Size:

  - 프로세스의 Resident Set 크기를 동적으로 조정한다.
  - 프로세스의 메모리 요구량과 시스템의 메모리 가용성에 따라 Resident Set 크기를 증가시키거나 감소시킨다.
  - 프로세스의 작업 집합을 고려하여 Resident Set을 관리한다.

- Working Set Model:
  ![Working-Set-Model](/assets/img/2024-05-31-OS-13/Working-Set-Model.png)
  ![Working-Set-Model-example](/assets/img/2024-05-31-OS-13/Working-Set-Model-Example.png)
  - Wi(t, delta) : 특정 시간 t에 지난 delta 구간에서 reference 되어진 page들의 집합
  - 프로세스의 작업 집합을 기반으로 Resident Set을 관리한다.
  - 작업 집합 크기를 추정하고, 작업 집합에 속하는 페이지들을 Resident Set에 유지한다.
  - 작업 집합의 크기에 따라 Resident Set의 크기를 동적으로 조정한다.

### Page-Fault Frequency

![Page-Fault-Frequency](/assets/img/2024-05-31-OS-13/Page-Fault-Frequency.png)

- Page-Fault 빈도에 따른 upper-bound와 lower-bound를 둔다.
- Page-Fault Frequency가 upper bound보다 높아지면 frame 수를 늘리고, page-fault 수가 lower-bound보다 낮아지면 frame 수를 줄인다.

## Hardware and Control Structures for VM

![Hardware-and-Control-Structure](/assets/img/2024-05-31-OS-13/Hardware-and-Control-Structure.png)

- Page Table Base Register : Table이 커지면서 Table은 메모리에 담기고, Page Table의 Base Address를 저장하는 곳.
- Page Table Entry의 추가 bit

  - Modify bit : 수정 여부
  - Present bit : 해당 Page가 메모리에 있나 없나(부분 적재로 인해 필요한 bit)
    ![Memory-Management-Format](/assets/img/2024-05-31-OS-13/Memory-Management.png)

    - Combined 장점
      - 최종적으로 Page 단위로 구분하기 때문에 외부 단편화가 없음
      - Page안에서 논리적으로 구분되기 때문에 Protection이나 Sharing 효과적
    - Combined 단점
      - 2개의 table이 필요하고, 복잡성이 올라간다.
      - 조각마다 마다 마지막 Page에서 내부 단편화 발생

### TLB(Translation Lookaside Buffer)

- Page Table이 메모리에 적재됨에 따라 Memory에 두 번 방문해야 하는 비효율성이 발생(PTE 접근에 1번, 실제 메모리 접근에 1번)
- 최근에 참조되었던 Page Table Entry를 저장해놓은 cache
- Virtual address로 접근하기 이전에 MMU에서 TLB를 먼저 접근하여 찾는 데이터가 있으면 바로 Physical Address를 얻음
- 가상 주소를 물리 주소로 빠르게 변환하기 위한 Cache
- Check TLB
  ![Check-TLB](/assets/img/2024-05-31-OS-13/Check-TLB.png)
  - TLB는 해당 정보를 탑재시킨 특정 Processor에게만 유용한 정보 -> Process Switch 시 TLB Flush 비용 발생

### OS for VM - Translation

![Overview](/assets/img/2024-05-31-OS-13/Overview.png)

## Virtual Memory Issue

### Page size

- Page size가 클 경우
  - Page Size Entry의 수가 적어진다.
  - Page Table이 작아지고, 초기 loading은 길어지지만, Locality가 존재한다면 I/O 효율이 좋아진다.
  - 하지만 내부 단편화가 커지는 단점이 존재한다.

### Faster Translation

- 한번의 메모리 접근에 Page fault가 두번 일어날 수 있다.
- 이를 해결하기 위해 TLB를 사용하지만 Processor가 바뀔 때 의미 없는 주소가 되어버린다.
- Solution1. Flush
  - Process가 바뀔 때 모든 valid bit을 0으로 바꾼다.(cold start)
- Solution2. ASID(Address Space ID)
  - TLB를 공유할 수 있도록 PID 정보를 넣는 것

## 더 작은 페이지 테이블을 가지기 위한 기법

1. Combined Paging and Segmentation:
   ![Combined](/assets/img/2024-05-31-OS-13/Combined.png)

   - Combined Paging and Segmentation은 가상 메모리 관리를 위해 페이징과 세그멘테이션을 결합한 기법이다.
   - 프로세스의 주소 공간은 세그먼트로 나누어지고, 각 세그먼트는 다시 페이지로 분할된다.
   - 가상 주소는 세그먼트 번호와 세그먼트 내에서의 페이지 번호, 페이지 내에서의 오프셋으로 구성된다.
   - 주소 변환 과정에서 세그먼트 테이블과 페이지 테이블이 함께 사용된다.
   - 세그먼테이션은 프로그램의 논리적 구조를 반영하고, 페이징은 물리 메모리 관리를 효율적으로 수행한다.
   - 장점 : 메모리 보호, 공유, 동적 할당 등을 제공하며, 페이징의 이점과 세그먼테이션의 이점을 모두 활용할 수 있다.
   - 단점으로는 복잡한 메모리 관리 구조와 추가적인 메모리 오버헤드가 있다.

2. Hierarchical Paging:
   ![Hierarchy](/assets/img/2024-05-31-OS-13/Hierarchy.png)

   - Hierarchical Paging은 다단계 페이지 테이블을 사용하여 가상 메모리를 관리하는 기법이다.
   - 가상 주소 공간을 여러 단계의 페이지 테이블로 나누어 관리한다.
   - 가상 주소는 페이지 번호와 오프셋으로 구성되며, 페이지 번호는 다시 여러 단계로 나누어진다.
   - 각 단계의 페이지 테이블은 다음 단계의 페이지 테이블을 가리키는 엔트리를 가지고 있다.
   - 주소 변환 과정에서는 최상위 단계의 페이지 테이블부터 시작하여 순차적으로 다음 단계의 페이지 테이블을 탐색한다.
   - 최종 단계의 페이지 테이블에서 실제 물리 메모리 주소를 얻을 수 있다.
   - Hierarchical Paging은 페이지 테이블의 크기를 줄일 수 있으며, 메모리 사용 효율성을 높일 수 있다.
   - 단점으로는 주소 변환 과정에서 여러 단계의 페이지 테이블을 탐색해야 하므로 성능 오버헤드가 발생할 수 있다.

3. Inverted Page Table:
   ![Inverted](/assets/img/2024-05-31-OS-13/Invert.png)
   - Inverted Page Table은 기존 페이지 테이블과는 달리, 시스템 전체에 대해 하나의 페이지 테이블만 유지하는 기법이다.
   - 페이지 테이블의 각 엔트리는 물리 메모리의 페이지 프레임 번호와 해당 페이지를 소유한 프로세스의 식별자를 저장한다.
   - 가상 주소 변환 시, 페이지 테이블을 검색하여 해당 가상 주소를 가진 프로세스의 페이지 프레임 번호를 찾는다.
   - Inverted Page Table은 페이지 테이블의 크기를 물리 메모리의 크기에 비례하도록 유지할 수 있다.
   - 장점 : 페이지 테이블의 크기를 줄일 수 있으며, 시스템 전체의 메모리 관리가 간소화된다.
   - 단점 : 주소 변환 과정에서 페이지 테이블을 검색해야 하므로 성능 오버헤드가 발생할 수 있다.
     - 또한 공유 메모리와 같은 기능을 지원하기 위해서는 추가적인 자료구조가 필요할 수 있다.
