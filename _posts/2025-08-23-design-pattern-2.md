---
title: "디자인패턴: Builder 패턴"
author: Dante
date: 2025-08-23 11:50:03 +0900
categories: [design-pattern, software-engineering]
tags: [design-pattern, builder, clean-code, fluent-builder, functional-builder]
keywords: [design-pattern, builder, clean-code, fluent-builder, functional-builder]
description: "Builder 패턴의 특징과 각종 구현 방식에 대해 알아보자."
toc: true
toc_sticky: true
---

# Builder 패턴이란?

- Builder 패턴은 복잡한 객체를 단계별로 생성하기 위한 창조형 디자인 패턴이다.

**핵심 동기:**
- 간단한 객체(예: X, Y 좌표를 가진 Point)는 단일 생성자 호출로 쉽게 만들 수 있음.
- 하지만 복잡한 객체는 생성 과정에서 많은 준비 작업이 필요.

**기존 방식의 문제점:**
- 10개 이상의 매개변수를 가진 생성자는 비생산적
- 매개변수가 많으면 실수하기 쉬움 (순서 착각 등)
- 여러 개의 + 기호로 문자열을 연결하는 것은 비효율적
- IDE의 자동완성 기능도 완벽하지 않음

**Builder 패턴의 해결책:**
- 객체를 조각조각 단계적으로 구성 (piece-wise construction)
- StringBuilder처럼 각 부분을 하나씩 추가하는 방식
- 복잡한 객체 생성을 위한 별도의 컴포넌트 제공
- 명확하고 사용하기 쉬운 API 제공

**결론:** Builder 패턴은 복잡한 객체 생성 과정을 간결하고 직관적으로 만들어주는 특별한 API를 제공하는 패턴이다.


# Builder 패턴: 복잡한 객체를 단계별로 구성하기

Builder 패턴은 복잡한 객체의 생성 과정을 단계별로 나누어 진행할 수 있게 해주는 생성 패턴이다. 특히 많은 매개변수를 가진 객체나 선택적 매개변수가 많은 객체를 생성할 때 유용하다.

## Builder 패턴이 필요한 이유

복잡한 객체를 생성할 때 다음과 같은 문제들이 발생할 수 있다:

1. **텔레스코핑 생성자 문제(Telescoping Constructor Problem)**: 매개변수가 많아질수록 생성자가 복잡해진다
2. **가독성 저하**: 어떤 매개변수가 무엇을 의미하는지 파악하기 어렵다
3. **불변성 보장의 어려움**: 객체 생성 후 상태 변경 가능성이 존재한다
4. **유효성 검증의 분산**: 객체 생성 과정에서 유효성 검증이 어렵다

Builder 패턴은 이러한 문제들을 해결하고, 복잡한 객체를 직관적이고 읽기 쉬운 방식으로 생성할 수 있게 해준다.

## HTML 요소 생성 예제

다음은 HTML 요소를 생성하는 Builder 패턴의 구현 예제이다:

### 1. HTML 요소 클래스

```csharp
public class HtmlElement
{
    public string Name, Text;
    public List<HtmlElement> Elements = new List<HtmlElement>();
    private const int indentSize = 2;

    public HtmlElement(string name, string text)
    {
        Name = name ?? throw new ArgumentNullException(nameof(name));
        Text = text ?? string.Empty;  // text는 빈 문자열 허용
    }

    public HtmlElement() { }

    private string ToStringImpl(int indent)
    {
        var sb = new StringBuilder();
        var i = new string(' ', indentSize * indent);
        sb.AppendLine($"{i}<{Name}>");

        if (!string.IsNullOrWhiteSpace(Text))
        {
            sb.Append(new string(' ', indentSize * (indent + 1)));
            sb.AppendLine(Text);
        }

        foreach (var e in Elements)
        {
            sb.Append(e.ToStringImpl(indent + 1));
        }

        sb.AppendLine($"{i}</{Name}>");
        return sb.ToString();
    }

    public override string ToString()
    {
        return ToStringImpl(0);
    }
}
```

`HtmlElement` 클래스는 HTML 요소를 나타내며, 계층 구조를 가질 수 있도록 설계되었다. `ToStringImpl` 메서드는 적절한 들여쓰기와 함께 HTML 문자열을 생성한다.

### 2. HTML Builder 클래스

```csharp
public class HtmlBuilder
{
    private readonly string rootName;
    HtmlElement root = new HtmlElement();

    public HtmlBuilder(string rootName)
    {
        this.rootName = rootName;
        root.Name = rootName;
    }

    public HtmlBuilder AddChild(string childName, string childText)
    {
        var e = new HtmlElement(childName, childText);
        root.Elements.Add(e);

        return this; // 메서드 체이닝을 위한 자기 자신 반환
    }

    public override string ToString()
    {
        return root.ToString();
    }

    public void Clear()
    {
        root = new HtmlElement { Name = rootName };
    }
}
```

`HtmlBuilder` 클래스는 Builder 패턴의 핵심이다:
- **Fluent Interface**: `AddChild` 메서드가 자기 자신을 반환하여 메서드 체이닝이 가능하다
- **단계별 구성**: HTML 요소를 하나씩 추가하며 구조를 생성한다
- **캡슐화**: 복잡한 HTML 구조 생성 로직을 내부에 숨긴다

