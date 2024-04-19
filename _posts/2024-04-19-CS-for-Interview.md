---
title: [면접을위한 CS 지식. Chapter1-디자인 패턴과 프로그래밍 패러다임]
author: excelsiorKim
date: 2024-04-19 12:30:03 +0900
categories: [Book, CS]
tags: [back-end, CS, book]
keywords: [back-end, CS, book]
description: 면접을 위한 CS 지식
toc: true
toc_sticky: true
---

# 디자인 패턴

- 디자인 패턴이란 프로그램을 설계할 때 발생했던 문제점들을 객체 간의 상호 관계 등을 이용하여 해결할 수 있도록 하나의 규약 형태로 만들어 놓은 것을 의미한다.

### 1. 싱글톤 패턴

- 하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴이다.
- 보통 데이터베이스 연결 모듈에 많이 사용한다.
- 하나의 인스턴스를 가지고 다른 모듈들이 공유하기 때문에 생성 비용이 줄어든다는 장점이 있다.
- 하지마 의존성이 높아진다는 단점도 있다.
- 예제 코드

  ```java
  public class Singleton {
    // 싱글톤 인스턴스를 저장할 private static 변수
    private static Singleton instance;

    // 외부에서 인스턴스를 생성하지 못하도록 생성자를 private로 선언
    private Singleton() {}

    // 싱글톤 인스턴스를 반환하는 public static 메소드
    // 메소드에 synchronized 키워드를 추가하여 동시에 여러 스레드가 접근하지 못하도록 할 수 있다.
    public static synchronized Singleton getInstance() {
        // 인스턴스가 아직 생성되지 않았다면 생성
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
  }

  ```

### 의존성 주입

- 싱글톤 패턴은 사용하기 쉽고 실용적이지만 모듈간의 결합을 가하게 만들 수 있다는 단점이 있다.
- 이를 해결할 수 있는 기법들 중 하나인 의존성 주입(DI, Dependency Injection)을 통해 모듈 간의 결합을 조금 더 느슨하게 만들어 해결할 수 있다.
  - 의존성(종속성) : A가 B에 의존성이 있다는 것은 B의 변경 사항에 대해 A 또한 변해야 된다는 것을 의미한다.
- 메인 모듈이 직접 다른 하위 모둘에 대한 의존성을 주기보다 중간에 의존성 주압지(dependency injector)가 이 부분을 가로채 메인 모듈이 '간접'적으로 의존성을 주입하는 방식이다.
  - 이를 통해 메인 모듈(상위 모듈)은 하위 모듈에 대한 의존성이 떨어지게 된다. 이를 '디커플링(decoupling) 된다'고도 한다.
- 장점
  - 모듈들을 쉽게 교체할 수 있는 구조가 되어 테스팅하기 쉽고 마이그레이션하기도 수월하다.
  - 또한 구현할 때 추상화 레이어를 넣고 이를 기반으로 구현제를 넣어 주기 때문에 애플리케이션 의존성 방향이 일관되고, 애프리케이션을 쉽게 추론할 수 있으며, 모듈 간의 고나계들이 조금 더 명확해진다.
- 단점
  - 모듈들이 더욱더 분리되므로 클래스 수가 늘어나 복잡성이 증가될 수 있으며 약간의 런타임 패널티가 생기기도 한다.

### 2. 팩토리 패턴

- 객체를 사용하는 코드에서 객체 생성 부분을 떼어내 추상화한 패턴이자 상속 관계가 있는 두 클래스에서 상위 클래스가 중요한 뼈대를 결정하고, 하위 클래스에서 객체 생성에 관한 구체적인 내용을 결정하는 패턴이다.
- 상위 클래스와 하위 클래스가 분리되기 때문에 느슨한 겨합을 가지며 상위 클래스에서는 인스턴스 생성 방식에 대해 전혀 알 필요가 없기 때문에 더 많은 유연성을 갖게 된다.
- 객체 생성 로직이 따로 떼어져 있기 때문에 코드를 리팩터링하더라도 한 곳만 고칠 수 있게 되니 유지 보수성이 증가한다.

