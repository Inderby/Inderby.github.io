---
title: "디자인패턴: Factory 패턴"
author: Dante
date: 2025-08-25 18:50:03 +0900
categories: [design-pattern, software-engineering]
tags: [design-pattern, factory, clean-code, abstract-factory]
keywords: [design-pattern, factory, clean-code, abstract-factory]
description: "Factory 패턴의 특징과 각종 구현 방식에 대해 알아보자."
toc: true
toc_sticky: true
---

# 팩토리 패턴 완벽 가이드

## 1. 서론

팩토리 패턴은 객체 지향 프로그래밍에서 가장 널리 사용되는 생성 패턴(Creational Pattern) 중 하나이다. 이 패턴은 객체 생성 로직을 캡슐화하여 클라이언트 코드가 구체적인 클래스에 의존하지 않고도 객체를 생성할 수 있도록 한다. 본 문서에서는 팩토리 메서드 패턴부터 추상 팩토리 패턴까지, 다양한 팩토리 패턴의 구현과 활용 방법을 체계적으로 살펴본다.

## 2. 팩토리 메서드 패턴 (Factory Method Pattern)

### 2.1 개념과 필요성

일반적으로 객체를 생성할 때 생성자를 직접 호출하는 방식은 다음과 같은 한계를 갖는다:

- **복잡한 생성 로직**: 객체 생성 시 복잡한 계산이나 변환이 필요한 경우 생성자로는 처리가 어렵다
- **다양한 생성 방식**: 같은 타입의 매개변수를 받는 여러 생성자를 만들 수 없다
- **의도 표현의 어려움**: 생성자만으로는 어떤 방식으로 객체를 생성하는지 명확히 표현하기 어렵다

팩토리 메서드 패턴은 정적 메서드를 통해 이러한 문제를 해결한다.

### 2.2 기본 구현

다음은 좌표계 변환을 포함한 Point 클래스의 구현 예제이다:

```csharp
public class Point
{
    private double x, y;

    // 생성자를 private으로 선언하여 직접 생성을 방지
    private Point(double x, double y)
    {
        this.x = x;
        this.y = y;
    }

    // 데카르트 좌표계로 Point 생성
    public static Point NewCartesianPoint(double x, double y)
    {
        return new Point(x, y);
    }

    // 극좌표계로 Point 생성
    public static Point NewPolarPoint(double rho, double theta)
    {
        return new Point(rho * Math.Cos(theta), rho * Math.Sin(theta));
    }
}
```

이 구현의 핵심은 생성자를 `private`으로 선언하여 외부에서 직접 객체를 생성하는 것을 방지하고, 명확한 이름을 가진 팩토리 메서드를 통해서만 객체를 생성하도록 강제하는 것이다.

### 2.3 비동기 팩토리 메서드 패턴

현대 애플리케이션에서는 객체 초기화 과정에서 네트워크 호출이나 파일 I/O와 같은 비동기 작업이 필요한 경우가 많다. 생성자는 `async`를 지원하지 않으므로, 비동기 팩토리 메서드 패턴을 활용해야 한다.

```csharp
public class DatabaseConnection
{
    private string connectionString;
    private bool isConnected;

    public static async Task<DatabaseConnection> CreateAsync(string connectionString)
    {
        var connection = new DatabaseConnection(connectionString);
        await connection.ConnectAsync();
        return connection;
    }

    private DatabaseConnection(string connectionString)
    {
        this.connectionString = connectionString;
        this.isConnected = false;
    }

    private async Task ConnectAsync()
    {
        // 실제 데이터베이스 연결 로직
        await Task.Delay(2000); // 연결 시뮬레이션
        this.isConnected = true;
    }
}
```

## 3. 팩토리 패턴의 접근 제어

### 3.1 접근 제어의 딜레마

팩토리 패턴 구현 시 가장 흔히 마주치는 문제는 생성자의 접근성 제어이다. 생성자를 `private`으로 만들면 팩토리가 접근할 수 없고, `public`으로 두면 외부에서 직접 생성할 수 있어 팩토리 패턴의 목적에 어긋난다.

