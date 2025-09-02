---
title: "디자인패턴: Composite 패턴"
author: Dante
date: 2025-08-31 19:50:03 +0900
categories: [design-pattern, software-engineering]
tags: [design-pattern, composite , clean-code]
keywords: [design-pattern, composite, clean-code]
description: "composite 패턴의 특징과 각종 구현 방식에 대해 알아보자."
toc: true
toc_sticky: true
---

# Composite 패턴

## 개요

Composite 패턴은 구조적 디자인 패턴으로, 객체들을 트리 구조로 구성하여 부분-전체 계층을 표현한다. 이 패턴을 통해 개별 객체와 객체들의 집합을 동일하게 처리할 수 있다.

## 패턴의 목적

Composite 패턴은 트리 형태의 계층 구조로 객체를 구성하고, 단일 객체와 복합 객체에 일관된 인터페이스를 제공하며, 객체 안에 다른 객체들을 포함할 수 있는 재귀적 구조를 지원한다.

## 기본 구현

### Component 클래스

```csharp
public class GraphicObject
{
    public virtual string Name { get; set; } = "Group";
    public string Color;
    
    private Lazy<List<GraphicObject>> children = new Lazy<List<GraphicObject>>();
    public List<GraphicObject> Children => children.Value;

    private void Print(StringBuilder builder, int depth)
    {
        builder.AppendLine(new string('*', depth))
            .Append(string.IsNullOrWhiteSpace(Color) ? string.Empty : $"{Color}")
            .Append(Name);

        foreach (GraphicObject child in Children)
        {
            child.Print(builder, depth + 1);
        }
    }

    public override string ToString()
    {
        var sb = new StringBuilder();
        Print(sb, 0);
        return sb.ToString();
    }
}
```

### Leaf 클래스들

```csharp
public class Circle : GraphicObject
{
    public override string Name => "Circle";
}

public class Square : GraphicObject
{
    public override string Name => "Square";
}
```

### 사용 예시

```csharp
static void Main(string[] args)
{
    var drawing = new GraphicObject{Name = "My Drawing"};
    drawing.Children.Add(new Square{Color = "Red"});
    drawing.Children.Add(new Circle{Color = "Yellow"});
    
    var group = new GraphicObject();
    group.Children.Add(new Circle{Color = "Blue"});
    group.Children.Add(new Square{Color = "Green"});
    drawing.Children.Add(group);
    
    System.Console.WriteLine(drawing);
}
```

## 구성 요소

Composite 패턴은 Component, Leaf, Composite 세 가지 주요 요소로 구성된다.

**Component**는 복합체와 개별 객체 모두에 대한 공통 인터페이스를 정의하며, 자식 객체들을 관리하는 기본 동작을 구현한다.

**Leaf**는 트리의 말단 노드 역할을 하며, Component를 상속받아 개별 객체의 동작을 구현한다.

**Composite**는 자식 컴포넌트들을 저장하고 관리하며, 자식 컴포넌트들에 대한 동작을 재귀적으로 호출한다.

## 핵심 특징

### Lazy Initialization

```csharp
private Lazy<List<GraphicObject>> children = new Lazy<List<GraphicObject>>();
```

메모리 효율성을 위해 자식 컬렉션을 필요할 때만 생성하여 성능을 최적화하고 메모리 사용량을 감소시킨다.

### 재귀적 처리

Print 메서드는 현재 객체를 출력한 후 자식 객체들을 재귀적으로 처리하여 트리 구조를 시각적으로 표현한다.

## IEnumerable을 활용한 고급 구현

### Neural Network 예시

```csharp
public static class ExtensionMethods
{
    public static void ConnectTo(this IEnumerable<Neuron> self, IEnumerable<Neuron> other)
    {
        if (ReferenceEquals(self, other)) return;

        foreach (var from in self)
        {
            foreach (var to in other)
            {
                from.Out.Add(to);
                to.In.Add(from);
            }
        }
    }
}

public class Neuron : IEnumerable<Neuron>
{
    private float value;
    public List<Neuron> In, Out;

    public IEnumerator<Neuron> GetEnumerator()
    {
        yield return this;
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
        return GetEnumerator();
    }
}

public class NeuronLayer : Collection<Neuron>
{
    // Collection<T>는 이미 IEnumerable<T>를 구현
}
```

### 고급 구현의 특징

이 접근법은 단일 객체를 컬렉션처럼 처리하며, 확장 메서드를 통해 통합 연산을 제공하고, 자기 참조를 방지하여 순환 참조로 인한 문제를 해결한다.

```csharp
static void Main(string[] args)
{
    var neuron1 = new Neuron();
    var neuron2 = new Neuron();
    var layer1 = new NeuronLayer();
    var layer2 = new NeuronLayer();

    neuron1.ConnectTo(neuron2);
    neuron1.ConnectTo(layer1);
    layer1.ConnectTo(layer2);
}
```

## 장단점

### 장점

개별 객체와 복합 객체를 일관되게 처리할 수 있으며, 새로운 컴포넌트 타입을 쉽게 추가할 수 있다. 복잡한 트리 구조를 간단하게 표현하고, 객체의 복잡성을 숨겨 클라이언트 코드를 단순화한다.

### 단점

모든 컴포넌트가 동일한 인터페이스를 가져야 하므로 과도한 일반화가 발생할 수 있으며, 런타임에 특정 타입의 컴포넌트만 허용하기 어렵다. 단순한 구조에는 오버엔지니어링이 될 수 있다.

## 적용 사례

Composite 패턴은 UI 컴포넌트의 계층 구조, 파일 시스템의 디렉토리와 파일, 조직도의 부서와 직원 관계, 그래픽 편집기의 도형 그룹화 등에 활용된다.

## 관련 패턴

Decorator 패턴은 단일 객체에 동적으로 기능을 추가하는 반면, Composite는 객체들의 집합을 관리한다. Iterator 패턴은 Composite 구조를 순회할 때, Visitor 패턴은 각 노드에 대해 서로 다른 연산을 수행할 때 함께 사용된다.

## 결론

Composite 패턴은 계층적인 데이터나 중첩된 구조를 다룰 때 개별 객체와 객체 집합을 동일하게 처리할 수 있게 하여 코드의 일관성과 유지보수성을 향상시킨다. IEnumerable을 활용한 고급 구현은 C#의 타입 시스템과 LINQ의 장점을 활용하여 더욱 우아하고 효율적인 패턴 구현을 가능하게 한다.