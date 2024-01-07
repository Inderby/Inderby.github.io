---
title: "Java 자료구조 : (Doubly) Linked List"
author: excelsiorKim
date: 2024-01-07 18:24:38 +0900
categories: [CS, DataStructure]
tags: [LinkedList, CS, DataStructure]
keywords: [LinkedList, CS, DataStructure]
description: LinkedList, CS, DataStructure
toc: true
toc_sticky: true
---

### Linked List 구조

- 연결 리스트라고도 함
- 배열이 순차적으로 연결된 공간에 데이터를 나열하는 데이터 구조라면, Linked List는 떨어진 곳에서 존재하는 데이터를 화살표로 연결해서 관리하는 데이터 구조

### Linked List에서 사용되는 용어

- Node : 데이터 저장 단위(데이터 값, 포인터)로 구성되어 있다.
- Pointer : 각 노드 안에서, 다음이나 이전의 노드와의 연결 정보를 가지고 있는 공간

### 일반적인 Linked List 형태

![linked-list](/assets/img/2024-01-07-Linked-List/linked-list.png)

### Linked List의 장단점

- 장점
  - 미리 데이터 공간을 미리 할당하지 않아도 됨
  - 배열은 미리 데이터 공간을 할당 해야 함
- 단점
  - 연결을 위한 별도 데이터 공간이 필요하므로, 저장공간 효율이 높지 않음
  - 연결 정보를 찾는 시간이 필요하므로 접근 속도가 느림
  - 중간 데이터 삭제시, 앞뒤 데이터의 연결을 재구성해야 하는 부가적인 작업 필요

### Linked List의 데이터 추가 방식

![linked-list-add-node](/assets/img/2024-01-07-Linked-List/linked-list-add-node.png)

- 데이터 삭제는 해당 방식의 역순으로 진행하면 됨

### 구현 코드

```java
public class SingleLinkedList<T> {
    public Node<T> head = null;

    public class Node<T> {
        T data;
        Node<T> next = null;

        public Node(T data) {
            this.data = data;
        }
    }

    public void addNode(T data) {
        if (head == null) {
            head = new Node<T>(data);
        } else {
            Node<T> node = this.head;
            while (node.next != null) {
                node = node.next;
            }
            node.next = new Node<T>(data);
        }
    }

    public void printAll() {
        if (head != null) {
            Node<T> node = this.head;
            System.out.println(node.data);
            while (node.next != null) {
                node = node.next;
                System.out.println(node.data);
            }
        }
    }

    public Node<T> search(T data) {
        if (this.head == null) {
            return null;
        } else {
            Node<T> node = this.head;
            while (node != null) {
                if (node.data == data) {
                    return node;
                } else {
                    node = node.next;
                }
            }
            return null;
        }
    }

    public void addNodeInside(T data, T isData) {
        Node<T> searchedNode = this.search(isData);

        if (searchedNode == null) {
            this.addNode(data);
        } else {
            Node<T> nextNode = searchedNode.next;
            searchedNode.next = new Node<T>(data);
            searchedNode.next.next = nextNode;
        }
    }

    public boolean delNode(T isData) {
        if (this.head == null) {
            return false;
        } else {
            Node<T> node = this.head;
            if (node.data == isData) {
                this.head = this.head.next;
                return true;
            } else {
                while (node.next != null) {
                    if (node.next.data == isData) {
                        node.next = node.next.next;
                        return true;
                    }
                    node = node.next;
                }
                return false;
            }
        }
    }
}
```

- 여기서 끝내기엔 싱거운 감이 있기 때문에 Doubly Linked List도 구현해보자.

### Doubly Linked List의 기본 구조

- 이중 연결 리스트라고도 함
- 장점 : 양방향으로 연결되어 있어 노드 탐색이 양쪽으로 모두 가능
- 위와 같은 장점 때문에 대중적으로 쓰이는 방식이다.
  ![doubly-linked-list](/assets/img/2024-01-07-Linked-List/doubly-linked-list.png)