### 3. 사용 예제

```csharp
public class Demo
{
    static void Main(string[] args)
    {
        HtmlBuilder htmlBuilder = new HtmlBuilder("ul");

        htmlBuilder.AddChild("li", "hello")
                   .AddChild("li", "world");

        Console.WriteLine(htmlBuilder.ToString());
    }
}
```

위 코드는 다음과 같은 HTML을 생성한다:

```html
<ul>
  <li>
    hello
  </li>
  <li>
    world
  </li>
</ul>
```

## Builder 패턴의 장점

### 1. 가독성 향상
```csharp
// Builder 패턴 사용
var html = new HtmlBuilder("ul")
    .AddChild("li", "첫 번째 항목")
    .AddChild("li", "두 번째 항목")
    .AddChild("li", "세 번째 항목");

// 전통적인 방식 (복잡함)
var element = new HtmlElement("ul", "");
element.Elements.Add(new HtmlElement("li", "첫 번째 항목"));
element.Elements.Add(new HtmlElement("li", "두 번째 항목"));
element.Elements.Add(new HtmlElement("li", "세 번째 항목"));
```

### 2. 유연성
- 필요한 구성 요소만 선택적으로 추가할 수 있다
- 런타임에 동적으로 객체 구조를 결정할 수 있다

### 3. 재사용성
- Builder 인스턴스를 재사용하여 유사한 객체들을 생성할 수 있다
- `Clear()` 메서드로 Builder 상태를 초기화한 후 재사용할 수 있다

### 4. 검증과 일관성
- 객체 생성 과정에서 일관된 검증 로직을 적용할 수 있다
- 불완전한 객체 생성을 방지할 수 있다

## Builder 패턴 변형

### 1. Fluent Builder
현재 예제에서 사용된 방식으로, 메서드 체이닝을 통해 직관적인 API를 제공한다. 가장 일반적으로 사용되는 Builder 패턴의 형태이다.

### 2. Step Builder (단계별 Builder)

Step Builder는 객체 생성 과정을 명확한 단계로 나누고, 각 단계를 인터페이스로 정의하여 올바른 순서로 객체가 생성되도록 강제하는 패턴이다. 이는 컴파일 타임에 필수 단계를 보장하며, 잘못된 순서로 메서드를 호출하는 것을 방지한다.

#### Step Builder의 핵심 개념

**1. 단계별 인터페이스 분리**
각 단계마다 별도의 인터페이스를 정의하여 다음 단계로만 진행할 수 있도록 제한한다.

**2. 컴파일 타임 검증**
타입 시스템을 활용하여 빌드 과정에서 필수 단계를 누락하는 것을 컴파일 타임에 방지한다.

**3. 명확한 API**
각 단계에서 사용할 수 있는 메서드가 명확하게 제한되어 API 사용이 직관적이다.

#### 자동차 생성 Step Builder 예제

다음은 자동차 객체를 생성하는 Step Builder 패턴의 구현이다:

```csharp
public enum CarType
{
    Sedan,
    Crossover
}

public class Car
{
    public CarType Type;
    public int WheelSize;
    
    public override string ToString()
    {
        return $"Car Type: {Type}, Wheel Size: {WheelSize}";
    }
}
```

**단계별 인터페이스 정의:**

```csharp
// 1단계: 자동차 타입 지정 인터페이스
public interface ISpecifyCarType
{
    ISpecifyWheelSize OfType(CarType type);
}

// 2단계: 휠 크기 지정 인터페이스
public interface ISpecifyWheelSize
{
    IBuildCar WithWheels(int size);
}

// 3단계: 빌드 실행 인터페이스
public interface IBuildCar
{
    Car Build();
}
```

**Step Builder 구현:**

```csharp
public class CarBuilder
{
    // 내부 구현 클래스 - 모든 인터페이스를 구현한다
    private class Impl : ISpecifyCarType, ISpecifyWheelSize, IBuildCar
    {
        private Car car = new Car();

        public ISpecifyWheelSize OfType(CarType type)
        {
            car.Type = type;
            return this; // 다음 단계 인터페이스로 반환
        }

        public IBuildCar WithWheels(int size)
        {
            // 비즈니스 로직: 자동차 타입에 따른 휠 크기 검증
            switch (car.Type)
            {
                case CarType.Crossover when size < 17 || size > 20:
                case CarType.Sedan when size < 15 || size > 17:
                    throw new ArgumentException($"Wrong size of wheel for {car.Type}.");
            }
            car.WheelSize = size;
            return this; // 마지막 단계 인터페이스로 반환
        }

        public Car Build()
        {
            return car;
        }
    }

    // 팩토리 메서드 - 첫 번째 단계 인터페이스로 시작한다
    public static ISpecifyCarType Create()
    {
        return new Impl();
    }
}
```

#### 사용 예제