```java
interface Car {
    void drive();
}

class Sedan implements Car {
    @Override
    public void drive() {
        System.out.println("Driving a sedan.");
    }
}

class SUV implements Car {
    @Override
    public void drive() {
        System.out.println("Driving an SUV.");
    }
}

class CarFactory {
    // 팩토리 메소드
    // if문을 사용하지 않고 Enum 혹은 Map을 이용하여 매핑할 수도 있다.
    public static Car getCar(String carType) {
        if (carType == null) {
            return null;
        }
        if (carType.equalsIgnoreCase("SEDAN")) {
            return new Sedan();
        } else if (carType.equalsIgnoreCase("SUV")) {
            return new SUV();
        }
        return null;
    }
}

public class FactoryPatternDemo {
    public static void main(String[] args) {
        // 팩토리를 통해 객체를 생성
        Car car1 = CarFactory.getCar("SEDAN");
        car1.drive();

        Car car2 = CarFactory.getCar("SUV");
        car2.drive();
    }
}

```

### 전략 패턴(정책 패턴)

- 객체의 행위를 바꾸고 싶은 경우 '직접' 수정하지 않곡 전략이라고 부르는 '캡슐화한 알고리즘'을 컨텍스트 안에서 바꿔주면서 상호 교체가 가능하게 만드는 패턴이다.
  - 컨텍스트 : 상황, 맥락, 문맥을 의미하며, 개발자가 어떠한 작업을 완료하는 데 필요한 모든 관련 정보를 말한다.

```java
// 결제 전략 인터페이스
interface PaymentStrategy {
    void pay(int amount);
}

// 신용카드 결제 전략
class CreditCardStrategy implements PaymentStrategy {
    private String name;
    private String cardNumber;

    public CreditCardStrategy(String name, String cardNumber) {
        this.name = name;
        this.cardNumber = cardNumber;
    }

    @Override
    public void pay(int amount) {
        System.out.println(amount + "원이 " + cardNumber + "의 신용카드로 결제되었습니다.");
    }
}

// PayPal 결제 전략
class PayPalStrategy implements PaymentStrategy {
    private String emailId;

    public PayPalStrategy(String email) {
        this.emailId = email;
    }

    @Override
    public void pay(int amount) {
        System.out.println(amount + "원이 " + emailId + "의 PayPal 계정으로 결제되었습니다.");
    }
}

// 결제 컨텍스트
class ShoppingCart {
    private PaymentStrategy paymentStrategy;

    // 결제 전략 설정
    public void setPaymentStrategy(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    // 결제 수행
    public void checkout(int amount) {
        paymentStrategy.pay(amount);
    }
}

// 클라이언트 코드
public class StrategyPatternDemo {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();

        // 신용카드로 결제
        cart.setPaymentStrategy(new CreditCardStrategy("홍길동", "1234-5678-9012-3456"));
        cart.checkout(10000);

        // PayPal로 결제
        cart.setPaymentStrategy(new PayPalStrategy("user@example.com"));
        cart.checkout(5000);
    }
}

```

### 옵저버 패턴

- 주체가 어떤 객체(subject)의 상태 변화를 관찰하다가 상태 변화가 있을 때마다 메서드 등을 통해 옵저버 목록에 있는 옵저버들에게 변화를 알려주는 디자인 패턴이다.
  - 여기서 주체란 객체의 상태 변화를 보고 있는 관찰자이며, 옵저버들이란 이 객체의 상태 변화에 따라 전달되는 메서드 등을 기반으로 '추가 변화 사항'이 생기는 객체들을 의미한다.
  - 혹은 주체와 객체를 구분하지 않고 상태가 변경되는 객체를 기반으로 구축하기도 한다.
- 옵저버 패턴은 주로 이벤트 기반 시스템에 사용하며 MVC(Model-View-Controller)패턴에도 사용된다.
  - 예를 들어 주체라고 볼 수 있는 모델(Model)에서 변경 사항이 생겨 `update()` 메서드로 옵저버인 뷰에 알려주고 이를 기반으로 컨트롤러(Controller) 등이 작동한다.

