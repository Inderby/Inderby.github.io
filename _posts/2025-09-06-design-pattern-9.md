---
title: "디자인패턴: Decorator 패턴"
author: Dante
date: 2025-09-06 16:50:03 +0900
categories: [design-pattern, software-engineering]
tags: [design-pattern, decorator , clean-code]
keywords: [design-pattern, decorator, clean-code]
description: "decorator 패턴의 특징과 각종 구현 방식에 대해 알아보자."
toc: true
toc_sticky: true
---

# Decorator 패턴

## 들어가며

객체지향 프로그래밍에서 기존 객체에 새로운 기능을 추가하고자 할 때, 상속을 사용하는 것이 일반적인 접근법이다. 그러나 상속은 정적이고 유연하지 못하며, 다중 기능 조합 시 클래스 폭발(class explosion) 문제를 야기할 수 있다. Decorator 패턴은 이러한 문제를 해결하는 강력한 구조적 디자인 패턴이다.

## Decorator 패턴이란?

Decorator 패턴은 객체에 추가 기능을 동적으로 첨가할 수 있게 해주는 패턴이다. 서브클래스를 만드는 것보다 유연한 대안을 제공하며, 객체를 여러 개의 데코레이터로 감싸서 원하는 기능을 조합할 수 있다.

## 코드 분석: C#으로 구현한 Decorator 패턴

제공된 코드를 통해 Decorator 패턴의 실제 구현을 살펴보고자 한다.

### 1. 기본 구조

먼저 모든 도형의 기본이 되는 `IShape` 인터페이스가 정의되어 있다:

```csharp
public interface IShape
{
    string AsString();
}
```

이 인터페이스를 구현하는 구체적인 도형 클래스들이 존재한다:
* `Circle`: 반지름을 가진 원
* `Square`: 한 변의 길이를 가진 정사각형

### 2. Decorator 계층 구조

코드에서 가장 흥미로운 부분은 다층적인 Decorator 추상 클래스 구조이다:

```csharp
public abstract class ShapeDecorator : IShape
{
    protected internal readonly List<Type> types = new List<Type>();
    protected internal IShape shape;
}
```

기본 `ShapeDecorator`는 감싸고 있는 shape 객체와 적용된 데코레이터 타입들의 리스트를 관리한다.

### 3. 고급 기능: 순환 참조 정책

이 구현의 독특한 점은 데코레이터 순환 참조를 처리하는 정책 시스템이다:

```csharp
public abstract class ShapeDecorator<TSelf, TCyclePolicy> : ShapeDecorator 
    where TCyclePolicy : ShapeDecoratorCyclePolicy, new()
```

세 가지 정책이 구현되어 있다:
* **ThrowOnCyclePolicy**: 동일한 타입의 데코레이터가 중복 적용되면 예외를 발생시킨다
* **CycleAllowedPolicy**: 모든 중복을 허용한다
* **AbsorbCyclePolicy**: 중복 데코레이터를 조용히 무시한다

### 4. 구체적인 Decorator 구현

#### ColoredShape
도형에 색상을 추가하는 데코레이터이다:

```csharp
public class ColoredShape : ShapeDecorator<ColoredShape, ThrowOnCyclePolicy>
{
    private string color;
    
    public string AsString()
    {
        var sb = new StringBuilder($"{shape.AsString()}");
        if (policy.ApplicationAllowed(types[0], types.Skip(1).ToList()))
            sb.Append($"has the color {color}");
        return sb.ToString();
    }
}
```

#### TransparentShape
도형에 투명도를 추가하는 데코레이터이다:

```csharp
public class TransparentShape : IShape
{
    public string AsString() => 
        $"{shape.AsString()} has {transparency * 100.0}% transparency";
}
```

### 5. 실제 사용 예제

```csharp
var square = new Square(1.23f);
// 출력: A square with side 1.23

var redSquare = new ColoredShape(square, "red");
// 출력: A square with side 1.23 has the color red

var redHalfTransparentSquare = new TransparentShape(redSquare, 0.5f);
// 출력: A square with side 1.23 has the color red has 50% transparency
```

## Dependency Injection과 Decorator 패턴

### DI 컨테이너를 활용한 Decorator 패턴 구현