```csharp
public class Demo
{
    static void Main(string[] args)
    {
        // 정확한 순서로만 메서드 호출이 가능하다
        var sedan = CarBuilder.Create()
            .OfType(CarType.Sedan)      // 1단계: 타입 지정
            .WithWheels(16)             // 2단계: 휠 크기 지정
            .Build();                   // 3단계: 빌드 실행

        var crossover = CarBuilder.Create()
            .OfType(CarType.Crossover)
            .WithWheels(18)
            .Build();

        Console.WriteLine(sedan);    // Car Type: Sedan, Wheel Size: 16
        Console.WriteLine(crossover); // Car Type: Crossover, Wheel Size: 18

        // 다음과 같은 호출은 컴파일 에러가 발생한다:
        // CarBuilder.Create().WithWheels(16); // OfType()을 먼저 호출해야 함
        // CarBuilder.Create().Build();        // 필수 단계들을 거쳐야 함
    }
}
```

#### Step Builder의 장점

**1. 강제된 순서**
```csharp
// 올바른 순서 - 컴파일 성공
var car = CarBuilder.Create()
    .OfType(CarType.Sedan)    // 반드시 첫 번째
    .WithWheels(16)           // 반드시 두 번째
    .Build();                 // 반드시 마지막

// 잘못된 순서 - 컴파일 에러
// var car = CarBuilder.Create().WithWheels(16); // 에러!
```

**2. 비즈니스 로직 검증**
각 단계에서 입력값의 유효성을 검증할 수 있다:
- Sedan: 15-17인치 휠만 허용
- Crossover: 17-20인치 휠만 허용

**3. IDE 지원**
각 단계에서 호출 가능한 메서드만 IntelliSense에 표시되어 개발자 경험이 향상된다.

**4. 불완전한 객체 생성 방지**
`Build()` 메서드가 마지막 인터페이스에만 있어 모든 필수 속성이 설정된 후에만 객체 생성이 가능하다.

#### 고급 Step Builder 패턴

더 복잡한 객체의 경우 선택적 단계와 필수 단계를 조합할 수 있다:

```csharp
public interface ISpecifyCarType
{
    ISpecifyWheelSize OfType(CarType type);
}

public interface ISpecifyWheelSize
{
    IOptionalFeatures WithWheels(int size);
}

public interface IOptionalFeatures : IBuildCar
{
    IOptionalFeatures WithAirConditioner();
    IOptionalFeatures WithSunroof();
    IOptionalFeatures WithGPS();
    // Build() 메서드는 IBuildCar에서 상속받아 사용 가능
}

public interface IBuildCar
{
    Car Build();
}
```

이러한 구조를 통해 필수 단계(타입, 휠 크기) 이후에 선택적 기능들을 자유롭게 추가할 수 있다.

#### Step Builder vs Fluent Builder

| 특징               | Step Builder                | Fluent Builder            |
| ------------------ | --------------------------- | ------------------------- |
| **순서 강제**      | 컴파일 타임에 강제됨        | 런타임에 검증 또는 미검증 |
| **타입 안전성**    | 높음                        | 중간                      |
| **복잡성**         | 높음 (인터페이스 많음)      | 낮음                      |
| **유연성**         | 낮음 (정해진 순서)          | 높음 (자유로운 순서)      |
| **IDE 지원**       | 우수 (단계별 메서드만 표시) | 좋음                      |
| **오류 발견 시점** | 컴파일 타임                 | 런타임                    |

#### 언제 Step Builder를 사용할까?

- **필수 단계가 명확한 경우**: 반드시 거쳐야 하는 설정 단계가 있을 때
- **순서가 중요한 경우**: 설정 순서가 객체의 유효성에 영향을 미칠 때
- **복잡한 검증이 필요한 경우**: 각 단계에서 이전 단계의 결과에 따라 검증이 달라질 때
- **팀 개발 환경**: 다른 개발자들이 API를 잘못 사용하는 것을 방지하고자 할 때
- **도메인 특화 언어(DSL) 구축**: 명확한 문법 구조가 필요한 경우

Step Builder는 타입 안전성과 명확한 API를 제공하는 강력한 패턴이지만, 구현 복잡도가 높으므로 정말 필요한 경우에만 사용하는 것이 좋다.

### 3. Faceted Builder

Faceted Builder는 복잡한 객체의 서로 다른 측면(Facet)을 별도의 전문 Builder로 분리하여 구성하는 고급 패턴이다. 하나의 객체가 여러 영역의 정보를 가질 때, 각 영역을 담당하는 독립적인 Builder를 제공하여 관심사를 분리하고 코드의 가독성을 높인다.

#### 핵심 개념

**1. 관심사 분리**
- 복잡한 객체를 논리적 영역(Facet)으로 분할한다
- 각 영역마다 전용 Builder 클래스를 제공한다
- 주 Builder가 Facade 역할을 하여 하위 Builder들을 조율한다

**2. 공유 객체 참조**
- 모든 Faceted Builder가 동일한 객체 인스턴스를 공유한다
- 각 Builder의 작업이 같은 객체에 반영된다
- 최종적으로 하나의 완성된 객체를 생성한다

#### Person 객체 예제

복잡한 Person 객체를 주소 정보와 직업 정보로 분리하여 구성하는 예제이다:

