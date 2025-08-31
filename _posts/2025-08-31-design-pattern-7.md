---
title: "디자인패턴: Bridge 패턴"
author: Dante
date: 2025-08-31 19:50:03 +0900
categories: [design-pattern, software-engineering]
tags: [design-pattern, bridge , clean-code]
keywords: [design-pattern, bridge, clean-code]
description: "bridge 패턴의 특징과 각종 구현 방식에 대해 알아보자."
toc: true
toc_sticky: true
---

# Bridge Pattern (브릿지 패턴)

## 개요

Bridge 패턴은 구조적 디자인 패턴(Structural Design Pattern) 중 하나로, 추상화와 구현을 분리하여 각각 독립적으로 변화할 수 있도록 하는 패턴이다. 이 패턴은 상속 대신 객체 합성을 사용하여 기능의 확장성을 제공한다.

## 문제 상황

대형 클래스를 두 개의 별개 계층구조로 나누어야 하는 경우가 있다. 예를 들어 다음과 같다:

- **추상화(Abstraction)**: Shape (원, 사각형, 삼각형 등)
- **구현(Implementation)**: Renderer (벡터 렌더링, 래스터 렌더링 등)

상속만을 사용할 경우 `VectorCircle`, `RasterCircle`, `VectorRectangle`, `RasterRectangle` 등 기하급수적으로 클래스가 증가하게 된다.

## Bridge 패턴의 구조

```
Shape (추상화)
├── Circle
├── Rectangle
└── Triangle

IRenderer (구현 인터페이스)
├── VectorRenderer
└── RasterRenderer
```

## 코드 예시

### 1. 구현 인터페이스 정의

```csharp
public interface IRenderer
{
    void RenderCircle(float radius);
}
```

### 2. 구체적인 구현 클래스들

```csharp
// 벡터 렌더링 구현
public class VectorRenderer : IRenderer
{
    public void RenderCircle(float radius)
    {
        WriteLine($"Drawing a circle of radius {radius}");
    }
}

// 래스터 렌더링 구현
public class RasterRenderer : IRenderer
{
    public void RenderCircle(float radius)
    {
        WriteLine($"Drawing pixels for circle with radius {radius}");
    }
}
```

### 3. 추상화 클래스

```csharp
public abstract class Shape
{
    protected IRenderer renderer;
    
    protected Shape(IRenderer renderer)
    {
        if (renderer == null)
        {
            throw new ArgumentNullException(nameof(renderer));
        }
        
        this.renderer = renderer;
    }
    
    public abstract void Draw();
    public abstract void Resize(float factor);
}
```

### 4. 구체적인 추상화 클래스

```csharp
public class Circle : Shape
{
    private float radius;
    
    public Circle(IRenderer renderer, float radius) : base(renderer)
    {
        this.radius = radius;
    }
    
    public override void Draw()
    {
        renderer.RenderCircle(radius);  // 브릿지를 통한 위임
    }
    
    public override void Resize(float factor)
    {
        radius *= factor;
    }
}
```

### 5. 의존성 주입을 활용한 사용 예시

```csharp
class Demo
{
    public static void Main(string[] args)
    {
        var cb = new ContainerBuilder();
        cb.RegisterType<VectorRenderer>().As<IRenderer>()
            .SingleInstance();
        cb.Register((c, p) => new Circle(c.Resolve<IRenderer>(), p.Positional<float>(0)));
        
        using (var c = cb.Build())
        {
            var circle = c.Resolve<Circle>(
                new PositionalParameter(0, 5.0f));
            circle.Draw();      // Drawing a circle of radius 5
            circle.Resize(10.0f);
            circle.Draw();      // Drawing a circle of radius 50
        }
    }
}
```

## Bridge 패턴의 핵심 특징

### 1. 분리된 계층구조

- **추상화**: `Shape` → `Circle`, `Rectangle` 등
- **구현**: `IRenderer` → `VectorRenderer`, `RasterRenderer` 등

### 2. 위임을 통한 연결

- `Circle` 클래스는 실제 렌더링을 `IRenderer`에게 위임한다
- 상속 대신 합성(Composition)을 사용한다

### 3. 런타임에서의 구현 변경

```csharp
var circle = new Circle(new VectorRenderer(), 5.0f);
// 나중에 다른 렌더러로 변경 가능하다
```

## 장점

Bridge 패턴의 주요 장점은 다음과 같다:

1. **확장성**: 새로운 모양이나 렌더러를 용이하게 추가할 수 있다
2. **유연성**: 런타임에 구현체 변경이 가능하다  
3. **단일 책임 원칙**: 각 클래스가 하나의 책임만을 갖는다
4. **개방-폐쇄 원칙**: 기존 코드의 수정 없이 확장이 가능하다

## 실제 활용 예시

Bridge 패턴은 다음과 같은 영역에서 활용된다:

- **GUI 프레임워크**: 추상적인 윈도우와 플랫폼별 구현을 분리한다
- **데이터베이스 드라이버**: 추상적인 DB 연결과 특정 DB 구현을 분리한다  
- **그래픽 라이브러리**: 도형과 렌더링 엔진을 분리한다
- **미디어 플레이어**: 미디어 타입과 코덱 구현을 분리한다

## 다른 패턴과의 차이점

### vs Adapter Pattern

- **Bridge**: 설계 단계에서 추상화와 구현을 분리한다
- **Adapter**: 기존에 호환되지 않는 인터페이스를 연결한다

### vs Strategy Pattern  

- **Bridge**: 객체의 전체 구조에 영향을 미친다
- **Strategy**: 특정 알고리즘만을 교체한다

## 결론

Bridge 패턴은 복잡한 시스템에서 추상화와 구현을 효과적으로 분리하여 코드의 유지보수성과 확장성을 크게 향상시킨다. 특히 다양한 플랫폼이나 구현 방식을 지원해야 하는 시스템에서 매우 유용한 패턴이다.