실무에서 Decorator 패턴을 적용할 때, Dependency Injection(DI) 컨테이너를 활용하면 더욱 유연하고 관리하기 쉬운 구조를 만들 수 있다. Autofac과 같은 DI 컨테이너는 데코레이터 등록과 해결을 위한 내장 기능을 제공한다.

### 코드 분석: Autofac을 사용한 Decorator 구현

#### 1. 기본 서비스 인터페이스와 구현

```csharp
public interface IReportingService
{
    void Report();
}

public class ReportingService : IReportingService
{
    public void Report()
    {
        Console.WriteLine("Here is your report");
    }
}
```

`IReportingService`는 보고서 생성 기능을 정의하는 인터페이스이며, `ReportingService`는 이를 구현하는 기본 클래스이다.

#### 2. Decorator 구현

```csharp
public class ReportingServiceWithLogging : IReportingService
{
    private IReportingService decorated;

    public ReportingServiceWithLogging(IReportingService decorated)
    {
        this.decorated = decorated;
    }

    public void Report()
    {
        Console.WriteLine("Commencing log...");
        decorated.Report();
        Console.WriteLine("Ending log...");
    }
}
```

`ReportingServiceWithLogging`은 기존 `IReportingService`를 감싸서 로깅 기능을 추가하는 데코레이터이다. 생성자 주입을 통해 데코레이트할 서비스를 받아들인다.

#### 3. DI 컨테이너 설정

```csharp
static void Main(string[] args)
{
    var b = new ContainerBuilder();
    
    // 기본 서비스를 명명된 서비스로 등록
    b.RegisterType<ReportingService>()
        .Named<IReportingService>("reporting");
    
    // 데코레이터 등록
    b.RegisterDecorator<IReportingService>(
        (context, service) => new ReportingServiceWithLogging(service),
        "reporting");
    
    using (var c = b.Build())
    {
        var r = c.Resolve<IReportingService>();
        r.Report();
    }
}
```

실행 결과:
```
Commencing log...
Here is your report
Ending log...
```

### DI를 활용한 Decorator 패턴의 장점

#### 1. **자동 의존성 해결**
DI 컨테이너가 데코레이터 체인을 자동으로 구성해준다. 개발자는 등록만 하면 되고, 복잡한 객체 생성 로직을 작성할 필요가 없다.

#### 2. **다중 데코레이터 체인**
여러 데코레이터를 순차적으로 적용할 수 있다:

```csharp
b.RegisterType<ReportingService>()
    .Named<IReportingService>("reporting");

b.RegisterDecorator<IReportingService>(
    (context, service) => new ReportingServiceWithLogging(service),
    "reporting");

b.RegisterDecorator<IReportingService>(
    (context, service) => new ReportingServiceWithCaching(service),
    "reporting");

b.RegisterDecorator<IReportingService>(
    (context, service) => new ReportingServiceWithMetrics(service),
    "reporting");
```

#### 3. **조건부 데코레이터 적용**
런타임 조건에 따라 데코레이터를 선택적으로 적용할 수 있다:

```csharp
b.RegisterDecorator<IReportingService>((context, service) =>
{
    var config = context.Resolve<IConfiguration>();
    if (config.LoggingEnabled)
        return new ReportingServiceWithLogging(service);
    return service;
}, "reporting");
```

#### 4. **테스트 용이성**
DI를 사용하면 테스트 시 mock 객체를 쉽게 주입할 수 있어 단위 테스트가 용이하다:

```csharp
[Test]
public void ReportingServiceWithLogging_Should_Log_Before_And_After()
{
    var mockService = new Mock<IReportingService>();
    var decorator = new ReportingServiceWithLogging(mockService.Object);
    
    decorator.Report();
    
    mockService.Verify(x => x.Report(), Times.Once);
}
```

### 실무 활용 시나리오

#### 1. **횡단 관심사(Cross-cutting Concerns) 처리**
로깅, 캐싱, 트랜잭션, 보안 검증 등의 횡단 관심사를 데코레이터로 구현:

```csharp
public class SecureReportingService : IReportingService
{
    private readonly IReportingService decorated;
    private readonly IAuthService authService;

    public SecureReportingService(
        IReportingService decorated, 
        IAuthService authService)
    {
        this.decorated = decorated;
        this.authService = authService;
    }

    public void Report()
    {
        if (!authService.IsAuthorized())
            throw new UnauthorizedException();
        
        decorated.Report();
    }
}
```