```csharp
public class Person
{
    // 주소 정보
    public string StreetAddress, Postcode, City;

    // 직업 정보  
    public string CompanyName, Position;
    public int AnnualIncome;

    public override string ToString()
    {
        return $"주소: {StreetAddress}, {City} {Postcode}\n" +
               $"직업: {CompanyName}의 {Position}, 연봉: {AnnualIncome:C}";
    }
}
```

#### 메인 Facade Builder

```csharp
public class PersonBuilder // Facade 역할
{
    // 모든 하위 Builder가 공유할 객체 참조
    protected Person person = new Person();

    // 각 측면을 담당하는 Builder에 대한 접근점
    public PersonJobBuilder Works => new PersonJobBuilder(person);
    public PersonAddressBuilder Lives => new PersonAddressBuilder(person);

    // 암시적 변환 연산자로 편의성을 제공한다
    public static implicit operator Person(PersonBuilder pb)
    {
        return pb.person;
    }
}
```

**핵심 포인트:**
- `protected Person person`: 모든 하위 Builder가 접근할 공유 객체
- `Works`, `Lives` 프로퍼티: 각 영역별 Builder에 대한 진입점
- 암시적 변환 연산자: `PersonBuilder`에서 `Person`으로 자동 변환

#### 주소 정보 Builder

```csharp
public class PersonAddressBuilder : PersonBuilder
{
    public PersonAddressBuilder(Person person)
    {
        this.person = person; // 공유 객체 참조 설정
    }

    public PersonAddressBuilder At(string streetAddress)
    {
        person.StreetAddress = streetAddress;
        return this;
    }

    public PersonAddressBuilder WithPostcode(string postcode)
    {
        person.Postcode = postcode;
        return this;
    }

    public PersonAddressBuilder In(string city)
    {
        person.City = city;
        return this;
    }
}
```

#### 직업 정보 Builder

```csharp
public class PersonJobBuilder : PersonBuilder
{
    public PersonJobBuilder(Person person)
    {
        this.person = person; // 공유 객체 참조 설정
    }

    public PersonJobBuilder At(string companyName)
    {
        person.CompanyName = companyName;
        return this;
    }

    public PersonJobBuilder AsA(string position)
    {
        person.Position = position;
        return this;
    }

    public PersonJobBuilder Earning(int amount)
    {
        person.AnnualIncome = amount;
        return this;
    }
}
```

#### 사용 예제

```csharp
public static void Main(string[] args)
{
    var pb = new PersonBuilder();
    
    // Faceted Builder를 사용한 객체 생성
    Person person = pb
        .Lives                           // 주소 Builder로 전환
            .At("123 London Road")       // 도로명 설정
            .In("London")                // 도시 설정  
            .WithPostcode("SW123C")      // 우편번호 설정
        .Works                           // 직업 Builder로 전환
            .At("Fabrikam")              // 회사명 설정
            .AsA("Engineer")             // 직책 설정
            .Earning(123000);            // 연봉 설정

    Console.WriteLine(person);
}
```

**출력 결과:**
```
주소: 123 London Road, London SW123C
직업: Fabrikam의 Engineer, 연봉: ₩123,000
```

#### Faceted Builder의 동작 원리

**1. 객체 공유 메커니즘**
```csharp
// 1. 메인 Builder에서 Person 객체를 생성한다
PersonBuilder pb = new PersonBuilder(); // person = new Person() 실행

// 2. 주소 Builder 생성 시 동일한 객체 참조를 전달한다
PersonAddressBuilder addressBuilder = pb.Lives; // new PersonAddressBuilder(person)

// 3. 직업 Builder 생성 시에도 동일한 객체 참조를 전달한다  
PersonJobBuilder jobBuilder = pb.Works; // new PersonJobBuilder(person)

// 4. 모든 Builder가 같은 Person 인스턴스를 수정한다
```

**2. Builder 간 전환**
- `Lives` 프로퍼티 호출 시 주소 설정 모드로 전환된다
- `Works` 프로퍼티 호출 시 직업 설정 모드로 전환된다
- 각 Builder는 자신의 영역에 특화된 메서드만 제공한다

**3. 상속 활용**
- 모든 Faceted Builder가 `PersonBuilder`를 상속한다
- 공통 기능(공유 객체 참조, 암시적 변환)을 기본 클래스에서 제공한다
- 각 하위 Builder는 자신만의 전문 메서드를 추가한다

#### 고급 활용 패턴

**1. 크로스 참조 (Cross-Reference)**
```csharp
var person = new PersonBuilder()
    .Lives
        .At("서울시 강남구")
        .In("서울")
    .Works                          // 다시 직업 설정으로
        .At("네이버")
        .AsA("시니어 개발자")
    .Lives                          // 다시 주소 설정으로  
        .WithPostcode("12345")      // 추가 주소 정보
    .Works                          // 최종 직업 정보
        .Earning(80000000);
```