```java
public class DoubleLinkedList<T> {
    public static void main(String[] args) {
        DoubleLinkedList<Integer> MyLinkedList = new DoubleLinkedList<Integer>();

        MyLinkedList.addNode(1);
        MyLinkedList.addNode(2);
        MyLinkedList.addNode(3);
        MyLinkedList.addNode(4);
        MyLinkedList.addNode(5);
        MyLinkedList.printAll();
        System.out.println("----------------");

        MyLinkedList.insertToFront(3, 2);
        MyLinkedList.printAll();
        System.out.println("----------------");

        MyLinkedList.insertToFront(6, 2);
        MyLinkedList.insertToFront(1, 0);
        MyLinkedList.printAll();
        System.out.println("----------------");

        MyLinkedList.addNode(6);
        MyLinkedList.printAll();
    }

    public Node<T> head = null;
    public Node<T> tail = null;

    public class Node<T> {
        T data;
        Node<T> prev = null;
        Node<T> next = null;

        public Node(T data) {
            this.data = data;
        }
    }

    public void addNode(T data) {
        if (this.head == null) {
            this.head = new Node<T>(data);
            this.tail = this.head;
        } else {
            Node<T> node = this.head;
            while (node.next != null) {
                node = node.next;
            }
            node.next = new Node<T>(data);
            node.next.prev = node;
            this.tail = node.next;
        }
    }

    public void printAll() {
        if (this.head != null) {
            Node<T> node = this.head;
            System.out.println(node.data);
            while (node.next != null) {
                node = node.next;
                System.out.println(node.data);
            }
        }
    }

    public T searchFromHead(T isData) {
        if (this.head == null) {
            return null;
        } else {
            Node<T> node = this.head;
            while (node != null) {
                if (node.data == isData) {
                    return node.data;
                } else {
                    node = node.next;
                }
            }
            return null;
        }
    }

    public T searchFromTail(T isData) {
        if (this.head == null) {
            return null;
        } else {
            Node<T> node = this.tail;
            while (node != null) {
                if (node.data == isData) {
                    return node.data;
                } else {
                    node = node.prev;
                }
            }
            return null;
        }
    }

    public boolean insertToFront(T existedData, T addData) {
        if (this.head == null) {
            this.head = new Node<T>(addData);
            this.tail = this.head;
            return true;
        } else if (this.head.data == existedData) {
            Node<T> newHead = new Node<T>(addData);
            newHead.next = this.head;
            this.head = newHead;
            this.head.next.prev = this.head; // 2021.09.13 추가 (prev 도 연결을 해줘야 함)
            return true;
        } else {
            Node<T> node = this.head;
            while (node != null) {
                if (node.data == existedData) {
                    Node<T> nodePrev = node.prev;

                    nodePrev.next = new Node<T>(addData);
                    nodePrev.next.next = node;

                    nodePrev.next.prev = nodePrev;
                    node.prev = nodePrev.next;
                    return true;
                } else {
                    node = node.next;
                }
            }
            return false;
        }
    }
}
```

### Java 컬렉션 프레임워크의 LinkedList

- `List` interface를 구현하므로, `ArrayList` 클래스와 사용할 수 있는 메소드가 거의 같다.
- LinkedList는 List 자료구조 외에도 Stack이나 Queue 자료구조로서도 이용이 가능하기 때문에 이를 위한 메서드들도 구현되어 있어 내부 메서드 갯수가 컬렉션중에 가장 많다고 보면 된다.
- LinkedList 클래스는 doubly linked list로 구현 되어 있다.

### LinkedList 사용법

- **객체 생성**

  - `LinkedList()` : LinkedList 객체 생성
  - `LinkedList(Collection c)` : 주어진 컬렉션을 포함한 객체 생성

  ```java
    // 타입설정 int 타입만 적재 가능
  LinkedList<Integer> list = new LinkedList<>();

  // 생성시 초기값 설정
  LinkedList<Integer> list2 = new LinkedList<Integer>(Arrays.asList(1,2));
  ```

  - 초기 값을 미리 지정하는 기능은 제공되지 않는다. 내부 데이터 집합 구조가 배열처럼 미리 공간을 할당하고 사용하는 방식이 아니라 데이터가 추가될 때마다 노드(객체)들이 생성되어 동적으로 추가되는 방식이기 때문이다.

