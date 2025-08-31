---
title: "디자인패턴: Singleton 패턴"
author: Dante
date: 2025-08-29 18:50:03 +0900
categories: [design-pattern, software-engineering]
tags: [design-pattern, singleton , clean-code, abstract-factory]
keywords: [design-pattern, singleton, clean-code, abstract-factory]
description: "singleton 패턴의 특징과 각종 구현 방식에 대해 알아보자."
toc: true
toc_sticky: true
---

# Singleton Design Pattern in C#

## 개요

Singleton 패턴은 클래스의 인스턴스가 오직 하나만 생성되도록 보장하는 생성 패턴(Creational Pattern)이다. 전역적으로 접근 가능한 단일 인스턴스를 제공하며, 시스템 전반에서 공유되는 리소스나 설정을 관리할 때 유용하다.

## 기본 구조

### Thread-Safe Lazy Singleton 구현

```csharp
public class SingletonDatabase : IDatabase
{
    private Dictionary<string, int> capitals;
    
    private SingletonDatabase()
    {
        Console.WriteLine("Initializing database");
        
        capitals = File.ReadAllLines("capitals.txt")
            .Chunk(2)
            .ToDictionary(
                list => list.ElementAt(0).Trim(),
                list => int.Parse(list.ElementAt(1))
            );
    }
    
    private static Lazy<SingletonDatabase> instance = 
        new Lazy<SingletonDatabase>(() => new SingletonDatabase());
    
    public static SingletonDatabase Instance => instance.Value;
    
    public int GetPopulation(string name)
    {
        return capitals[name];
    }
}
```

### 주요 특징

1. **Private Constructor**: 외부에서 직접 인스턴스를 생성할 수 없도록 생성자를 private으로 선언한다.
2. **Lazy Initialization**: `Lazy<T>` 클래스를 사용하여 실제로 필요할 때까지 인스턴스 생성을 지연시킨다.
3. **Thread Safety**: `Lazy<T>`가 자동으로 스레드 안전성을 보장한다.
4. **Global Access Point**: `Instance` 프로퍼티를 통해 전역적으로 접근 가능하다.

## Singleton의 문제점과 해결책

### 1. 테스트의 어려움

Singleton은 전역 상태를 만들어 단위 테스트를 어렵게 만든다. 다음은 하드코딩된 Singleton 의존성의 예시이다:

```csharp
public class SingletonRecordFinder
{
    public int GetTotalPopulation(IEnumerable<string> names)
    {
        int result = 0;
        foreach (var name in names)
        {
            // 하드코딩된 Singleton 의존성
            result += SingletonDatabase.Instance.GetPopulation(name);
        }
        return result;
    }
}
```

이 클래스는 실제 파일 시스템에 의존하고 테스트 데이터를 제어할 수 없어 테스트가 어렵다.

### 2. 의존성 주입을 통한 해결

```csharp
public class ConfigurableRecordFinder
{
    private IDatabase database;

    public ConfigurableRecordFinder(IDatabase database)
    {
        this.database = database ?? throw new ArgumentNullException(nameof(database));
    }

    public int GetTotalPopulation(IEnumerable<string> names)
    {
        int result = 0;
        foreach (var name in names)
        {
            result += database.GetPopulation(name);
        }
        return result;
    }
}
```

의존성 주입을 통해 테스트용 Mock 객체를 주입할 수 있다:

```csharp
public class DummyDatabase : IDatabase
{
    public int GetPopulation(string name)
    {
        return new Dictionary<string, int>
        {
            ["alpha"] = 1,
            ["beta"] = 2,
            ["gamma"] = 3
        }[name];
    }
}
```

## 테스트 구현

### 1. Singleton 인스턴스 검증

```csharp
[Test]
public void IsSingletonTest()
{
    var db1 = SingletonDatabase.Instance;
    var db2 = SingletonDatabase.Instance;
    Assert.That(db1, Is.SameAs(db2));
}
```

