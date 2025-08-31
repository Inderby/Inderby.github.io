---
title: "디자인패턴: Adapter 패턴"
author: Dante
date: 2025-08-229 18:50:03 +0900
categories: [design-pattern, software-engineering]
tags: [design-pattern, adapter , clean-code, generic-factory]
keywords: [design-pattern, adapter, clean-code, generic-factory]
description: "adapter 패턴의 특징과 각종 구현 방식에 대해 알아보자."
toc: true
toc_sticky: true
---

# C# Adapter Pattern 종합 가이드

## 개요

Adapter Pattern은 호환되지 않는 인터페이스를 가진 클래스들이 함께 작동할 수 있도록 하는 구조적 디자인 패턴이다. 기존 클래스의 인터페이스를 클라이언트가 기대하는 인터페이스로 변환하여 상호 운용성을 제공한다.

## 기본 구현

### 문제 상황

벡터 기반 도형(선으로 구성)을 점 기반 렌더링 시스템에서 그려야 하는 상황을 고려한다. 벡터 객체는 Line으로 구성되나, 렌더링 시스템은 Point만 처리할 수 있다.

### 기본 클래스 구조

```csharp
public class Point
{
    public int X, Y;
    public Point(int x, int y) => (X, Y) = (x, y);
}

public class Line
{
    public Point start, end;
    public Line(Point start, Point end) => (this.start, this.end) = (start, end);
}

public class VectorObject : Collection<Line> { }

public class VectorRectangle : VectorObject
{
    public VectorRectangle(int x, int y, int width, int height)
    {
        Add(new Line(new Point(x, y), new Point(x + width, y)));
        Add(new Line(new Point(x + width, y), new Point(x + width, y + height)));
        Add(new Line(new Point(x, y), new Point(x, y + height)));
        Add(new Line(new Point(x, y + height), new Point(x + width, y + height)));
    }
}
```

### 어댑터 구현

```csharp
public class LineToPointAdapter : Collection<Point>
{
    public LineToPointAdapter(Line line)
    {
        int left = Math.Min(line.start.X, line.end.X);
        int right = Math.Max(line.start.X, line.end.X);
        int top = Math.Min(line.start.Y, line.end.Y);
        int bottom = Math.Max(line.start.Y, line.end.Y);

        if (right - left == 0) // 수직선
        {
            for (int y = top; y <= bottom; ++y)
                Add(new Point(left, y));
        }
        else if (bottom - top == 0) // 수평선
        {
            for (int x = left; x <= right; ++x)
                Add(new Point(x, top));
        }
    }
}
```

## 성능 최적화: 캐싱 구현

### 문제점

동일한 Line 객체에 대해 반복적인 변환 작업 시 매번 새로운 계산이 수행되어 성능 저하가 발생한다.

### 캐싱 기반 어댑터

```csharp
public class LineToPointAdapter : IEnumerable<Point>
{
    private static Dictionary<int, List<Point>> cache = new Dictionary<int, List<Point>>();

    public LineToPointAdapter(Line line)
    {
        var hash = line.GetHashCode();
        if (cache.ContainsKey(hash)) return;
        
        var points = new List<Point>();
        // 변환 로직 실행 후 캐시에 저장
        cache.Add(hash, points);
    }

    public IEnumerator<Point> GetEnumerator() =>
        cache.Values.SelectMany(x => x).GetEnumerator();
}
```

## Generic Adapter Pattern

### 개념

제네릭을 활용하여 타입 안전성과 재사용성을 극대화한 어댑터 패턴의 고급 구현이다. 컴파일 타임에 타입이 결정되어 런타임 오버헤드를 최소화한다.

### 차원 정보 타입

```csharp
public interface IInteger
{
    int Value { get; }
}

public static class Dimensions
{
    public class Two : IInteger { public int Value => 2; }
    public class Three : IInteger { public int Value => 3; }
}
```

### 제네릭 벡터 구현