- **요소 추가/삽입**

  - `void addFirst(Object obj)` : LinkedList의 맨 앞에 객체(obj)를 추가
  - `void addLast(Objec obj)` : LinkedList의 맨 뒤에 객체(obj)를 추가
  - `boolean add(Object obj)` : LinkedList의 마지막에 객체를 추가한다. (성공하면 true)
  - `void add(int index, Object element)` : 지정된 위치(index)에 객체를 저장한다.
  - `void addAll(Collection c)` : 주어진 컬렉션의 모든 객체를 저장한다. (마지막에 추가)
  - `void addAll(int index, Collection c)` : 지정한 위치부터 주어진 컬렉션의 데이터를 저장한다.

  ```java
  LinkedList<String> list = new LinkedList<>(Arrays.asList("A", "B", "C"));

  // 가장 뒤에 데이터 추가
  list.addLast("new");
  ```

  - addFirst() 와 addLast() 는 요소를 첫번째, 마지막에 추가하는 것이기 때문에 **O(1)** 의 시간이 걸린다.
  - 그러나 중간 삽입일 경우 중간에 삽입할 위치까지의 탐색을 필요하기 때문에 **O(N)** 의 시간이 걸린다.

- **요소 삭제**

  - remove 메서드 역시 add 메서드 처럼 removeFirst() 와 removeLast() 는 **O(1)**, 그외에는 탐색 시간이 필요하기에 **O(N)** 이 걸린다. 만일 값을 전부 제거하려면 clear() 메소드를 사용하면 된다
  - `Object removeFirst()` : 첫번째 노드를 제거
  - `Object removeLast()` : 마지막 노드를 제거
  - `Object remove()` : LinkedList의 첫 번째 요소를 제거
  - `Object remove(int index)` : 지정된 위치(index)에 있는 객체를 제거한다.
  - `boolean remove(Object obj)` : 지정된 객체를 제거한다. (성공하면 true)
  - `boolean removeAll(Collection c)` : 지정한 컬렉션에 저장된 것과 동일한 노드들을 LinkedList에서 제거한다.
  - `boolean retainAll(Collection c)` : LinkedList에 저장된 객체 중에서 주어진 컬렉션과 공통된 것들만 남기고 제거한다.
  - `void clear()` : LinkedList를 완전히 비운다.

  ```java
  LinkedList<String> list = new LinkedList<>(Arrays.asList("A", "B", "C"));

  // 마지막 데이터 제거
  list.removeLast();
  ```

  - 참고로 모든 값들을 제거 한다고 해서 단번에 노드들이 제거되는 것이 아닌, 참조 체인을 따라가면서 일일히 null로 설정해주기 때문에 **O(N)** 의 시간이 걸린다.

- **요소 검색**
  - `int size()` : LinkedList에 저장된 객체의 개수를 반환한다.
  - `boolean isEmpty()` : LinkedList가 비어있는지 확인한다.
  - `boolean contains(Object obj)` : 지정된 객체(obj)가 LinkedList에 포함되어 있는지 확인한다.
  - `boolean containsAll(Collection c)` : 지정된 컬렉션의 모든 요소가 포함되었는지 알려줌.
  - `int indexOf(Object obj)` : 지정된 객체(obj)가 저장된 위치를 찾아 반환한다.
  - `int lastIndexOf(Object obj)` : 지정된 객체(obj)가 저장된 위치를 뒤에서 부터 역방향으로 찾아 반환한다
- **요소 얻기**
  - 개별 단일 요소를 얻고자 하면 get 메서드로 얻어올 수 있다. 단, LinkedList는 ArrayList와 달리 참조를 따라서 일일히 이동해야 하기 때문에 탐색 성능은 좋지 않은 편이다.
  - `Object get(in index)` : 지정된 위치(index)에 저장된 객체를 반환한다.
  - `List subList(int fromIndex, int toIndex)` :fromIndex부터 toIndex사이에 저장된 객체를 List로 반환한다.