```java
import java.util.ArrayList;
import java.util.List;

// Observer interface
interface Observer {
    void update(String message);
}

// Concrete Observer
class User implements Observer {
    private String name;

    public User(String name) {
        this.name = name;
    }

    @Override
    public void update(String message) {
        System.out.println(name + " received message: " + message);
    }
}

// Subject interface
interface Subject {
    void registerObserver(Observer o);
    void removeObserver(Observer o);
    void notifyObservers();
}

// Concrete Subject
class NewsAgency implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private String news;

    public void setNews(String news) {
        this.news = news;
        notifyObservers();
    }

    @Override
    public void registerObserver(Observer o) {
        observers.add(o);
    }

    @Override
    public void removeObserver(Observer o) {
        observers.remove(o);
    }

    @Override
    public void notifyObservers() {
        for (Observer o : observers) {
            o.update(news);
        }
    }
}

// Client code
public class ObserverPatternDemo {
    public static void main(String[] args) {
        NewsAgency newsAgency = new NewsAgency();
        User user1 = new User("John");
        User user2 = new User("Jane");

        newsAgency.registerObserver(user1);
        newsAgency.registerObserver(user2);

        newsAgency.setNews("Breaking News: Observer Pattern in Java!");

        newsAgency.removeObserver(user1);

        newsAgency.setNews("Update: Observer Pattern Example Completed.");
    }
}

```

### 프록시 패턴과 프록시 서버

- 대상 객체(subject)에 접근하기 전 그 접근에 대한 흐름을 가로채 대상 객체 앞단의 인터페이스 역할을 하는 디자인 패턴이다.
- 이를 통해 객체의 속성, 변환 등을 보완하며 보안, 데이터 검증, 캐싱, 로깅에 사용한다.

  ```java
  interface Image {
    void display();
  }

  class RealImage implements Image {
      private String fileName;

      public RealImage(String fileName) {
          this.fileName = fileName;
          loadFromDisk(fileName);
      }

      @Override
      public void display() {
          System.out.println("Displaying " + fileName);
      }

      private void loadFromDisk(String fileName) {
          System.out.println("Loading " + fileName);
      }
  }

  class ProxyImage implements Image {
      private RealImage realImage;
      private String fileName;

      public ProxyImage(String fileName) {
          this.fileName = fileName;
      }

      @Override
      public void display() {
          if (realImage == null) {
              realImage = new RealImage(fileName);
          }
          realImage.display();
      }
  }

  public class ProxyPatternDemo {
      public static void main(String[] args) {
          Image image1 = new ProxyImage("test_10mb.jpg");
          Image image2 = new ProxyImage("test_20mb.jpg");

          // 이미지를 처음에는 로드하지 않습니다.
          System.out.println("Image will be loaded on demand:");

          // 실제 이미지가 필요할 때 로딩되고 표시됩니다.
          image1.display();
          System.out.println("");

          // 이미 로딩된 이미지는 바로 표시됩니다.
          image1.display();
          System.out.println("");

          // 두 번째 이미지도 처음에는 로드되지 않습니다.
          image2.display();
      }
  }
  ```

- 이는 프록시 서버로도 활용된다.
- 프록시 서버
  - 서버와 클라이언트 사이에서 클라이언트가 자신을 통해 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해주는 컴퓨터 시스템이나 응용 프로그램을 가리킨다.
  - 프록시 서버에서의 캐싱
    - 캐시 안에 정보를 담아주고, 캐시 안에 있는 정보를 요구하는 요청에 대해 다시 멀리 있는 원격 서버에 요청하지 않고 캐시 안에 있는 데이터를 활용하는 것을 말한다. 이를 통해 불필요하게 외부와 연결하지 않기 때문에 트래픽을 줄일 수 있다는 장점이 있다.
- CORS와 프런트엔드의 프록시 서버
  - CORS(Cross-Origin Resource Sharing)는 서버가 웹 브라우저에서 리소스를 로드할 때 다른 오리진을 통해 로드하지 못하게 하는 HTTP 헤더 기반 메커니즘이다.
    - 오리진 : 프로토콜과 호스트 이름, 포트의 조합을 말함.
  - 프런트엔드 개발 시 프런트앤드 서버를 만들어서 백엔드 서버와 통신할 때 주로 CORS에러를 마주친다.
  - 이를 해결하기 위해 프런트엔드에서 프록시 서버를 만들기도 한다.