#### 2. **성능 모니터링**
메서드 실행 시간을 측정하는 데코레이터:

```csharp
public class PerformanceMonitoringDecorator : IReportingService
{
    private readonly IReportingService decorated;
    private readonly IMetricsCollector metrics;

    public void Report()
    {
        var stopwatch = Stopwatch.StartNew();
        try
        {
            decorated.Report();
        }
        finally
        {
            stopwatch.Stop();
            metrics.RecordExecutionTime("Report", stopwatch.ElapsedMilliseconds);
        }
    }
}
```

#### 3. **재시도 로직**
실패 시 자동 재시도를 구현하는 데코레이터:

```csharp
public class RetryDecorator : IReportingService
{
    private readonly IReportingService decorated;
    private readonly int maxRetries;

    public void Report()
    {
        int attempts = 0;
        while (attempts < maxRetries)
        {
            try
            {
                decorated.Report();
                return;
            }
            catch (Exception ex) when (attempts < maxRetries - 1)
            {
                attempts++;
                Thread.Sleep(1000 * attempts); // 백오프 전략
            }
        }
    }
}
```

### 주의사항

1. **순서의 중요성**: 데코레이터를 등록하는 순서가 실행 순서를 결정한다
2. **성능 오버헤드**: 과도한 데코레이터 체인은 성능에 영향을 줄 수 있다
3. **디버깅 복잡성**: 여러 층의 데코레이터는 디버깅을 어렵게 만들 수 있다
4. **DI 컨테이너 종속성**: 특정 DI 컨테이너의 기능에 의존하게 된다

## Decorator 패턴의 장점

1. **동적 기능 추가**: 런타임에 객체에 새로운 기능을 추가할 수 있다
2. **단일 책임 원칙**: 각 데코레이터는 하나의 기능만 담당한다
3. **개방-폐쇄 원칙**: 기존 코드를 수정하지 않고 새로운 기능을 추가할 수 있다
4. **유연한 조합**: 여러 데코레이터를 조합하여 복잡한 기능을 구현할 수 있다

## 주의사항

1. **복잡성 증가**: 많은 작은 클래스들이 생성되어 코드 이해가 어려워질 수 있다
2. **순서 의존성**: 데코레이터를 적용하는 순서가 결과에 영향을 미칠 수 있다
3. **디버깅 어려움**: 여러 층의 래핑으로 인해 디버깅이 복잡해질 수 있다

## 실무 활용 예시

Decorator 패턴은 다음과 같은 상황에서 유용하다:
* **UI 컴포넌트**: 스크롤바, 테두리, 그림자 등을 동적으로 추가
* **스트림 처리**: BufferedStream, CryptoStream 등 I/O 스트림 래핑
* **미들웨어 체인**: 웹 프레임워크의 요청 처리 파이프라인
* **캐싱**: 기존 서비스에 캐싱 레이어 추가
* **로깅 및 모니터링**: 기존 기능에 로깅이나 성능 측정 추가

## 마무리

Decorator 패턴은 객체의 기능을 유연하게 확장할 수 있는 강력한 도구이다. 제공된 코드에서 볼 수 있듯이, 순환 참조 정책과 같은 고급 기능을 추가하여 더욱 견고한 구현을 만들 수 있다. 상속의 경직성을 피하면서도 기능을 확장해야 할 때, Decorator 패턴은 훌륭한 선택이 될 것이다.

특히 이 구현에서 주목할 점은 제네릭과 정책 패턴을 활용하여 타입 안정성과 유연성을 동시에 달성했다는 것이다. 이는 단순한 Decorator 패턴을 넘어서 실무에서 마주할 수 있는 복잡한 요구사항들을 우아하게 해결하는 방법을 보여준다.

또한 DI 컨테이너를 활용한 Decorator 패턴 구현은 엔터프라이즈 애플리케이션에서 횡단 관심사를 처리하는 표준적인 방법이 되었다. Autofac과 같은 현대적인 DI 컨테이너는 데코레이터 패턴을 일급 시민으로 지원하여, 복잡한 데코레이터 체인도 선언적으로 구성할 수 있게 해준다. 이를 통해 코드의 유지보수성과 테스트 용이성을 크게 향상시킬 수 있다.