- **배열 변환**
  - Linked List는 배열은 아니지만 리스트 컬렉션이기 때문에 연속된 값으로서 배열로 변환이 가능하다.
  - `Object[] toArray()` : LinkedList에 저장된 모든 객체들을 객체배열로 반환한다.
  - `Object[] toArray(Obejct[] objArr)` : LinkedList에 저장된 모든 객체들을 객체배열 objArr에 담아 반환한다.
- **이터레이터**
  - `Collection` 인터페이스에서는 `Iterator` 인터페이스를 구현한 클래스의 인스턴스를 반환하는 `iterator()` 메소드를 정의하여 각 요소에 접근하도록 정의 하고 있다. 따라서 `Collection` 인터페이스를 상속받는 List나 Set 인터페이스에서도 `iterator()` 메소드를 사용할 수 있다. (Map은 X)
  - `LinkedList` 메서드를 보면 `Iterator` 뿐만 아니라 `ListIterator`도 지원하는걸 볼 수 있는데, `Iterator`는 `Collection` 인터페이스를 구현한 컬렉션에서 모두 사용할수 있는 반면,` ListIterator`는 오로지 `List` 컬렉션에서만 사용이 가능하다.
    - `ListIterator` 인터페이스는 `Iterator` 인터페이스를 상속받아 여러 기능을 추가한 인터페이스로서, `Iterator`는 컬렉션의 요소에 접근할 때 단 방향으로만 이동할 수 있는 반면, `ListIterator` 인터페이스는 컬렉션 요소의 대체, 추가 그리고 인덱스 검색 등을 위한 작업에서 **양방향으로 이동하는 것을 지원**하여 더욱 쓰임새가 넓다.
  - `Iterator iterator()` : LinkedList의 Iterator객체를 반환한다.
  - `ListIterator listIterator()` : LinkedList의 ListIterator를 반환한다.
  - `ListIterator listIterator(int index)` : LinkedList의 지정된 위치부터 시작하는 ListIterator를 반환한다.
- **Stack & Queue 지원**

  - `Object element()` : LinkedList에 첫 번째 노드를 반환
  - `boolean offer(Object obj)` : 지정된 객체(obj)를 LinkedList의 끝에 추가. 성공하면 true 실패하면 false
  - `Object peek()` : LinkedList의 첫 번째 요소를 반환
  - `Object poll()` : LinkedList의 첫 번째 요소를 반환. LinkedList의 요소에서는 제거된다.
  - `void push(Object obj)` : 맨 앞에 객체(obj)를 추가 (addFirst와 동일)
  - `Iterator descendingIterator()` : 역순으로 조회하기 위한 DescendingIterator를 반환
  - `Object getFirst()` : LinkedList의 첫번째 노드를 반환
  - `Object getLast()` : LinkedList의 마지막 노드를 반환
  - `boolean offerFirst(Object obj)` : 지정된 객체(obj)를 LinkedList의 맨 앞에 추가, 성공하면 true
  - `boolean offerLast(Object obj)` : 지정된 객체(obj)를 LinkedList의 맨 뒤에 추가, 성공하면 true
  - `Object peakFirst()` : 첫번째 노드를 반환
  - `Object peakLast()` : 마지막 노드를 반환
  - `Object pollFirst()` : 첫번째 노드를 반환하면서 제거
  - `Object pollLast()` : 마지막 노드를 반환하면서 제거
  - `Object pop()` : 첫번째 노드를 제거 (removeFirst와 동일)
  - `boolean removeFirstOccurrence(Object obj)` : 첫번째로 일치하는 객체를 제거
  - `boolean removeLastOccurrence(Object obj)` : 마지막으로 일치하는 객체를 제거

- 내부 함수들이 너무하게 많다...ㅎ