### 이터레이터 패턴

- 이터레이터 패턴(Iterator pattern)은 이터레이터를 사용하여 컬렉션의 요소들에 접근하는 디자인 패턴이다.
- 이를 통해 순회할 수 있는 여러 가지 자료형의 구조와는 상관없이 이터레이터라는 하나의 인터페이스로 순회가 가능하다.

  ```java
  import java.util.*;

  // 컬렉션에 대한 이터레이터 인터페이스 정의
  interface Iterator {
      boolean hasNext();
      Object next();
  }

  // 컬렉션에 대한 인터페이스 정의
  interface Container {
      Iterator getIterator();
  }

  // 구체적인 컬렉션 클래스
  class NameRepository implements Container {
      public String names[] = {"Robert", "John", "Julie", "Lora"};

      @Override
      public Iterator getIterator() {
          return new NameIterator();
      }

      private class NameIterator implements Iterator {
          int index;

          @Override
          public boolean hasNext() {
              if(index < names.length){
                  return true;
              }
              return false;
          }

          @Override
          public Object next() {
              if(this.hasNext()){
                  return names[index++];
              }
              return null;
          }
      }
  }

  // 이터레이터 패턴을 사용하는 클라이언트
  public class IteratorPatternDemo {

      public static void main(String[] args) {
          NameRepository namesRepository = new NameRepository();

          for(Iterator iter = namesRepository.getIterator(); iter.hasNext();){
              String name = (String)iter.next();
              System.out.println("Name : " + name);
          }
      }
  }

  ```

### 노출모듈 패턴

- 즉시 실행 함수를 통해 private, public 같은 접근 제어자를 만드는 패턴을 말한다.
  - 함수를 정의하자마자 바로 호출하는 함수. 초기화 코드, 라이브러리 내 전역 변수의 충돌 방지 등에 사용한다
- JavaScript에서 사용되는 패턴이다.
  - 자바스크립트가 프로토타입 기반 언어라는 점과 스크립트 언어의 특성상 모듈성과 캡슐화를 언어 차원에서 지원하지 않기 때문에, 이러한 패턴을 통해 모듈화와 캡슐화를 구현하기 위함이다.

### MVC 패턴

- 모델, 뷰, 컨트롤러로 이루어진 디자인 패턴이다.
- 애플리케이션의 구성 요소를 세 가지 역할로 구분하여 개발 프로세스에서 각각의 구성 요소에만 집중해서 개발할 수 있다.
- 재사용성과 확장성이 용이하다는 장점이 있고, 애플리케이션이 복잡해질수록 모델과 뷰의 관계가 복잡해지는 단점이 있다.
- 모델
  - 애플리케이션의 데이터인 데이터베이스, 상수, 변수 등을 뜻한다.
- 뷰
  - inputbox, checkbox, textarea 등 사용자 인터페이스 요소를 나타낸다.
  - 즉, 모델을 기반으로 사용자가 볼 수 있는 화면을 뜻한다.
  - 모델이 가지고 있는 정보를 따로 저장하지 않아야 한다.
  - 또한 변경이 일어나면 컨트롤러에 이를 전달해야 한다.
- 컨트롤러
  - 하나 이상의 모델과 하나 이상의 뷰를 잇는 다리 역할을 하며 이벤트 등 메인 로직을 담당한다.
  - 또한, 모델과 뷰의 생명주기도 관리하며, 모델이나 뷰의 변경 통지를 받으면 이를 해석하여 각각의 구성 요소에 해당 내용에 대해 알려준다.

### MVP 패턴

- MVC 패턴으로부터 파생되었으며 MVC에서 C에 해당하는 컨트롤러가 프레젠터(presenter)로 교체된 패턴이다.
- 뷰와 프레젠터는 일대일 관계이기 때문에 MVC패턴보다 더 강한 결합을 지닌 디자인 패턴이라고 볼 수 있다.

### MVVM 패턴