### 3.2 해결 방안

#### Internal 생성자 활용
라이브러리 개발 시 생성자를 `internal`로 선언하여 같은 어셈블리 내에서만 접근 가능하도록 제한할 수 있다.

```csharp
public class Point
{
    internal Point(double x, double y)
    {
        this.x = x;
        this.y = y;
    }
}
```

#### 내부 클래스 패턴
팩토리를 클래스의 내부 클래스로 구현하여 `private` 생성자에 접근 가능하게 한다.

```csharp
public class Point
{
    private Point(double x, double y)
    {
        this.x = x;
        this.y = y;
    }

    public static class Factory
    {
        public static Point NewCartesianPoint(double x, double y)
        {
            return new Point(x, y);  // private 생성자 접근 가능
        }

        public static Point NewPolarPoint(double rho, double theta)
        {
            return new Point(rho * Math.Cos(theta), rho * Math.Sin(theta));
        }
    }
}
```

## 4. 고급 팩토리 패턴

### 4.1 객체 추적 팩토리 (Tracking Factory)

생성된 객체들을 추적하고 관리해야 하는 경우, `WeakReference`를 활용한 추적 팩토리를 구현할 수 있다.

```csharp
public class TrackingThemeFactory
{
    private readonly List<WeakReference<ITheme>> themes = new();
    
    public ITheme CreateTheme(bool dark)
    {
        ITheme theme = dark ? new DarkTheme() : new LightTheme();
        themes.Add(new WeakReference<ITheme>(theme));
        return theme;
    }

    public string Info
    {
        get
        {
            var sb = new StringBuilder();
            foreach (var reference in themes)
            {
                if (reference.TryGetTarget(out var theme))
                {
                    bool dark = theme is DarkTheme;
                    sb.Append(dark ? "Dark" : "Light").Append(" theme ");
                }
            }
            return sb.ToString();
        }
    }
}
```

### 4.2 일괄 교체 팩토리 (Replaceable Factory)

런타임에 생성된 모든 객체를 일괄적으로 교체해야 하는 경우, 래퍼 클래스를 활용한 교체 가능 팩토리를 구현할 수 있다.

```csharp
public class Ref<T> where T : class
{
    public T Value;
    public Ref(T value) => Value = value;
}

public class ReplaceableThemeFactory
{
    private readonly List<WeakReference<Ref<ITheme>>> themes = new();

    public Ref<ITheme> CreateTheme(bool dark)
    {
        var r = new Ref<ITheme>(dark ? new DarkTheme() : new LightTheme());
        themes.Add(new(r));
        return r;
    }

    public void ReplaceTheme(bool dark)
    {
        foreach (var wr in themes)
        {
            if (wr.TryGetTarget(out var reference))
            {
                reference.Value = dark ? new DarkTheme() : new LightTheme();
            }
        }
    }
}
```

## 5. 추상 팩토리 패턴 (Abstract Factory Pattern)

### 5.1 개념과 목적

추상 팩토리 패턴은 팩토리 메서드 패턴의 확장된 형태로, 관련된 객체들의 패밀리를 생성하는 인터페이스를 제공한다. 이 패턴은 구체적인 클래스를 지정하지 않고도 관련성이 있거나 의존적인 객체들의 집합을 생성할 수 있게 한다.

### 5.2 구현 예제

음료 자판기를 예로 들어 추상 팩토리 패턴을 구현한다:

