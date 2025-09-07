---
title: "디자인패턴: Flyweight 패턴"
author: Dante
date: 2025-09-07 19:50:03 +0900
categories: [design-pattern, software-engineering]
tags: [design-pattern, flyweight , clean-code]
keywords: [design-pattern, flyweight, clean-code]
description: "flyweight 패턴의 특징과 각종 구현 방식에 대해 알아보자."
toc: true
toc_sticky: true
---

# Flyweight 패턴: 메모리 효율성을 위한 구조적 디자인 패턴

## 개요

Flyweight 패턴은 GoF(Gang of Four) 디자인 패턴 중 구조적 패턴에 속하는 패턴으로, 대량의 객체를 효율적으로 지원하기 위해 공유를 통해 메모리 사용량을 최소화하는 것을 목적으로 한다. 이 패턴은 유사한 객체들이 공통으로 사용하는 데이터를 공유함으로써 메모리 풋프린트를 크게 줄일 수 있다.

## 패턴의 핵심 개념

Flyweight 패턴의 핵심은 객체의 상태를 두 가지로 분리하는 것이다:

- **내부 상태(Intrinsic State)**: 여러 객체가 공유할 수 있는 상태
- **외부 상태(Extrinsic State)**: 각 객체마다 고유한 상태

이러한 분리를 통해 내부 상태를 공유함으로써 메모리 사용량을 획기적으로 줄일 수 있다.

## 실제 구현 사례 분석

### 사례 1: 사용자 이름 관리 시스템

첨부된 코드에서 `User`와 `User2` 클래스는 Flyweight 패턴 적용 전후의 차이를 명확하게 보여준다.

#### 패턴 적용 전 (User 클래스)

```csharp
public class User
{
    private string fullName;

    public User(string fullName)
    {
        this.fullName = fullName;
    }
}
```

이 구현에서는 각 User 객체가 전체 이름 문자열을 개별적으로 저장한다. 100개의 이름과 100개의 성을 조합하여 10,000개의 User 객체를 생성할 경우, 중복되는 이름과 성이 있음에도 불구하고 각 객체가 독립적인 문자열을 보유하게 된다.

#### 패턴 적용 후 (User2 클래스)

```csharp
public class User2
{
    private static List<string> strings = new List<string>();
    private int[] names;
    
    public User2(string fullName)
    {
        int getOrAdd(string s)
        {
            int idx = strings.IndexOf(s);
            if (idx != -1) return idx;
            else
            {
                strings.Add(s);
                return strings.Count - 1;
            }
        }
        names = fullName.Split(' ').Select(getOrAdd).ToArray();
    }
}
```

`User2` 클래스는 Flyweight 패턴을 적용하여 다음과 같은 최적화를 구현한다:

1. **문자열 풀(String Pool)**: 정적 리스트 `strings`에 모든 고유한 이름 요소를 저장
2. **인덱스 기반 참조**: 실제 문자열 대신 인덱스 배열을 사용하여 이름을 표현
3. **중복 제거**: 동일한 이름이나 성은 한 번만 저장되고 여러 객체가 공유

이 접근법을 통해 100개의 이름과 100개의 성으로 만든 10,000개의 사용자 객체에서 실제로는 200개의 고유한 문자열만 메모리에 저장된다.

### 사례 2: 텍스트 포맷팅 시스템

두 번째 예제는 텍스트 포맷팅에서 Flyweight 패턴을 적용하는 방법을 보여준다.

#### 비효율적인 접근법 (FormattedText 클래스)

```csharp
public class FormattedText
{
    private readonly string plainText;
    private bool[] capitalize;
    
    public FormattedText(string plainText)
    {
        this.plainText = plainText;
        capitalize = new bool[plainText.Length];
    }
}
```

이 구현에서는 텍스트의 각 문자마다 대문자 변환 여부를 저장하는 boolean 배열을 사용한다. 텍스트가 길어질수록 메모리 사용량이 선형적으로 증가하며, 다양한 포맷팅 옵션(굵게, 기울임 등)을 추가하려면 각각에 대한 별도의 배열이 필요하다.

#### Flyweight 패턴 적용 (BetterFormattedText 클래스)

```csharp
public class BetterFormattedText
{
    private string plainText;
    private List<TextRange> formatting = new List<TextRange>();
    
    public TextRange GetRange(int start, int end)
    {
        var range = new TextRange { Start = start, End = end };
        formatting.Add(range);
        return range;
    }
}
```

개선된 구현에서는 포맷팅 정보를 범위(Range) 객체로 관리한다. 이 접근법의 장점은 다음과 같다:

1. **메모리 효율성**: 포맷팅이 적용된 구간만 객체로 저장
2. **확장성**: TextRange 클래스에 다양한 포맷팅 속성을 쉽게 추가 가능
3. **유연성**: 겹치는 범위나 복잡한 포맷팅 규칙을 효과적으로 처리

## 성능 측정과 검증

제공된 테스트 코드는 dotMemoryUnit을 사용하여 실제 메모리 사용량을 측정한다:

```csharp
dotMemory.Check(memory =>
{
    Console.WriteLine(memory.SizeInBytes);
});
```

이러한 정량적 측정을 통해 Flyweight 패턴 적용 전후의 메모리 사용량 차이를 객관적으로 비교할 수 있다. 일반적으로 대규모 데이터셋에서는 수십 배에서 수백 배의 메모리 절약 효과를 관찰할 수 있다.

## 적용 시 고려사항

Flyweight 패턴을 적용할 때는 다음 사항들을 고려해야 한다:

### 장점
- 대량의 유사한 객체를 다룰 때 메모리 사용량을 크게 줄일 수 있음
- 시스템의 확장성 향상
- 캐시 효율성 개선으로 인한 성능 향상 가능

### 단점
- 코드 복잡도 증가
- 공유 상태 관리에 따른 추가적인 연산 비용
- 멀티스레드 환경에서 동기화 고려 필요

## 실무 적용 지침

Flyweight 패턴은 다음과 같은 상황에서 특히 유용하다:

1. **문서 편집기**: 문자 객체의 폰트, 크기 등 속성 공유
2. **게임 개발**: 대량의 동일한 객체(나무, 총알 등) 렌더링
3. **데이터베이스 연결 풀**: 연결 객체의 재사용
4. **캐싱 시스템**: 자주 사용되는 데이터의 공유

## 결론

Flyweight 패턴은 메모리 최적화가 중요한 시스템에서 필수적인 디자인 패턴이다. 제시된 예제들은 이 패턴이 어떻게 실제 코드에서 구현되고, 얼마나 효과적으로 메모리를 절약할 수 있는지를 명확하게 보여준다. 특히 대규모 데이터를 다루는 현대 애플리케이션에서는 이러한 최적화 기법이 시스템의 성능과 확장성을 결정짓는 중요한 요소가 될 수 있다.

적절한 상황에서 Flyweight 패턴을 적용한다면, 메모리 효율성과 시스템 성능을 동시에 개선할 수 있을 것이다. 다만, 패턴 적용에 따른 복잡도 증가와 유지보수성을 함께 고려하여 균형잡힌 설계 결정을 내리는 것이 중요하다.