- MVC의 C에 해당하는 컨트롤러가 뷰모델(view model)로 바뀐 패턴이다.
- 여기서 뷰 모델은 뷰를 더 추상화한 계층이며, MVVM 패턴은 MVC 패턴과 다르게 커맨드와 데이터 바인딩을 가지는 것이 특징이다.
- 뷰와 뷰모델 사이의 양방향 데이터 바인딩을 지원하며 UI를 별도의 코드 수정 없이 재사용할 수 있고 단위 테스팅하기 쉽다는 장점이 있다.
  - 커멘드 : 여러가지 요소에 대한 처리를 하나의 액션으로 처리할 수 있게 하는 기법이다.
  - 데이터 바인딩 : 화면에 보이는 데이터와 웹 브라우저의 메모리 데이터를 일치시키는 기법으로, 뷰 모델을 변경하면 뷰가 변경된다.

# 프로그래밍 패러다임

- 프로그래밍 패러다임은 프로그래머에게 프로그래밍의 관점을 갖게 해주는 역할을 하는 개발 방법론이다.
- 예를 들어 객체지향 프로그래밍은 프로그램을 상호 작용하는 객체들의 잡흡으로 볼 수 있게하고, 함수형 프로그래밍은 상태 값을 지니지 않는 함수 값들의 연속으로 생각할 수 있게 한다.
- 언어 마다 특정한 패러다임을 지원하기도 하는데, jdk 1.8 이전의 자바는 객체지향 프로그래밍을 지원했다. jdk 1.8부터는 함수형 프로그래밍 패러다임을 지원하기 위해 람다식, 생성자 레퍼런스, 메서드 레퍼런스를 도입했고 선언형 프로그래밍을 위해 stream 같은 표준 API등을 추가했다.
- 프로그래밍 패러다임은 크게 선언형, 명령형을 나누며, 선연형은 함수형이라는 하위 집합을 갖는다.
- 또한 명령형은 다시 객체지향, 절차지향으로 나눈다.

### 선언형과 함수형 프로그래밍

- 선언형 프로그래밍이란 '무엇을' 풀어내는가에 집중하는 패러다임이며, "프로그램은 함수로 이루어진 것이다."라는 명제가 담겨 있는 패러다임이기도 하다.
- 함수형 프로그래밍은 선언형 패러다임의 일종이다.
  - 함수형 프로그래밍은 작은 '순수 함수'들을 블록처럼 쌓아 로직을 구현하고, '고차 함수'를 통해 재사용성을 높인 프로그래밍 패러다임이다.
    - 순수함수 : 출력이 입력에만 의존하는 것을 의미한다.
      - pure 함수는 들어오는 매개 변수에만 영향을 받는다. 만약 매개 변수 이외에 전역 변수 등에 의해 출력에 영향을 받으면 순수함수가 아니다.
    - 고차함수 : 함수가 함수를 값처럼 매개변수로 받아 로직을 생성할 수 있는 것을 말한다.
      - 이때 고차 함수를 쓰기 위해서는 해당 언어가 일급 객체라는 특징을 가져야 하며 그 특징은 아래와 같다.
        - 변수나 메서드에 함수를 할당할 수 있다.
        - 함수 안에 함수를 매개변수로 담을 수 있다.
        - 함수가 함수를 반환할 수 있다.
  - 자바스크립트는 단순하고 유연한 언어이며, 함수가 일급 객체이기 때문에 객체지향 프로그래밍보다는 함수형 프로그래밍 방식이 선호된다.
  - 참고로 함수형 프로그래밍은 이외에도 커링, 불변성 등 많은 특징이 있다.

### 객체지향 프로그래밍(OOP, Object-Oriented Programming)

- 객체들의 집합으로 프로그램의 상호 작용을 표현하며 데이터를 객체로 취급하여 객체 내부에 선언된 메서드를 활용하는 방식을 말한다.
- 설계에 많은 시간이 소요되며 처리 속도가 다른 프로그래밍 패러다임에 비해 상대적으로 느리다.
- 특징
  - 추상화 : 복잡한 시스템으로부터 핵심적인 개념 또는 기능을 간추려내는 것을 의미한다.
  - 캡슐화 : 객체의 속성과 메서드를 하나로 묶고 일부를 외부에 감추어 은닉하는 것을 말한다.
  - 상속성 : 상위 클래스의 특성을 하위 클래스가 이어받아서 재사용하거나 추가, 확장하는 것을 말한다.
    - 코드의 재사용 측면, 계층적인 관계 생성, 유지 보수성 측면에서 중요하다.
  - 다형성 : 하나의 메서드나 클래스가 다양한 방법으로 동작하는 것을 말함.
    - 대표적으로 오버로딩, 오버라이딩이 있음
      - 오버로딩 : 같은 이름을 가진 메서드를 여러 개 두는 것을 말함. 메서드의 타입, 매개 변수의 유형, 개수 등으로 컴파일 중에 발생하는 '정적' 다형성이다.
      - 오버 라이딩 : 메서드 오버라이딩을 말하며 상위 클래스로부터 상송받은 메서드를 하위 클래스가 재정의하는 것을 말함. 이는 런타임 중에 발생하는 '동적' 다형성이다.
