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

# 빌더 패턴 완벽 가이드

## 📌 빌더 패턴이란?

빌더 패턴은 복잡한 객체를 단계별로 생성하기 위한 생성 디자인 패턴입니다. 특히 많은 매개변수를 가진 객체나 선택적 매개변수가 많은 객체를 생성할 때 유용합니다.

### 왜 빌더 패턴이 필요한가?

복잡한 객체 생성 시 발생하는 문제들:

1. **텔레스코핑 생성자 문제**: 매개변수가 늘어날수록 생성자가 복잡해짐
2. **가독성 저하**: 어떤 매개변수가 무엇을 의미하는지 파악 어려움
3. **실수 위험**: 매개변수 순서를 착각하기 쉬움
4. **유효성 검증 분산**: 객체 생성 과정에서 일관된 검증이 어려움

## 🎯 기본 빌더 패턴 구현

### HTML 요소 생성 예제

가장 기본적인 Fluent Builder 패턴을 HTML 요소 생성을 통해 알아보겠습니다.

```csharp
// HTML 요소 클래스
public class HtmlElement
{
    public string Name, Text;
    public List<HtmlElement> Elements = new List<HtmlElement>();
    private const int indentSize = 2;

    public HtmlElement(string name, string text)
    {
        Name = name ?? throw new ArgumentNullException(nameof(name));
        Text = text ?? string.Empty;
    }

    public override string ToString()
    {
        return ToStringImpl(0);
    }

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
}

// HTML 빌더 클래스
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

    public override string ToString() => root.ToString();

    public void Clear()
    {
        root = new HtmlElement { Name = rootName };
    }
}
```

**사용 예제:**

```csharp
var html = new HtmlBuilder("ul")
    .AddChild("li", "hello")
    .AddChild("li", "world");

Console.WriteLine(html.ToString());
// 출력:
// <ul>
//   <li>
//     hello
//   </li>
//   <li>
//     world
//   </li>
// </ul>
```

## 🚀 고급 빌더 패턴 변형

### 1. Step Builder - 순서가 중요할 때

Step Builder는 객체 생성 과정을 명확한 단계로 나누고, 컴파일 타임에 올바른 순서를 강제합니다.

```csharp
// 단계별 인터페이스 정의
public interface ISpecifyCarType
{
    ISpecifyWheelSize OfType(CarType type);
}

public interface ISpecifyWheelSize
{
    IBuildCar WithWheels(int size);
}

public interface IBuildCar
{
    Car Build();
}

// Step Builder 구현
public class CarBuilder
{
    private class Impl : ISpecifyCarType, ISpecifyWheelSize, IBuildCar
    {
        private Car car = new Car();

        public ISpecifyWheelSize OfType(CarType type)
        {
            car.Type = type;
            return this;
        }

        public IBuildCar WithWheels(int size)
        {
            // 타입별 휠 크기 검증
            switch (car.Type)
            {
                case CarType.Crossover when size < 17 || size > 20:
                case CarType.Sedan when size < 15 || size > 17:
                    throw new ArgumentException($"Wrong size of wheel for {car.Type}.");
            }
            car.WheelSize = size;
            return this;
        }

        public Car Build() => car;
    }

    public static ISpecifyCarType Create() => new Impl();
}

// 사용 예제
var car = CarBuilder.Create()
    .OfType(CarType.Sedan)      // 1단계: 필수
    .WithWheels(16)             // 2단계: 필수
    .Build();                   // 3단계: 완료

// 컴파일 에러 - 순서를 지키지 않으면 컴파일되지 않음
// CarBuilder.Create().WithWheels(16); // 에러!
```

### 2. Faceted Builder - 복잡한 객체의 영역별 구성

Faceted Builder는 복잡한 객체를 논리적 영역으로 분할하여 각 영역별 전문 빌더를 제공합니다.

```csharp
public class Person
{
    // 주소 정보
    public string StreetAddress, Postcode, City;
    // 직업 정보  
    public string CompanyName, Position;
    public int AnnualIncome;
}

// 메인 Facade Builder
public class PersonBuilder
{
    protected Person person = new Person();

    public PersonJobBuilder Works => new PersonJobBuilder(person);
    public PersonAddressBuilder Lives => new PersonAddressBuilder(person);

    public static implicit operator Person(PersonBuilder pb) => pb.person;
}

// 주소 빌더
public class PersonAddressBuilder : PersonBuilder
{
    public PersonAddressBuilder(Person person)
    {
        this.person = person;
    }

    public PersonAddressBuilder At(string streetAddress)
    {
        person.StreetAddress = streetAddress;
        return this;
    }

    public PersonAddressBuilder In(string city)
    {
        person.City = city;
        return this;
    }
}

// 직업 빌더
public class PersonJobBuilder : PersonBuilder
{
    public PersonJobBuilder(Person person)
    {
        this.person = person;
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
}

// 사용 예제
Person person = new PersonBuilder()
    .Lives                      // 주소 영역으로 전환
        .At("123 London Road")
        .In("London")
    .Works                      // 직업 영역으로 전환
        .At("Fabrikam")
        .AsA("Engineer");
```

