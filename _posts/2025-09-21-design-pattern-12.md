---
title: "디자인패턴: Chain Of Responsibility 패턴"
author: Dante
date: 2025-09-21 17:50:03 +0900
categories: [design-pattern, software-engineering]
tags: [design-pattern, Chain-of-Responsibility , clean-code]
keywords: [design-pattern, Chain-of-Responsibility, clean-code]
description: "Chain of Responsibility 패턴의 특징과 각종 구현 방식에 대해 알아보자."
toc: true
toc_sticky: true
---

# Chain of Responsibility 패턴: 유연한 요청 처리 체인 구축하기

## 개요

Chain of Responsibility 패턴은 요청을 처리할 수 있는 객체들을 체인으로 연결하여, 요청을 보낸 객체와 이를 처리하는 객체 간의 결합도를 낮추는 행동 디자인 패턴이다. 이 패턴을 통해 여러 객체가 요청을 처리할 기회를 가질 수 있으며, 런타임에 처리 체인을 동적으로 구성할 수 있다.

## 패턴의 핵심 개념

Chain of Responsibility 패턴의 핵심은 요청을 처리할 수 있는 객체들을 연쇄적으로 연결하는 것이다. 각 처리자(Handler)는 요청을 처리할 수 있는지 판단하고, 처리할 수 없다면 체인의 다음 처리자에게 요청을 전달한다. 이러한 구조는 요청의 송신자와 수신자를 분리시켜 시스템의 유연성을 크게 향상시킨다.

## 두 가지 구현 방식

### 1. Method Chain 방식

첫 번째 구현 방식은 전통적인 메서드 체인 방식이다. 이 방식에서는 각 modifier가 다음 modifier에 대한 참조를 직접 보유한다.

```csharp
public class CreatureModifier
{
    protected Creature creature;
    protected CreatureModifier next;

    public void Add(CreatureModifier cm)
    {
        if (next != null) next.Add(cm);
        else next = cm;
    }

    public virtual void Handle() => next?.Handle();
}
```

이 구조의 특징은 다음과 같다:

**장점:**
- 구현이 직관적이고 이해하기 쉽다
- 체인의 흐름을 명확하게 추적할 수 있다
- 각 modifier가 처리 여부를 결정하고 다음으로 전달하는 책임을 명확히 한다

**단점:**
- 체인이 한번 구성되면 변경이 어렵다
- 특정 modifier를 체인 중간에서 제거하기 복잡하다
- modifier들이 직접적으로 연결되어 있어 결합도가 상대적으로 높다

`NoBonusesModifier`와 같은 특수한 modifier를 통해 체인을 중단시킬 수도 있다. 이는 `Handle()` 메서드를 오버라이드하되 `base.Handle()`을 호출하지 않음으로써 구현된다.

### 2. Event-Broker Chain 방식

두 번째 구현은 이벤트 기반의 브로커 체인 방식으로, 더욱 유연하고 느슨하게 결합된 구조를 제공한다.

```csharp
public class Game
{
    public event EventHandler<Query> Queries;

    public void PerformQuery(object sender, Query q)
    {
        Queries?.Invoke(sender, q);
    }
}
```

이 방식의 핵심은 Query 객체를 통한 간접적인 통신이다:

```csharp
public int Attack
{
    get
    {
        var q = new Query(Name, Query.Argument.Attack, attack);
        game.PerformQuery(this, q);
        return q.value;
    }
}
```

**주요 특징:**
- **동적 등록/해제**: `IDisposable` 인터페이스를 활용하여 modifier를 런타임에 쉽게 추가하거나 제거할 수 있다
- **느슨한 결합**: modifier들이 서로를 직접 참조하지 않고 이벤트 시스템을 통해 통신한다
- **쿼리 기반 처리**: 모든 변경사항이 Query 객체를 통해 전달되므로 처리 과정을 표준화할 수 있다
- **using 문 활용**: C#의 using 문을 활용하여 modifier의 수명 주기를 명확하게 관리할 수 있다

```csharp
using (new DoubleAttackModifier(game, goblin))
{
    // modifier가 적용된 상태
    using (new IncreaseDefenseModifier(game, goblin))
    {
        // 두 modifier가 모두 적용된 상태
    }
    // IncreaseDefenseModifier가 자동으로 해제됨
}
// DoubleAttackModifier도 자동으로 해제됨
```

### 성능 고려사항

Event-Broker 방식은 유연성이 뛰어나지만, 이벤트 호출에 따른 오버헤드가 존재한다. 성능이 중요한 시스템에서는 다음을 고려해야 한다:

- 이벤트 핸들러 수가 많아질수록 성능 저하 가능성이 있다
- Query 객체 생성에 따른 메모리 할당 오버헤드가 존재한다
- 빈번한 속성 접근 시 매번 이벤트가 발생하므로 캐싱 전략을 고려해야 한다

## 실제 활용 사례

Chain of Responsibility 패턴은 다음과 같은 상황에서 유용하다:

1. **미들웨어 파이프라인**: 웹 프레임워크에서 요청/응답 처리
2. **이벤트 버블링**: GUI 시스템에서 이벤트 전파
3. **로깅 시스템**: 다양한 로그 레벨과 출력 대상 관리
4. **권한 검증**: 다단계 권한 체크 시스템
5. **게임 시스템**: 버프/디버프와 같은 상태 효과 관리

## 결론

Chain of Responsibility 패턴은 복잡한 처리 로직을 작은 단위로 분해하고, 이를 유연하게 조합할 수 있도록 한다. Method Chain 방식은 단순하고 직관적인 구현을 제공하는 반면, Event-Broker Chain 방식은 더 높은 유연성과 동적 구성 능력을 제공한다. 

프로젝트의 요구사항에 따라 적절한 방식을 선택하되, 유연성이 필요한 경우 Event-Broker 방식을, 단순함과 명확성이 중요한 경우 Method Chain 방식을 고려하는 것이 바람직하다. 무엇보다 중요한 것은 패턴의 적용이 시스템의 복잡도를 불필요하게 증가시키지 않으면서도 유지보수성과 확장성을 향상시키는지 신중히 평가하는 것이다.