```csharp
public class Vector<TSelf, T, D>
    where D : IInteger, new()
    where TSelf : Vector<TSelf, T, D>, new()
{
    protected T[] data;

    public Vector() => data = new T[new D().Value];
    
    public Vector(params T[] values)
    {
        var requiredSize = new D().Value;
        data = new T[requiredSize];
        for (int i = 0; i < Math.Min(requiredSize, values.Length); ++i)
            data[i] = values[i];
    }

    public T this[int index]
    {
        get => data[index];
        set => data[index] = value;
    }
}

public class VectorOfInt<TSelf, D> : Vector<VectorOfInt<TSelf, D>, int, D> 
    where D : IInteger, new() 
    where TSelf : VectorOfInt<TSelf, D>, new()
{
    public VectorOfInt() { }
    public VectorOfInt(params int[] values) : base(values) { }

    public static VectorOfInt<TSelf, D> operator +(VectorOfInt<TSelf, D> lhs, VectorOfInt<TSelf, D> rhs)
    {
        var result = new TSelf();
        var dim = new D().Value;
        for (int i = 0; i < dim; i++)
            result[i] = lhs[i] + rhs[i];
        return result;
    }
}

public class Vector2i : VectorOfInt<Vector2i, Dimensions.Two>
{
    public Vector2i() { }
    public Vector2i(params int[] values) : base(values) { }
}
```

## Dependency Injection과 Adapter Pattern

### 개념

IoC 컨테이너를 활용하여 어댑터의 생성과 관리를 자동화하고, 런타임에 동적으로 어댑터를 선택할 수 있는 구현 방법이다.

### 기본 구조

```csharp
public interface ICommand
{
    void Execute();
}

class SaveCommand : ICommand
{
    public void Execute() => Console.WriteLine("Saving a file");
}

class OpenCommand : ICommand
{
    public void Execute() => Console.WriteLine("Opening a file");
}

public class Button
{
    private readonly ICommand _command;
    private readonly string name;

    public Button(ICommand command, string name)
    {
        _command = command ?? throw new ArgumentNullException(nameof(command));
        this.name = name;
    }

    public void Click() => _command.Execute();
    public void PrintMe() => Console.WriteLine($"I am a {name}");
}

public class Editor
{
    public IEnumerable<Button> Buttons { get; }

    public Editor(IEnumerable<Button> buttons)
    {
        Buttons = buttons;
    }

    public void ClickAll()
    {
        foreach (var button in Buttons)
            button.Click();
    }
}
```

### 컨테이너 설정

```csharp
static void Main(string[] args)
{
    var builder = new ContainerBuilder();
    
    // 메타데이터와 함께 커맨드 등록
    builder.RegisterType<SaveCommand>().As<ICommand>()
        .WithMetadata("Name", "Save");
    builder.RegisterType<OpenCommand>().As<ICommand>()
        .WithMetadata("Name", "Open");
    
    // 어댑터 자동 등록
    builder.RegisterAdapter<Meta<ICommand>, Button>(
        cmd => new Button(cmd.Value, (string)cmd.Metadata["Name"]));
    
    builder.RegisterType<Editor>();
    
    using (var container = builder.Build())
    {
        var editor = container.Resolve<Editor>();
        foreach (var button in editor.Buttons)
        {
            button.PrintMe();
            button.Click();
        }
    }
}
```

## 패턴별 특성 비교

### 기본 Adapter Pattern
- **장점**: 구현이 단순하며 이해하기 쉽다
- **단점**: 타입별로 별도 구현이 필요하다

### 캐싱 기반 Adapter
- **장점**: 반복 연산 시 성능이 크게 향상된다
- **단점**: 메모리 사용량이 증가하며 캐시 관리가 필요하다

### Generic Adapter Pattern
- **장점**: 타입 안전성과 성능 최적화를 동시에 달성한다
- **단점**: 구현 복잡도가 높으며 학습 곡선이 가파르다

### DI 기반 Adapter
- **장점**: 자동화된 관리와 높은 확장성을 제공한다
- **단점**: IoC 컨테이너 의존성과 런타임 오버헤드가 존재한다

## 적용 지침

### 사용 시기
- 기존 클래스를 수정할 수 없는 상황
- 서로 다른 인터페이스를 통합해야 하는 경우
- 레거시 시스템과 신규 시스템의 연동

### 구현 선택 기준
- **기본 구현**: 단순한 일회성 변환이 필요한 경우
- **캐싱 구현**: 반복적인 변환 작업이 빈번한 경우
- **제네릭 구현**: 다양한 타입에 대한 일관된 처리가 필요한 경우
- **DI 구현**: 복잡한 객체 그래프와 동적 구성이 필요한 경우

## 결론

Adapter Pattern은 호환되지 않는 인터페이스 문제를 해결하는 핵심적인 디자인 패턴이다. 기본적인 구현부터 고급 기법까지 다양한 접근 방식이 존재하며, 각각은 특정 상황에서 최적의 성능과 유지보수성을 제공한다. 프로젝트의 요구사항과 제약 조건을 고려하여 적절한 구현 방식을 선택하는 것이 중요하다.