### 3. Functional Builder - 극도의 유연성

함수형 프로그래밍 개념을 활용하여 매우 유연한 빌더를 구현합니다.

```csharp
public abstract class FunctionalBuilder<TSubject, TSelf>
    where TSelf : FunctionalBuilder<TSubject, TSelf>
    where TSubject : new()
{
    private readonly List<Func<TSubject, TSubject>> actions = new();

    public TSelf Do(Action<TSubject> action) => AddAction(action);

    public TSubject Build() => actions.Aggregate(new TSubject(), (p, f) => f(p));

    private TSelf AddAction(Action<TSubject> action)
    {
        actions.Add(p => { action(p); return p; });
        return (TSelf)this;
    }
}

// Person Builder 구현
public sealed class PersonBuilder : FunctionalBuilder<Person, PersonBuilder>
{
    public PersonBuilder Called(string name) => Do(p => p.Name = name);
}

// 확장 메서드로 기능 추가
public static class PersonBuilderExtensions
{
    public static PersonBuilder WorksAs(this PersonBuilder builder, string position) 
        => builder.Do(p => p.Position = position);
}

// 사용 예제
var person = new PersonBuilder()
    .Called("Sarah")
    .WorksAs("Developer")
    .Do(p => p.Name = p.Name.ToUpper())  // 임의의 변환 가능
    .Build();
```

## 📊 빌더 패턴 변형 비교

| 패턴                   | 장점                                  | 단점                                   | 사용 시점               |
| ---------------------- | ------------------------------------- | -------------------------------------- | ----------------------- |
| **Fluent Builder**     | • 간단한 구현<br>• 직관적 API         | • 순서 강제 없음<br>• 타입 안전성 부족 | 일반적인 객체 생성      |
| **Step Builder**       | • 컴파일 타임 검증<br>• 순서 강제     | • 복잡한 구현<br>• 유연성 부족         | 필수 단계가 명확한 경우 |
| **Faceted Builder**    | • 책임 분리<br>• 확장성 우수          | • 구현 복잡도 증가                     | 복잡한 도메인 객체      |
| **Functional Builder** | • 극도의 유연성<br>• 확장 메서드 활용 | • 성능 오버헤드<br>• 타입 안전성 부족  | 동적 구성이 필요한 경우 |

## 💡 실제 활용 사례

### 1. StringBuilder (표준 라이브러리)
```csharp
var sb = new StringBuilder()
    .Append("Hello")
    .Append(" ")
    .AppendLine("World!");
```

### 2. LINQ 쿼리
```csharp
var query = dbContext.Users
    .Where(u => u.Age > 18)
    .OrderBy(u => u.Name)
    .Take(10);
```

### 3. ASP.NET Core 설정
```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("appsettings.json")
    .AddEnvironmentVariables()
    .Build();
```

### 4. HTTP 요청 빌더
```csharp
var request = new HttpRequestBuilder()
    .SetMethod(HttpMethod.Post)
    .SetUrl("https://api.example.com/users")
    .AddHeader("Authorization", "Bearer token")
    .SetBody(jsonContent)
    .Build();
```

## ✅ 빌더 패턴 사용 지침

### 언제 사용할까?

- 생성자 매개변수가 4개 이상일 때
- 선택적 매개변수가 많을 때
- 객체 생성 과정에서 복잡한 검증이 필요할 때
- 불변 객체를 생성해야 할 때
- DSL(Domain Specific Language)을 구현할 때

### 언제 피해야 할까?

- 단순한 객체 (매개변수 3개 이하)
- 모든 필드가 필수인 경우
- 성능이 극도로 중요한 경우

## 핵심 정리

빌더 패턴은 복잡한 객체 생성을 단순화하고 코드의 가독성을 크게 향상시키는 강력한 패턴입니다. 

**핵심 이점:**
- **가독성**: 메서드 체이닝으로 자연어에 가까운 코드 작성
- **유연성**: 선택적 매개변수를 우아하게 처리
- **유지보수성**: 새로운 매개변수 추가가 용이
- **안전성**: 객체 생성 과정에서 일관된 검증 가능

프로젝트의 요구사항과 복잡도에 따라 적절한 빌더 패턴 변형을 선택하여 사용하면, 유지보수가 용이하고 확장 가능한 코드를 작성할 수 있습니다.