### 2. 의존성 주입 활용 테스트

```csharp
[Test]
public void ConfigurablePopulationTest()
{
    var rf = new ConfigurableRecordFinder(new DummyDatabase());
    var names = new[] { "alpha", "gamma" };
    int tp = rf.GetTotalPopulation(names);
    
    Assert.That(tp, Is.EqualTo(4));
}
```

### 3. IoC 컨테이너를 통한 Singleton 관리

```csharp
[Test]
public void DIPopulationTest()
{
    var cb = new ContainerBuilder();
    cb.RegisterType<OrdinaryDatabase>()
        .As<IDatabase>()
        .SingleInstance();
    cb.RegisterType<ConfigurableRecordFinder>();

    using (var c = cb.Build())
    {
        var rf = c.Resolve<ConfigurableRecordFinder>();
    }
}
```

## 최적 실천 방법

### 1. Interface 기반 설계

인터페이스를 정의하여 구현체를 교체 가능하게 만드는 것이 바람직하다.

### 2. IoC 컨테이너 활용

직접 Singleton을 구현하는 대신 IoC 컨테이너(Autofac, Unity, Microsoft.Extensions.DependencyInjection 등)를 사용하여 생명주기를 관리하는 것이 권장된다.

### 3. Thread Safety 고려

- `Lazy<T>` 사용 (권장)
- Double-checked locking
- Static constructor 활용

## Per-Thread Singleton

Per-Thread Singleton은 각 스레드별로 독립적인 Singleton 인스턴스를 제공하는 패턴이다.

### 구현

```csharp
public sealed class PerThreadSingleton
{
    private static ThreadLocal<PerThreadSingleton> threadInstance
      = new ThreadLocal<PerThreadSingleton>(() => new PerThreadSingleton());

    public int Id;
    
    private PerThreadSingleton()
    {
        Id = Thread.CurrentThread.ManagedThreadId;
    }

    public static PerThreadSingleton Instance => threadInstance.Value;
}
```

### 특징

1. **ThreadLocal<T> 사용**: 각 스레드별로 독립적인 인스턴스를 관리한다.
2. **Thread Isolation**: 스레드 간 간섭이 없다.
3. **자동 정리**: 스레드 종료 시 인스턴스가 자동으로 정리된다.

### 사용 사례

- 스레드별 상태 관리가 필요한 경우
- 스레드 간 동기화 오버헤드를 피하고자 하는 경우
- 웹 애플리케이션의 요청별 컨텍스트 관리
- 스레드별 독립적인 리소스 사용이 필요한 경우

### 주의사항

1. **메모리 사용량**: 스레드 수만큼 인스턴스가 생성되어 메모리 사용량이 증가할 수 있다.
2. **스레드 풀 환경**: 스레드가 재사용되는 환경에서는 예상과 다르게 동작할 수 있다.
3. **정리 시점**: 스레드가 종료되지 않으면 인스턴스가 메모리에 계속 남을 수 있다.

## Ambient Context Pattern

Ambient Context 패턴은 스택 기반의 컨텍스트 관리를 통해 현재 활성화된 인스턴스에 접근할 수 있게 해주는 고급 패턴이다.

### 구현 예제

```csharp
public class BuildingContext : IDisposable
{
    public int WallHeight;
    
    public static BuildingContext Current => stack.Peek();
    
    private static Stack<BuildingContext> stack = new Stack<BuildingContext>();
    
    static BuildingContext()
    {
        stack.Push(new BuildingContext(0));
    }
    
    public BuildingContext(int wallHeight)
    {
        WallHeight = wallHeight;
        stack.Push(this);
    }
    
    public void Dispose()
    {
        if (stack.Count > 1)
            stack.Pop();
    }
}
```

### 사용 예제