- SOLID 설계 원칙
  - 단일 책임 원칙(SRP, Single Responsibility Principle) : 모든 클래스는 각각 하나의 책임만을 가져야 하는 원칙
  - 개방 폐쇄 원칙(OCP, Open Closed Principle) : 유지 보수 사항이 생긴다면 코드를 쉽게 확장할 수 있도록 하고 수정할 때는 닫혀있어야 한다.
  - 리스코프 치환 원칙(LSP, Liskov Substitution Principle) : 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다. 즉, 부모 객체에 자식 객체를 넣어도 시스템이 문제 없이 돌아가게 만드는 것
  - 인터페이스 분리 언칙(ISP, Interface Segregation Principle) : 하나의 일반적인 인터페이스보다 구체적인 여러 개의 인터페이스를 만들어야 하는 원칙.
  - 의존 역전 원칙(DIP, Dependency Inversion Principle) : 자신보다 변하기 쉬운 것에 의존하던 것을 추상화된 인터페이스나 상위 클래스를 두어 변하기 쉬운 것의 변화에 영향받지 않게 하는 원칙

### 절차지향 프로그래밍

- 로직이 수행되어야 할 연속적인 계산 과정으로 이루어져 있다.
- 일이 진행되는 방식으로 그저 코드를 구현하기만 하면 되기 때문에 코드의 가독성이 좋으며 실행 속도가 빠르다.
- 그렇기 때문에 계산이 많은 작업 등에 쓰인다.
  - 대표적으로 fortran을 이용한 대기 과학 관련 연산 작업, 머신 러닝의 배치 작업 등이 있다.
- 단저으로는 모듈화하기가 어렵고 유지 보수성이 떨어진다는 점이 있다.

### 패러다임의 혼합

- 어떠한 패러다임이 가장 좋을까? -> 그런 것은 없다.
- 비즈니스 로직이나 서비스의 특징을 고려해서 패러다임을 정하는 것이 좋다.
- **하나의 패러다임을 기반으로 통일하여 서비스를 구축하는 것도 좋은 생각이지만 여러 패러다임을 조합하여 상황과 맥락에 따라 패러다임 간의 장점만을 취해 개발하는 것이 좋다.**
- 예룰 들어 백엔드에 머신 러닝 파이프라인과 거래 관련 로직이 있다면 머신 러닝 파이프라인은 절차지향형 패러다임, 거래 관련 로직은 함수형 프로그래밍을 적용하는 것이 좋다.

## 면접 예상 질문

- Q. 옵저버 패턴을 어떻게 구현하는가?
  - A. 여러가지 방법이 있지만 프록시 객체를 써서 하곤 한다. 프록시 객체를 통해 객체의 속성이나 메서드 변화 등을 감지하고 이를 미리 설정해놓은 옵저버들에게 전달하는 방법으로 구현한다.
- Q. 프록시 서버를 설명하고 사용 사례에 대해 말하라.
  - A. 프록시 서버란 서버 앞단에 둬서 캐싱, 로깅, 데이터 분석을 서버보다 먼저하는 서버를 말한다. 이를 통해 포트 번호를 바꿔서 사용자가 실제 서버의 포트에 접근하지 못하게 할 수 있으며, 공격자의 DDOS 공격을 차단하거나 CDN을 프록시 서버로 달아서 캐싱 처리를 용이하게 할 수 있다. CloudFlare를 사용해 캐싱, 로그 분석 등을 하는 사용 사례가 있다.