**2. 조건부 설정**
```csharp
var builder = new PersonBuilder()
    .Lives.At("기본 주소");

if (hasDetailedAddress)
{
    builder = builder.Lives
        .WithPostcode("12345")
        .In("서울");
}

if (hasJobInfo)
{
    builder = builder.Works
        .At("삼성전자")
        .AsA("소프트웨어 엔지니어");
}

Person person = builder;
```

#### Faceted Builder의 장점

**1. 명확한 책임 분리**
```csharp
// 주소 관련 로직만 AddressBuilder에 집중된다
public class PersonAddressBuilder : PersonBuilder
{
    // 주소 검증, 형식화 등의 로직을 여기에 집중시킨다
    public PersonAddressBuilder WithValidatedPostcode(string postcode)
    {
        if (!IsValidPostcode(postcode))
            throw new ArgumentException("Invalid postcode format");
        person.Postcode = postcode;
        return this;
    }
}
```

**2. 가독성과 직관성**
- 각 영역별로 명확한 API를 제공한다
- 자연어에 가까운 fluent interface를 구현한다
- 코드를 읽기만 해도 의도를 파악할 수 있다

**3. 확장성**
- 새로운 영역 추가 시 새로운 Faceted Builder만 생성하면 된다
- 기존 코드 수정 없이 기능을 확장할 수 있다

```csharp
// 새로운 측면 추가 예제
public class PersonHealthBuilder : PersonBuilder
{
    public PersonHealthBuilder(Person person) 
    { 
        this.person = person; 
    }

    public PersonHealthBuilder Height(int cm) { /* ... */ }
    public PersonHealthBuilder Weight(int kg) { /* ... */ }
}

// PersonBuilder에 새 프로퍼티를 추가한다
public PersonHealthBuilder Health => new PersonHealthBuilder(person);
```

**4. 타입 안전성**
- 각 Builder가 자신의 영역에만 접근한다
- 컴파일 타임에 잘못된 메서드 호출을 방지한다
- IntelliSense에서 관련 메서드만 표시된다

#### Faceted Builder vs 다른 패턴

| 특징          | Faceted Builder      | Monolithic Builder | Step Builder     |
| ------------- | -------------------- | ------------------ | ---------------- |
| **책임 분리** | 최고 (영역별 분리)   | 낮음               | 중간             |
| **가독성**    | 높음 (의미적 그룹핑) | 중간               | 높음             |
| **확장성**    | 높음 (새 Facet 추가) | 중간               | 낮음             |
| **복잡성**    | 중간                 | 낮음               | 높음             |
| **유연성**    | 높음 (자유로운 전환) | 높음               | 낮음 (순서 고정) |

#### 언제 Faceted Builder를 사용할까?

- **복합 도메인 객체**: 여러 영역의 정보를 가진 복잡한 객체를 생성할 때
- **설정 객체**: 다양한 카테고리의 설정값을 가진 구성 객체를 만들 때  
- **대용량 DTO**: 여러 서비스의 데이터를 합친 전송 객체를 구성할 때
- **테스트 픽스처**: 복잡한 테스트 데이터를 생성할 때
- **엔터프라이즈 애플리케이션**: 복잡한 비즈니스 도메인을 다룰 때

**실제 활용 사례:**
```csharp
// 주문서 생성 예제
var order = new OrderBuilder()
    .Customer
        .WithName("김고객")
        .WithEmail("customer@example.com")
    .Shipping  
        .ToAddress("서울시 강남구")
        .WithMethod("택배")
    .Payment
        .WithCard("1234-5678-9012-3456")
        .Amount(50000)
    .Items
        .Add("상품A", 2, 15000)
        .Add("상품B", 1, 20000);
```

Faceted Builder는 복잡한 객체를 논리적으로 분할하여 각 영역에 집중할 수 있게 해주는 강력한 패턴이다. 특히 도메인이 복잡하고 여러 관심사가 얽혀있는 엔터프라이즈 애플리케이션에서 매우 유용하다.

### 4. Functional Builder

Functional Builder는 함수형 프로그래밍 개념을 Builder 패턴에 적용한 고급 패턴이다. 객체 생성 과정을 함수들의 조합으로 표현하여, 더욱 유연하고 확장 가능한 Builder를 만들 수 있다.

#### 핵심 개념

**1. Action 기반 접근법**
- 객체 설정을 `Action<T>` 함수들의 리스트로 관리한다
- 각 설정 작업을 독립적인 함수로 분리한다
- `Aggregate`를 사용하여 모든 액션을 순차적으로 적용한다

**2. 제네릭과 추상화**
- `FunctionalBuilder<TSubject, TSelf>` 추상 클래스로 재사용 가능한 구조를 제공한다
- 다양한 타입의 객체에 동일한 패턴을 적용할 수 있다

#### Functional Builder 구현

```csharp
public abstract class FunctionalBuilder<TSubject, TSelf>
    where TSelf : FunctionalBuilder<TSubject, TSelf>
    where TSubject : new()
{
    private readonly List<Func<TSubject, TSubject>> actions
        = new List<Func<TSubject, TSubject>>();

    public TSelf Do(Action<TSubject> action) => AddAction(action);

    public TSubject Build() => actions.Aggregate(new TSubject(), (p, f) => f(p));

    private TSelf AddAction(Action<TSubject> action)
    {
        actions.Add(p => { action(p); return p; });
        return (TSelf)this;
    }
}
```

