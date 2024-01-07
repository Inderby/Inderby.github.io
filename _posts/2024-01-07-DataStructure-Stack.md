---
title: "Java 자료구조: Stack"
author: excelsiorKim
date: 2024-01-07 17:38:15 +0900
categories: [CS, DataStructure]
tags: [Stack, java, DataStructure]
keywords: [Stack, java, DataStructure]
description: Stack, java, DataStructure
toc: true
toc_sticky: true
---

### 스택의 구조

- 한쪽 끝에서만 자료를 넣거나 뺄 수 있는 구조
- 가장 나중에 쌓은 데이터를 가장 먼저 빼낼 수 있는 데이터 구조
- LIFO(Last-In, First-Out)방식
  ![stack](/assets/img/2024-01-07-stack/stack.png)

### 알아둘 용어

- **push** : 데이터를 스택에 넣는 것
- **pop** : 데이터를 스택에서 꺼내는 것
- 시연해보고 싶으면 [여기](https://visualgo.net/en/list)를 클릭

### Java에서 Stack의 계층 구조

![stack-hierarchy](/assets/img/2024-01-07-stack/stack-hierarchy.png)

### Java에서의 Stack 자료구조 사용하기

- java.util 패키지에서 Stack 클래스 제공
- java.util.Vector를 extends함
- `push(e)` : 아이템을 Stack에 추가. 추가에 성공한 값을 반환함
- `pop()` : Stack에서 최상단에 있는 아이템을 리턴하고, 해당 아이템은 Stack에서 삭제(비어있는 Stack일 경우 `EmptyStackException` 발생)
- `peek()` : stack에서 최상단에 있는 아이템을 리턴하고, 해당 아이템은 삭제하지 않음(비어있는 Stack일 경우 `EmptyStackException` 발생)
- `empty()` : Stack이 비어있는지 여부를 확인 후 boolean 반환
- `search(e)`: Stack에 인자로 들어온 값이 존재하는지 확인 후 1-based index를 반환, 존재하지 않을 경우 -1을 반환
- 사용 예시

```java
//  java.util.Stack 클래스 임포트
import java.util.Stack;

// 자료형 매개변수를 넣어서, 스택에 들어갈 데이터의 타입을 지정해야 함
Stack<Integer> stack_int = new Stack<Integer>(); // Integer 형 스택 선언

stack_int.push(1);     // Stack 에 1 추가
stack_int.push(2);     // Stack 에 2 추가
stack_int.push(3);     // Stack 에 3 추가

stack_int.pop();       // Stack 에서 데이터 추출 (마지막에 넣은 3이 출력)

stack_int.pop();       // Stack 에서 데이터 추출 (현재 Stack 에 있는 데이터 중, 가장 나중에 넣어진 데이터 출력). 2가 출력
```

### 각 행위들의 시간 복잡도

- 삽입(Push): 맨 위에 데이터를 넣으면 되기 때문에 O(1)이다.
- 삭제(Pop): 맨 위에 데이터를 삭제하면 되기 때문에 O(1)이다.
- 읽기(Peek): 맨 위의 데이터를 읽으면 되기 때문에 O(1)이다.
- 탐색(Search): 맨 위의 데이터부터 하나씩 찾아야 하기 때문에 O(n)이 걸리게 된다.

### 참고

- 스택의 활용
  - 컴퓨터 내부의 프로세스 구조의 함수 동작 방식
- java.util 패키지의 Stack 단점

  ```java
  public class Stack<E> extends Vector<E> {

    public Stack() {
    }

    public E push(E item) {
        addElement(item);

        return item;
    }

    public synchronized E pop() {
    }

    public synchronized E peek() {
    }

    public synchronized int search(Object o) {
    }
  }
  ```

  - 위의 코드에서 보는 것과 같이 `synchronized` 키워드가 붙어 Thread-safe하다는 특징을 갖고 있지만, 멀티 스레드 환경이 아닐 때 사용 한다면 lock을 거는 작업으로 인해 많은 오버헤드가 발생한다.

  ```java
  import java.util.Stack;

  public class Test {
      public static void main(String[] args) {
          Stack<Integer> stack = new Stack<>();
          stack.push(1);
          stack.push(2);
          stack.push(3);

          System.out.println(stack.get(1));            // 해당 인덱스 원소 찾기
          System.out.println(stack.set(1, 1));         // 해당 인덱스에 원소 넣기
          System.out.println(stack.remove(1));         // 해당 인덱스 원소를 삭제
      }
  }
  ```

  - `Vector`를 확장하고 있기 때문에 위의 코드처럼 중간에 데이터를 삽입, 삭제할 수 있다.
  - 마지막으로 스택은 초기 용량을 설정할 수 있는 생성자가 없다 보니 데이터의 삽입을 많이 하게 되면 배열을 복사해야 하는 일이 빈번하게 발생할 수 있다는 단점도 존재

### 단점들에 대한 대안

- `ArrayDeque` 공식문서에 보면 스택구조로 사용하면 Stack 클래스보다 빠르고, 큐 구조로 사용하면 Queue 클래스보다 빠르다.(`ArrayDeque`는 Thread-Safe 하지 않기 때문)
- 때문에 `ArrayDeque`에 대한 자세한 사용 방법을 다룰 예정이다.(고마워 하.하.)
