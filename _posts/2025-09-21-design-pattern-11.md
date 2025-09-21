---
title: "디자인패턴: Proxy 패턴"
author: Dante
date: 2025-09-21 16:50:03 +0900
categories: [design-pattern, software-engineering]
tags: [design-pattern, proxy , clean-code]
keywords: [design-pattern, proxy, clean-code]
description: "proxy 패턴의 특징과 각종 구현 방식에 대해 알아보자."
toc: true
toc_sticky: true
---

# Proxy 패턴: 객체 접근의 대리자

## 개요

Proxy 패턴은 다른 객체에 대한 접근을 제어하기 위해 대리자 또는 플레이스홀더를 제공하는 구조적 디자인 패턴이다. 실제 객체에 대한 간접적인 접근을 통해 추가적인 기능을 제공하거나 접근을 제한할 수 있다.

## Proxy 패턴의 주요 유형과 구현

### 1. Protection Proxy (보호 프록시)

보호 프록시는 실제 객체에 대한 접근 권한을 제어한다. 특정 조건을 만족하는 경우에만 실제 객체의 메서드를 호출할 수 있도록 한다.

```csharp
public interface ICar
{
    void Drive();
}

public class CarProxy : ICar
{
    private Driver driver;
    private Car car = new Car();

    public CarProxy(Driver driver)
    {
        this.driver = driver;
    }

    public void Drive()
    {
        if (driver.Age >= 16)
        {
            car.Drive();
        }
        else
        {
            Console.WriteLine("Too Young");
        }
    }
}
```

위 예제에서 `CarProxy`는 운전자의 나이를 확인하여 16세 이상인 경우에만 실제 `Car` 객체의 `Drive()` 메서드를 호출한다.

### 2. Property Proxy (속성 프록시)

속성 프록시는 속성 값의 변경을 감지하고 추가적인 동작을 수행할 수 있도록 한다. C#의 속성 기능과 제네릭을 활용하여 구현할 수 있다.

```csharp
public class Property<T> where T : new()
{
    private T value;

    public T Value
    {
        get => value;
        set
        {
            if (Equals(this.value, value)) return;
            Console.WriteLine($"Assigning value to {value}");
            this.value = value;
        }
    }

    public static implicit operator T(Property<T> property)
    {
        return property.value;
    }

    public static implicit operator Property<T>(T value)
    {
        return new Property<T>(value);
    }
}
```

Property 프록시는 값이 실제로 변경되는 경우에만 로깅이나 알림 같은 추가 동작을 수행한다. 암시적 변환 연산자를 통해 일반 타입처럼 사용할 수 있다.

### 3. Dynamic Proxy (동적 프록시)

동적 프록시는 런타임에 프록시 객체를 생성하며, `DynamicObject`를 상속받아 구현할 수 있다. 메서드 호출을 가로채어 로깅, 카운팅 등의 부가 기능을 제공한다.

```csharp
public class Log<T> : DynamicObject where T : class, new()
{
    private readonly T subject;
    private Dictionary<string, int> methodCallCount = new Dictionary<string, int>();

    public override bool TryInvokeMember(InvokeMemberBinder binder, 
        object?[]? args, out object? result)
    {
        Console.WriteLine($"Invoking {subject.GetType().Name}.{binder.Name}");
        
        if (methodCallCount.ContainsKey(binder.Name)) 
            methodCallCount[binder.Name]++;
        else 
            methodCallCount.Add(binder.Name, 1);

        result = subject.GetType().GetMethod(binder.Name).Invoke(subject, args);
        return true;
    }
}
```

동적 프록시는 메서드 호출을 인터셉트하여 호출 횟수를 기록하고 로깅을 수행한다.

### 4. Value Proxy (값 프록시)

값 프록시는 기본 타입을 래핑하여 타입 안정성과 의미론적 명확성을 제공한다. 구조체와 연산자 오버로딩을 활용하여 구현한다.

```csharp
public struct Percentage
{
    private readonly float value;

    internal Percentage(float value)
    {
        this.value = value;
    }

    public static float operator *(float f, Percentage p)
    {
        return f * p.value;
    }
}

public static class PercentageExtensions
{
    public static Percentage Percent(this int value)
    {
        return new Percentage(value / 100.0f);
    }
}
```