**핵심 메서드 분석:**

1. **Do 메서드**: 임의의 `Action<TSubject>`를 추가할 수 있는 범용 메서드이다
2. **AddAction**: `Action`을 `Func<TSubject, TSubject>`로 변환하여 리스트에 추가한다
3. **Build**: `Aggregate`를 사용하여 모든 액션을 순차적으로 실행한다

#### Person Builder 구현

```csharp
public class Person
{
    public string Name, Position;
}

public sealed class PersonBuilder : FunctionalBuilder<Person, PersonBuilder>
{
    public PersonBuilder Called(string name) => Do(p => p.Name = name);
}

// 확장 메서드를 통한 기능 확장
public static class PersonBuilderExtensions
{
    public static PersonBuilder WorksAs(this PersonBuilder builder, string position) 
        => builder.Do(p => p.Position = position);
}
```

#### 사용 예제

```csharp
public static void Main(string[] args)
{
    var person = new PersonBuilder()
        .Called("Sarah")          // 기본 메서드
        .WorksAs("Developer")     // 확장 메서드
        .Do(p => p.Name = p.Name.ToUpper()) // 직접 Action 사용
        .Build();

    Console.WriteLine($"Name: {person.Name}, Position: {person.Position}");
    // 출력: Name: SARAH, Position: Developer
}
```

#### Functional Builder의 장점

**1. 극도의 유연성**
```csharp
var person = new PersonBuilder()
    .Called("John")
    .Do(p => p.Position = "Senior " + "Developer") // 복잡한 로직이 가능하다
    .Do(p => Console.WriteLine($"Creating: {p.Name}")) // 부수 효과도 가능하다
    .WorksAs("Team Lead")
    .Build();
```

**2. 확장성**
- 확장 메서드를 통해 기존 클래스 수정 없이 기능을 추가할 수 있다
- 라이브러리 사용자가 직접 기능을 확장할 수 있다

**3. 조건부 실행**
```csharp
var builder = new PersonBuilder().Called("Alice");

if (isManager)
{
    builder = builder.WorksAs("Manager");
}
else
{
    builder = builder.WorksAs("Developer");
}

var person = builder.Build();
```

**4. 함수 조합**
```csharp
// 공통 설정을 함수로 분리한다
Action<Person> makeManager = p => p.Position = "Manager";
Action<Person> makeNameUpperCase = p => p.Name = p.Name.ToUpper();

var person = new PersonBuilder()
    .Called("bob")
    .Do(makeNameUpperCase)
    .Do(makeManager)
    .Build();
```

#### 고급 활용 예제

**1. 복잡한 검증 로직**
```csharp
public static class PersonBuilderExtensions
{
    public static PersonBuilder WithValidatedName(this PersonBuilder builder, string name)
    {
        return builder.Do(p => 
        {
            if (string.IsNullOrWhiteSpace(name))
                throw new ArgumentException("Name cannot be empty");
            if (name.Length < 2)
                throw new ArgumentException("Name must be at least 2 characters");
            p.Name = name.Trim();
        });
    }

    public static PersonBuilder WithSeniorPosition(this PersonBuilder builder, string basePosition)
    {
        return builder.Do(p => p.Position = $"Senior {basePosition}");
    }
}
```

**2. 조건부 빌더 메서드**
```csharp
public static class PersonBuilderExtensions
{
    public static PersonBuilder AddTitleIf(this PersonBuilder builder, bool condition, string title)
    {
        if (condition)
        {
            return builder.Do(p => p.Name = $"{title} {p.Name}");
        }
        return builder;
    }
}

// 사용 예
var person = new PersonBuilder()
    .Called("Smith")
    .AddTitleIf(isDoctor, "Dr.")
    .WorksAs("Surgeon")
    .Build();
```

**3. 파이프라인 패턴과의 결합**
```csharp
public static class PersonBuilderExtensions
{
    public static PersonBuilder ApplyTransformation(
        this PersonBuilder builder, 
        params Action<Person>[] transformations)
    {
        foreach (var transformation in transformations)
        {
            builder = builder.Do(transformation);
        }
        return builder;
    }
}

// 사용 예
var transformations = new Action<Person>[]
{
    p => p.Name = p.Name.Trim(),
    p => p.Name = CultureInfo.CurrentCulture.TextInfo.ToTitleCase(p.Name),
    p => p.Position = p.Position?.ToLower()
};

var person = new PersonBuilder()
    .Called("  john doe  ")
    .WorksAs("DEVELOPER")
    .ApplyTransformation(transformations)
    .Build();
```

#### Functional Builder vs 다른 Builder 패턴