```csharp
public static void Main()
{
    var house = new Building();
    
    using (new BuildingContext(3000))
    {
        house.Walls.Add(new Wall(new Point(0, 0), new Point(5000, 0)));
        house.Walls.Add(new Wall(new Point(0, 0), new Point(0, 4000)));
    }
    
    using (new BuildingContext(3500))
    {
        house.Walls.Add(new Wall(new Point(0, 0), new Point(6000, 0)));
        house.Walls.Add(new Wall(new Point(0, 0), new Point(0, 4000)));
    }
}
```

### 장점

1. **스코프 기반 설정**: `using` 문을 통한 자동 컨텍스트 관리
2. **중첩 컨텍스트 지원**: 여러 단계의 컨텍스트 중첩 가능
3. **자동 복원**: 컨텍스트 종료 시 이전 상태로 자동 복원
4. **전역 접근**: `Current` 프로퍼티를 통한 접근
5. **메모리 안전**: `IDisposable` 구현으로 리소스 누수 방지

### 실제 활용 분야

- 로깅 컨텍스트 (요청별 추적 ID나 사용자 정보)
- 보안 컨텍스트 (현재 사용자의 권한이나 인증 정보)
- 트랜잭션 컨텍스트 (데이터베이스 트랜잭션 스코프)
- 설정 컨텍스트 (임시 설정값이나 기능 플래그)
- 문화권 컨텍스트 (언어나 지역 설정)

### 스레드 안전 버전

```csharp
public class ThreadSafeBuildingContext : IDisposable
{
    public int WallHeight;
    
    private static readonly ThreadLocal<Stack<ThreadSafeBuildingContext>> threadStack = 
        new ThreadLocal<Stack<ThreadSafeBuildingContext>>(() =>
        {
            var stack = new Stack<ThreadSafeBuildingContext>();
            stack.Push(new ThreadSafeBuildingContext(0));
            return stack;
        });
    
    public static ThreadSafeBuildingContext Current => threadStack.Value.Peek();
    
    public ThreadSafeBuildingContext(int wallHeight)
    {
        WallHeight = wallHeight;
        threadStack.Value.Push(this);
    }
    
    public void Dispose()
    {
        var stack = threadStack.Value;
        if (stack.Count > 1)
            stack.Pop();
    }
}
```

## 적절한 사용 지침

### 권장 사용 사례
- 로깅 시스템
- 캐시 관리자
- 설정 관리자
- 데이터베이스 연결 풀
- 하드웨어 인터페이스 래퍼

### 피해야 할 경우
- 단순한 전역 변수 대용
- 과도한 전역 상태 생성
- 테스트가 중요한 비즈니스 로직

## 결론

Singleton 패턴은 강력하지만 신중하게 사용해야 하는 패턴이다. 다음 사항들을 고려하는 것이 중요하다:

1. **테스트 가능성**: 의존성 주입을 통한 테스트 가능한 코드 작성
2. **Thread Safety**: `Lazy<T>`나 적절한 동기화 메커니즘 사용
3. **IoC 컨테이너**: 직접 구현보다는 컨테이너를 통한 생명주기 관리
4. **Interface 분리**: 구현체가 아닌 인터페이스에 의존하도록 설계

각 Singleton 변형은 서로 다른 용도와 특성을 가진다:

- **기본 Singleton**: 전역적으로 단일 인스턴스가 필요한 경우
- **Per-Thread Singleton**: 스레드별 독립적인 인스턴스가 필요한 경우  
- **Ambient Context**: 스코프 기반의 컨텍스트 관리가 필요한 경우

올바르게 사용된 Singleton은 시스템의 성능과 메모리 효율성을 향상시킬 수 있으나, 잘못 사용하면 테스트가 불가능하고 결합도가 높은 코드를 만들 수 있다. 특히 Ambient Context 패턴은 복잡한 비즈니스 로직에서 컨텍스트 기반의 설정 관리가 필요할 때 유용한 고급 패턴이다.