`Percentage` 구조체는 퍼센트 값을 명시적으로 표현하며, 확장 메서드를 통해 자연스러운 문법(`5.Percent()`)을 제공한다.

### 5. Composite Proxy with Array-Backed Properties

복합 프록시 패턴은 여러 속성을 배열로 관리하면서 개별 속성처럼 접근할 수 있도록 한다.

```csharp
public class MasonrySettings
{
    private readonly bool[] flags = new bool[3];

    public bool? All
    {
        get
        {
            if (flags.Skip(1).All(f => f == flags[0]))
                return flags[0];
            return null;
        }
        set
        {
            if (!value.HasValue) return;
            for (int i = 0; i < flags.Length; i++)
            {
                flags[i] = value.Value;
            }
        }
    }

    public bool Pillars
    {
        get => flags[0];
        set => flags[0] = value;
    }
    // ... 다른 속성들
}
```

이 패턴은 관련된 여러 설정을 효율적으로 관리하면서 일괄 설정 기능(`All` 속성)을 제공한다.

### 6. Structure of Arrays (SoA) Proxy

SoA 프록시는 메모리 효율성을 위해 객체의 배열(Array of Structures)을 구조의 배열(Structure of Arrays)로 변환한다.

```csharp
class Creatures
{
    private readonly int size;
    private byte[] age;
    private int[] x, y;

    public struct CreatureProxy
    {
        private readonly Creatures creatures;
        private readonly int index;

        public ref byte Age => ref creatures.age[index];
        public ref int X => ref creatures.x[index];
        public ref int Y => ref creatures.y[index];
    }
    
    public IEnumerator<CreatureProxy> GetEnumerator()
    {
        for (int pos = 0; pos < size; ++pos)
        {
            yield return new CreatureProxy(this, pos);
        }
    }
}
```

이 방식은 캐시 지역성을 개선하여 특정 속성만 접근하는 경우 성능상 이점을 제공한다.

## 고급 활용: Bit Flagging과 Expression Evaluation

첨부된 `BitFlagging.cs`는 프록시 패턴의 고급 활용 사례를 보여준다. `TwoBitSet` 클래스는 비트 연산을 통해 메모리 효율적인 데이터 저장을 구현한다.

```csharp
public class TwoBitSet
{
    private readonly ulong data;

    public byte this[int index]
    {
        get
        {
            var shift = index << 1;
            ulong mask = (0b11U << shift);
            return (byte)((data & mask) >> shift);
        }
    }
}
```

이는 각 연산자를 2비트로 표현하여 최대 32개의 연산자를 하나의 `ulong` 변수에 저장할 수 있게 한다.

## Proxy 패턴의 장점

1. **접근 제어**: 실제 객체에 대한 접근을 제어하고 권한을 관리할 수 있다.
2. **지연 초기화**: 무거운 객체의 생성을 실제로 필요한 시점까지 미룰 수 있다.
3. **로깅 및 감사**: 객체 접근에 대한 로깅과 모니터링을 투명하게 추가할 수 있다.
4. **캐싱**: 비용이 많이 드는 작업의 결과를 캐싱할 수 있다.
5. **타입 안정성**: 값 프록시를 통해 기본 타입에 의미론적 타입 안정성을 부여할 수 있다.

## 고려사항

프록시 패턴을 사용할 때는 다음 사항들을 고려해야 한다:

- **성능 오버헤드**: 간접 호출로 인한 성능 저하가 발생할 수 있다.
- **복잡성 증가**: 코드의 복잡도가 증가하여 디버깅이 어려워질 수 있다.
- **인터페이스 일치**: 프록시는 실제 객체와 동일한 인터페이스를 구현해야 한다.

## 결론

Proxy 패턴은 객체 지향 설계에서 매우 유용한 패턴으로, 객체 접근을 제어하고 부가 기능을 추가하는 우아한 방법을 제공한다. C#의 다양한 기능들(속성, 연산자 오버로딩, 동적 객체, ref 반환 등)과 결합하여 더욱 강력하고 표현력 있는 프록시를 구현할 수 있다. 적절한 상황에서 올바르게 적용한다면 코드의 유지보수성과 확장성을 크게 향상시킬 수 있다.