| 특징                         | Functional Builder    | Fluent Builder | Step Builder | Faceted Builder      |
| ---------------------------- | --------------------- | -------------- | ------------ | -------------------- |
| **유연성**                   | 최고 (임의 함수 실행) | 높음           | 낮음         | 높음 (영역별 전환)   |
| **확장성**                   | 최고 (확장 메서드)    | 중간           | 낮음         | 높음 (새 Facet 추가) |
| **타입 안전성**              | 중간                  | 중간           | 최고         | 높음                 |
| **복잡성**                   | 중간                  | 낮음           | 높음         | 중간                 |
| **성능**                     | 중간 (함수 리스트)    | 높음           | 높음         | 높음                 |
| **책임 분리**                | 낮음                  | 낮음           | 중간         | 최고 (영역별 분리)   |
| **함수형 프로그래밍 친화성** | 최고                  | 낮음           | 낮음         | 낮음                 |

#### 언제 Functional Builder를 사용할까?

- **동적 구성이 필요한 경우**: 런타임에 빌드 로직이 결정되는 상황에서 유용하다
- **확장성이 중요한 경우**: 라이브러리나 프레임워크에서 사용자 확장을 허용할 때 적합하다
- **복잡한 변환 로직**: 객체 생성 과정에서 복잡한 데이터 변환이 필요할 때 효과적이다
- **조건부 설정**: 다양한 조건에 따라 다른 설정을 적용해야 할 때 유연하게 대응할 수 있다
- **함수형 프로그래밍 스타일**: 팀이 함수형 프로그래밍에 익숙한 경우 자연스럽게 사용할 수 있다

Functional Builder는 함수형 프로그래밍의 장점을 Builder 패턴에 접목하여, 매우 유연하고 확장 가능한 객체 생성 메커니즘을 제공한다. 특히 C#의 LINQ와 같은 함수형 기능과 잘 어울리며, 복잡한 변환 로직을 우아하게 표현할 수 있다.

### 5. 상속을 활용한 Fluent Builder

복잡한 객체에서 여러 종류의 속성을 설정해야 할 때, 상속을 활용하여 책임을 분리하고 타입 안전성을 보장하는 고급 Builder 패턴이다. 이는 재귀적 제네릭 패턴(Curiously Recurring Template Pattern, CRTP)을 활용한다.

#### 구조

다음은 Person 객체를 생성하는 상속 기반 Fluent Builder의 예제이다:

```csharp
public class Person
{
    public string Name;
    public string Position;

    public class Builder : PersonJobBuilder<Builder>
    {
    }

    public static Builder New => new Builder();

    public override string ToString()
    {
        return $"{nameof(Name)}: {Name}, {nameof(Position)}: {Position}";
    }
}
```

**핵심 구조:**

1. **기본 Builder 클래스**
```csharp
public abstract class PersonBuilder
{
    protected Person person = new Person();

    public Person Build()
    {
        return person;
    }
}
```

2. **정보 설정을 담당하는 Builder**
```csharp
public class PersonInfoBuilder<SELF>
    : PersonBuilder
    where SELF : PersonInfoBuilder<SELF>
{
    public SELF Call(string name)
    {
        person.Name = name;
        return (SELF)this;
    }
}
```

3. **직업 설정을 담당하는 Builder**
```csharp
public class PersonJobBuilder<SELF> : PersonInfoBuilder<PersonJobBuilder<SELF>>
    where SELF : PersonJobBuilder<SELF>
{
    public SELF WorkAsA(string position)
    {
        person.Position = position;
        return (SELF)this;
    }
}
```

#### 핵심 개념

**1. 제네릭과 CRTP (Curiously Recurring Template Pattern)**
- `<SELF>` 제네릭 매개변수로 자기 자신의 타입을 전달한다
- 각 Builder 클래스가 올바른 타입으로 메서드 체이닝을 반환한다
- 컴파일 타임에 타입 안전성을 보장한다

**2. 책임 분리**
- `PersonInfoBuilder`: 개인 정보 설정을 담당한다
- `PersonJobBuilder`: 직업 정보 설정을 담당한다
- 각 Builder가 특정 영역의 속성만 관리한다

**3. 계층적 상속**
```
PersonBuilder (추상 기본 클래스)
    ↓
PersonInfoBuilder<SELF> (이름 설정)
    ↓
PersonJobBuilder<SELF> (직업 설정)
    ↓
Person.Builder (최종 구현 클래스)
```

#### 사용 예제

```csharp
public static void Main()
{
    var person = Person.New
        .Call("김개발")           // PersonInfoBuilder의 메서드
        .WorkAsA("소프트웨어 엔지니어")  // PersonJobBuilder의 메서드
        .Build();               // PersonBuilder의 메서드

    Console.WriteLine(person);
    // 출력: Name: 김개발, Position: 소프트웨어 엔지니어
}
```

#### 장점

**1. 타입 안전성**
- 컴파일 타임에 메서드 호출 순서와 타입을 검증한다
- 잘못된 타입 반환으로 인한 런타임 오류를 방지한다

**2. 확장성**
- 새로운 속성 그룹을 추가할 때 새로운 Builder 클래스만 추가하면 된다
- 기존 코드 수정 없이 기능을 확장할 수 있다

**3. 책임 분리**
- 각 Builder 클래스가 특정 영역의 속성만 담당한다
- 코드의 가독성과 유지보수성이 향상된다

**4. IntelliSense 지원**
- IDE에서 각 단계별로 사용 가능한 메서드만 표시된다
- 개발자 경험이 향상된다

