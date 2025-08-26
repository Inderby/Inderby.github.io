---
title: "디자인패턴: Prototype 패턴"
author: Dante
date: 2025-08-26 18:50:03 +0900
categories: [design-pattern, software-engineering]
tags: [design-pattern, prototype, clean-code, abstract-factory]
keywords: [design-pattern, prototype, clean-code, abstract-factory]
description: "Prototype 패턴의 특징과 각종 구현 방식에 대해 알아보자."
toc: true
toc_sticky: true
---

# Prototype 패턴과 C# ICloneable의 한계

## 개요

Prototype 패턴은 객체 생성 패턴 중 하나로, 기존 객체를 복사하여 새로운 객체를 생성하는 방식이다. C#에서는 `ICloneable` 인터페이스를 통해 이를 구현할 수 있으나, 여러 한계점이 존재한다.

## C# ICloneable의 문제점

### 1. 깊은 복사와 얕은 복사의 모호성

`ICloneable` 인터페이스는 `object Clone()` 메서드만을 정의하며, 이것이 깊은 복사(Deep Copy)인지 얕은 복사(Shallow Copy)인지 명확하지 않다.

```csharp
public class Person : ICloneable
{
    public string[] Names;
    private Address Address;

    public Person(string[] names, Address address)
    {
        Names = names;
        Address = address;
    }

    public object Clone()
    {
        return new Person((string[])Names.Clone(), (Address)Address.Clone());
    }
}
```

### 2. 참조 타입의 복사 문제

다음 코드는 일반적인 실수를 보여준다.

```csharp
static void Main(string[] args)
{
    var john = new Person(new[] { "John", "Smith" },
        new Address("London Road", 123));

    var jane = john; // 복사가 아닌 참조 할당
    jane.Names[0] = "Jane";
    WriteLine(john); // john의 이름도 "Jane"으로 변경됨
}
```

위 코드에서 `var jane = john;`은 `Clone()` 메서드를 사용하지 않고 단순히 참조를 복사한다. 이로 인해 `jane`의 변경사항이 `john`에도 영향을 미치게 된다.

올바른 사용법은 다음과 같다.

```csharp
var jane = (Person)john.Clone();
jane.Names[0] = "Jane";
```

### 3. 기존 방식의 한계점

#### 타입 캐스팅 필요
`Clone()` 메서드는 `object`를 반환하므로 명시적 캐스팅이 필요하다.

#### 배열의 얕은 복사 문제
`Names.Clone()`은 새로운 배열을 만들지만, 배열 내의 요소들은 여전히 같은 참조를 가진다. string은 불변(immutable) 타입이므로 이 경우에는 문제가 되지 않지만, 다른 참조 타입의 경우 문제가 될 수 있다.

## 개선된 해결책

### 1. 제네릭 인터페이스 정의

```csharp
public interface IDeepCopyable<T> where T : new()
{
    void CopyTo(T target);
    public T DeepCopy()
    {
        T t = new T();
        CopyTo(t);
        return t;
    }
}
```

이 접근법은 다음과 같은 장점을 제공한다.

- 타입 안전성: 제네릭을 사용하여 캐스팅 불필요
- 명확한 의미: "DeepCopy"로 깊은 복사임을 명시
- 기본 구현 제공: 인터페이스에서 기본 구현을 제공하여 중복 코드 감소

### 2. Address 클래스 구현

```csharp
public class Address : IDeepCopyable<Address>
{
    public string StreetName;
    public int HouseNumber;

    public Address() { }

    public Address(string streetName, int houseNumber)
    {
        StreetName = streetName;
        HouseNumber = houseNumber;
    }

    public void CopyTo(Address target)
    {
        target.StreetName = StreetName;
        target.HouseNumber = HouseNumber;
    } 

    public Address DeepCopy()
    {
        return (Address)MemberwiseClone();
    }
}
```

### 3. Person 클래스 구현

```csharp
public class Person : IDeepCopyable<Person>
{
    public string[] Names;
    public Address Address;

    public Person() { }
    
    public Person(string[] names, Address address)
    {
        Names = names;
        Address = address;
    }

    public void CopyTo(Person target)
    {
        target.Names = (string[])Names.Clone();
        target.Address = Address.DeepCopy();
    }

    public Person DeepCopy()
    {
        return new Person((string[])Names.Clone(), Address.DeepCopy());
    }
}
```

### 4. 상속 관계에서의 구현

```csharp
public class Employee : Person, IDeepCopyable<Employee>
{
    public int Salary;

    public Employee() { }

    public Employee(string[] names, Address address, int salary) : base(names, address)
    {
        Salary = salary;
    }

    public void CopyTo(Employee target)
    {
        base.CopyTo(target);
        target.Salary = Salary;
    }

    Employee DeepCopy()
    {
        return new Employee((string[])Names.Clone(), Address.DeepCopy(), Salary);
    }
}
```

## 직렬화를 활용한 대안

복잡한 객체 구조에서는 직렬화를 활용한 자동 깊은 복사를 고려할 수 있다.

### XML 직렬화 기반 복사

```csharp
public static class ExtensionMethods
{
    public static T DeepCopyXml<T>(this T self)
    {
        using (var ms = new MemoryStream())
        {
            var s = new XmlSerializer(typeof(T));
            s.Serialize(ms, self);
            ms.Position = 0;
            return (T)s.Deserialize(ms);
        }
    }
}
```

### Copy Constructor 패턴

```csharp
public class Person
{
    public string[] Names { get; set; }
    public Address Address { get; set; }

    public Person(string[] names, Address address)
    {
        Names = names;
        Address = address;
    }

    public Person(Person other)
    {
        Names = (string[])other.Names.Clone();
        Address = new Address(other.Address);
    }
}
```

## 접근법별 비교

### 수동 구현 방식
- 장점: 높은 성능, 세밀한 제어 가능
- 단점: 구현 복잡도 높음, 유지보수 비용

### 직렬화 방식
- 장점: 구현 간단, 복잡한 객체 그래프 자동 처리
- 단점: 성능 오버헤드, 메모리 사용량 증가

### Copy Constructor 방식
- 장점: 명확한 의도, 컴파일 타임 안전성
- 단점: 각 클래스마다 구현 필요

## 권장사항

프로젝트 특성에 따라 적절한 방법을 선택해야 한다.

- 간단한 객체 구조: Copy Constructor 또는 IDeepCopyable<T> 사용
- 복잡한 객체 그래프: XML Serialization 사용
- 고성능이 중요한 경우: 수동 구현 방식 사용
- 개발 속도가 중요한 경우: 직렬화 기반 접근법 사용

## 결론

C#의 `ICloneable` 인터페이스는 모호한 복사 의미, 타입 안전성 부족, 구현의 복잡성 등 여러 한계점을 가진다. 이러한 문제들로 인해 Microsoft는 새로운 API에서 `ICloneable` 사용을 권장하지 않는다.

대신 제네릭 인터페이스를 활용한 명시적 복사 메서드나 Copy Constructor 패턴을 사용하는 것이 바람직하다. 복잡한 객체의 경우 직렬화를 활용한 자동화된 접근법도 고려할 수 있으나, 성능과 메모리 사용량을 신중히 검토해야 한다.

Prototype 패턴을 구현할 때는 복사의 깊이를 명확히 하고, 타입 안전성과 성능을 모두 고려한 설계를 해야 한다.