```csharp
// 제품 인터페이스
public interface IHotDrink
{
    void Consume();
}

// 추상 팩토리 인터페이스
public interface IHotDrinkFactory
{
    IHotDrink Prepare(int amount);
}

// 구체적인 팩토리
internal class TeaFactory : IHotDrinkFactory
{
    public IHotDrink Prepare(int amount)
    {
        WriteLine($"Put in a tea bag, boil water, pour {amount} ml, add lemon, enjoy!");
        return new Tea();
    }
}

// 팩토리 관리자
public class HotDrinkMachine
{
    private List<Tuple<string, IHotDrinkFactory>> factories = new();
    
    public HotDrinkMachine()
    {
        // 리플렉션을 통한 자동 팩토리 등록
        foreach (var t in typeof(HotDrinkMachine).Assembly.GetTypes())
        {
            if (typeof(IHotDrinkFactory).IsAssignableFrom(t) && !t.IsInterface)
            {
                factories.Add(Tuple.Create(
                    t.Name.Replace("Factory", string.Empty),
                    (IHotDrinkFactory)Activator.CreateInstance(t)
                ));
            }
        }
    }

    public IHotDrink MakeDrink()
    {
        WriteLine("Available drinks:");
        for (var index = 0; index < factories.Count; index++)
        {
            WriteLine($"{index}: {factories[index].Item1}");
        }
        
        // 사용자 입력 처리 및 음료 생성
        // ...
    }
}
```

### 5.3 리플렉션을 통한 동적 팩토리 등록

위 구현에서는 리플렉션을 활용하여 `IHotDrinkFactory`를 구현하는 모든 클래스를 자동으로 발견하고 등록한다. 이를 통해 새로운 팩토리 클래스를 추가할 때 기존 코드를 수정할 필요가 없어진다.

## 6. 패턴 선택 가이드

### 6.1 팩토리 메서드 패턴을 선택해야 하는 경우

- 객체 생성 시 복잡한 로직이나 계산이 필요한 경우
- 같은 타입의 매개변수로 다양한 방식의 객체 생성이 필요한 경우
- 객체 생성 방식을 명확히 표현하고자 하는 경우
- 비동기 초기화가 필요한 경우

### 6.2 추상 팩토리 패턴을 선택해야 하는 경우

- 관련 객체들의 패밀리를 생성해야 하는 경우
- 플랫폼이나 환경에 따라 다른 구현이 필요한 경우
- 객체 생성 로직이 복잡하고 일관성이 중요한 경우
- 런타임에 객체 타입을 결정해야 하는 경우

## 7. 성능과 메모리 고려사항

### 7.1 정적 팩토리 메서드
- **장점**: 간단하고 직관적인 구현
- **단점**: 정적 메서드는 메모리에 항상 로드되어 있어야 함

### 7.2 리플렉션 기반 팩토리
- **장점**: 완전 자동화된 팩토리 등록
- **단점**: 초기화 시간 증가 및 런타임 타입 오류 가능성

### 7.3 WeakReference 활용
- **장점**: 메모리 누수 방지 및 자동 가비지 컬렉션
- **단점**: 추가적인 메모리 오버헤드 및 접근 시 성능 비용

## 8. 실제 적용 사례

팩토리 패턴은 다음과 같은 실제 시나리오에서 널리 활용된다:

- **UI 컴포넌트 생성**: 플랫폼별로 다른 UI 컴포넌트 생성
- **데이터베이스 연결**: 다양한 데이터베이스 엔진에 대한 연결 객체 생성
- **테마 시스템**: 런타임에 변경 가능한 테마 객체 관리
- **플러그인 아키텍처**: 동적으로 로드되는 플러그인 팩토리 관리

## 9. 결론

팩토리 패턴은 객체 지향 설계에서 필수적인 패턴이다. 단순한 팩토리 메서드부터 복잡한 추상 팩토리까지, 각 상황에 맞는 적절한 패턴을 선택하여 사용하는 것이 중요하다. 

특히 현대 애플리케이션 개발에서는 비동기 팩토리 메서드와 같은 고급 패턴의 활용이 점점 더 중요해지고 있다. 또한 리플렉션을 활용한 동적 팩토리 등록이나 WeakReference를 통한 메모리 효율적인 객체 관리 등의 기법을 적절히 활용하면, 더욱 유연하고 확장 가능한 시스템을 구축할 수 있다.

패턴을 적용할 때는 항상 복잡성과 이득 사이의 균형을 고려해야 한다. 과도한 추상화는 오히려 시스템을 복잡하게 만들 수 있으므로, 프로젝트의 요구사항과 규모에 맞는 적절한 수준의 패턴을 선택하는 것이 중요하다.