#### 고급 활용 예제

```csharp
// 더 복잡한 Builder 계층 구조
public abstract class PersonBuilder
{
    protected Person person = new Person();
    public Person Build() => person;
}

public class PersonInfoBuilder<SELF> : PersonBuilder
    where SELF : PersonInfoBuilder<SELF>
{
    public SELF Call(string name)
    {
        person.Name = name;
        return (SELF)this;
    }
    
    public SELF BornOn(DateTime dateOfBirth)
    {
        person.DateOfBirth = dateOfBirth;
        return (SELF)this;
    }
}

public class PersonJobBuilder<SELF> : PersonInfoBuilder<PersonJobBuilder<SELF>>
    where SELF : PersonJobBuilder<SELF>
{
    public SELF WorkAsA(string position)
    {
        person.Position = position;
        return (SELF)this;
    }
    
    public SELF Earning(decimal salary)
    {
        person.Salary = salary;
        return (SELF)this;
    }
}

public class PersonEducationBuilder<SELF> : PersonJobBuilder<PersonEducationBuilder<SELF>>
    where SELF : PersonEducationBuilder<SELF>
{
    public SELF StudiedAt(string university)
    {
        person.Education = university;
        return (SELF)this;
    }
}
```

이러한 패턴은 특히 설정이 복잡한 객체나 여러 단계를 거쳐 생성되는 객체에서 매우 유용하며, 타입 안전성과 확장성을 동시에 제공한다.

## Builder 패턴과 다른 생성 패턴의 비교

### Builder vs Factory 패턴

| 특징          | Builder 패턴                | Factory 패턴            |
| ------------- | --------------------------- | ----------------------- |
| **목적**      | 복잡한 객체를 단계별로 생성 | 객체 생성 로직을 캡슐화 |
| **복잡도**    | 복잡한 객체에 적합          | 단순한 객체에 적합      |
| **유연성**    | 매우 높음 (단계별 설정)     | 중간 (타입 선택)        |
| **생성 과정** | 명시적이고 상세함           | 숨겨짐                  |

### Builder vs Prototype 패턴

| 특징          | Builder 패턴            | Prototype 패턴        |
| ------------- | ----------------------- | --------------------- |
| **생성 방식** | 처음부터 새로 생성      | 기존 객체를 복제      |
| **성능**      | 상대적으로 느림         | 빠름 (복제)           |
| **사용 시점** | 다양한 구성이 필요할 때 | 유사한 객체가 많을 때 |

## 언제 Builder 패턴을 사용할까?

Builder 패턴은 다음과 같은 상황에서 사용하는 것이 적합하다:

- **매개변수가 많은 생성자**: 4개 이상의 매개변수를 가진 객체를 생성할 때
- **선택적 매개변수**: 일부 매개변수만 설정하고 싶은 경우
- **복잡한 객체**: 여러 단계를 거쳐 생성되는 객체를 다룰 때
- **불변 객체**: 생성 후 변경되지 않아야 하는 객체를 만들 때
- **유효성 검증**: 객체 생성 과정에서 복잡한 검증이 필요한 경우
- **DSL 구현**: 도메인 특화 언어를 구현할 때

## Builder 패턴의 실제 활용 사례

### 1. StringBuilder (표준 라이브러리)
```csharp
var sb = new StringBuilder()
    .Append("Hello")
    .Append(" ")
    .Append("World")
    .AppendLine("!")
    .ToString();
```

### 2. LINQ 쿼리 빌더
```csharp
var query = dbContext.Users
    .Where(u => u.Age > 18)
    .OrderBy(u => u.Name)
    .Take(10)
    .ToList();
```

### 3. HTTP 요청 빌더
```csharp
var request = new HttpRequestBuilder()
    .SetMethod(HttpMethod.Post)
    .SetUrl("https://api.example.com/users")
    .AddHeader("Authorization", "Bearer token")
    .SetBody(jsonContent)
    .Build();
```

### 4. 설정 객체 생성
```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("appsettings.json", optional: false)
    .AddEnvironmentVariables()
    .Build();
```

## 결론

Builder 패턴은 복잡한 객체 생성을 단순화하고 코드의 가독성을 크게 향상시킨다. 특히 DSL(Domain Specific Language)을 만들 때나 설정 객체를 생성할 때 매우 유용하다. 메서드 체이닝을 통한 Fluent Interface는 코드를 자연어에 가깝게 만들어 개발자 경험을 향상시킨다.

각 Builder 패턴 변형은 서로 다른 상황에서 장점을 발휘한다:
- **Fluent Builder**: 가장 일반적이고 간단한 구현
- **Step Builder**: 순서가 중요한 경우
- **Faceted Builder**: 복잡한 도메인 객체
- **Functional Builder**: 극도의 유연성이 필요한 경우
- **상속 기반 Builder**: 타입 안전성과 확장성이 중요한 경우

프로젝트의 요구사항과 복잡도에 따라 적절한 Builder 패턴을 선택하여 사용하면, 유지보수가 용이하고 확장 가능한 코드를 작성할 수 있다.
