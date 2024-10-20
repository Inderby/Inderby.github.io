---
title: [Mono와 Flux]
author: excelsiorKim
date: 2024-09-18 14:30:03 +0900
categories: [back-end, web]
tags: [back-end, web, Reactive-Programming]
keywords: [back-end, web, Reactive-Programming]
description: Reactive Programming in java
toc: true
toc_sticky: true
---

- Reactive Programming에서 Mono와 Flux는 Project Reactor 라이브러리의 핵심 구현체로, 둘 다 Publisher 인터페이스를 구현하다.
- 이들의 주요 특징과 차이점을 설명한다.

# Mono:
- 0 또는 1개의 요소를 발행하는 Publisher이다.
- 주로 단일 결과를 반환하는 비동기 작업에 사용된다.
- 예: HTTP 요청의 응답, 데이터베이스에서 단일 레코드 조회 등

- 주요 특징:
  - onComplete 또는 onError 신호로 종료된다.
  - 단일 값을 처리하는 데 최적화되어 있다.

- Mono의 fromRunnable, fromCallable, fromSupplier는 모두 비동기 작업을 생성하는 데 사용되지만, 각각 다른 특성과 용도를 가지고 있다.

### Mono.fromRunnable():
   - Runnable 인터페이스를 인자로 받는다.
   - 반환 값이 없는 작업을 실행할 때 사용한다.
   - 작업이 완료되면 빈 Mono를 방출한다 (Mono<Void>).
   - 주로 부작용(side-effect)만 있는 작업에 사용된다.

   예시:
   ```java
   Mono<Void> mono = Mono.fromRunnable(() -> System.out.println("작업 실행"));
   ```

### Mono.fromCallable():
   - Callable 인터페이스를 인자로 받는다.
   - 값을 반환하는 작업을 실행할 때 사용한다.
   - 작업이 완료되면 반환된 값을 포함한 Mono를 방출한다.
   - 예외를 던질 수 있는 작업에 적합한다.

   예시:
   ```java
   Mono<String> mono = Mono.fromCallable(() -> "결과");
   ```

### Mono.fromSupplier():
   - Supplier 인터페이스를 인자로 받는다.
   - 값을 반환하는 작업을 실행할 때 사용한다.
   - 작업이 완료되면 반환된 값을 포함한 Mono를 방출한다.
   - Callable과 유사하지만, 예외를 던질 수 없습니다.

   예시:
   ```java
   Mono<String> mono = Mono.fromSupplier(() -> "결과");
   ```

### Mono.fromFuture()
- CompletableFuture를 인자로 받아 Mono로 변환한다.
- 비동기 작업의 결과를 나타내는 Future를 리액티브 스트림으로 변환할 때 사용한다.
- Future가 완료되면 그 결과를 Mono로 방출한다.
- Future가 예외로 완료되면 Mono도 그 예외로 에러를 발생시킨다.

예시:
```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "비동기 작업 결과");
Mono<String> mono = Mono.fromFuture(future);
```

- Mono.fromFuture()의 특징:
  1. 비동기 변환: 이미 진행 중인 비동기 작업(Future)을 리액티브 스트림으로 변환한다.
  2. 지연 구독: Future의 결과를 기다리는 것은 Mono가 구독될 때까지 지연된다.
  3. 취소 처리: Mono 구독이 취소되면, 가능한 경우 기본 Future도 취소된다.
  4. 스레드 관리: Future가 완료되는 스레드에서 Mono의 신호가 발생한다.

- fromFuture와 다른 from* 메서드들의 차이점:
  - fromFuture는 이미 시작된 비동기 작업을 다룹니다.
  - fromCallable, fromSupplier, fromRunnable은 새로운 작업을 시작한다.

- 사용 시기:
  - 기존의 CompletableFuture 기반 코드를 리액티브 스트림으로 마이그레이션할 때
  - 외부 라이브러리나 레거시 코드에서 Future를 반환하는 경우
  - 병렬 처리나 비동기 I/O 작업의 결과를 리액티브 스트림으로 통합할 때

예제를 통한 비교:

```java
// fromFuture
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "Future 결과");
Mono<String> monoFromFuture = Mono.fromFuture(future);

// fromCallable
Mono<String> monoFromCallable = Mono.fromCallable(() -> "Callable 결과");

// fromSupplier
Mono<String> monoFromSupplier = Mono.fromSupplier(() -> "Supplier 결과");

// fromRunnable
Mono<Void> monoFromRunnable = Mono.fromRunnable(() -> System.out.println("Runnable 실행"));
```

이 예제에서 fromFuture는 이미 실행 중인 Future를 사용하는 반면, 다른 메서드들은 Mono가 구독될 때 새로운 작업을 시작한다.

### 주요 차이점
- fromRunnable은 값을 반환하지 않는다.
- fromCallable과 fromSupplier는 값을 반환하지만, fromCallable은 예외를 던질 수 있고 fromSupplier는 그렇지 않는다.
- fromRunnable과 fromCallable은 checked 예외를 던질 수 있지만, fromSupplier는 그렇지 않는다.

### 사용 시기:
- 단순히 작업을 실행하고 완료 여부만 알고 싶다면: fromRunnable
- 값을 반환하고 예외 처리가 필요한 경우: fromCallable
- 값을 반환하지만 예외 처리가 필요 없는 간단한 경우: fromSupplier


## Flux:
- 0에서 N개의 요소를 발행하는 Publisher이다.
- 여러 개의 요소를 처리하는 스트림 작업에 적합하다.
- 예: 데이터베이스의 여러 레코드 조회, 파일 읽기, 실시간 이벤트 스트림 등

- 주요 특징:
  - 여러 onNext 신호를 발생시킬 수 있으며, 최종적으로 onComplete 또는 onError로 종료된다.
  - 무한한 데이터 스트림도 표현할 수 있다.

### Flux.create vs Flux.generate

1. Flux.create:
   - 더 유연한 방식으로 Flux를 생성할 수 있다.
   - 비동기 또는 동기적으로 여러 요소를 방출할 수 있다.
   - FluxSink를 통해 요소를 방출하며, 배압(backpressure)을 처리할 수 있다.
   - 외부 소스(예: 리스너 기반 API)와 통합할 때 유용하다.

예시:

```java
Flux<Integer> flux = Flux.create(emitter -> {
    emitter.next(1);
    emitter.next(2);
    emitter.next(3);
    emitter.complete();
});
```

2. Flux.generate:
   - 동기적이고 상태 기반의 생성 방식이다.
   - 한 번에 하나의 요소만 방출할 수 있다.
   - 상태를 유지하고 다음 상태를 계산하는 방식으로 동작한다.
   - 순차적이고 결정적인 데이터 생성에 적합하다.

예시:
```java
Flux<Integer> flux = Flux.generate(
    () -> 0,
    (state, sink) -> {
        sink.next(state);
        if (state == 10) {
            sink.complete();
        }
        return state + 1;
    });
```

주요 차이점:
1. 방출 방식: create는 여러 요소를 한 번에 방출할 수 있지만, generate는 한 번에 하나의 요소만 방출한다.
2. 상태 관리: generate는 명시적인 상태 관리를 제공하지만, create는 그렇지 않다.
3. 사용 사례: create는 외부 API와의 통합에 적합하고, generate는 순차적이고 예측 가능한 데이터 생성에 유용하다.
4. 배압 처리: create는 FluxSink를 통해 배압을 더 세밀하게 제어할 수 있다.

### take(), takeWhile(), takeUntil()
- Flux의 takeWhile과 takeUntil은 둘 다 Flux에서 요소를 선택적으로 취하는 연산자이다.
- 하지만 그 동작 방식에 중요한 차이가 있다.

1. takeWhile:
   - 주어진 조건이 true인 동안 요소를 계속 취합한다.
   - 조건이 false가 되는 첫 번째 요소에서 중단하고, 해당 요소는 포함하지 않는다.
   - 조건이 false가 되면 즉시 구독을 취소한다.

2. takeUntil:
   - 주어진 조건이 true가 될 때까지 요소를 계속 취한다.
   - 조건이 true가 되는 요소를 포함하고 그 이후에 중단한다.
   - 조건이 true가 되는 요소까지 포함한 후 구독을 취소한다.


- 코드 예제

```java
import reactor.core.publisher.Flux;

public class FluxTakeOperationsExample {
    public static void main(String[] args) {
        Flux<Integer> numbers = Flux.range(1, 10);

        System.out.println("takeWhile example:");
        numbers.takeWhile(n -> n < 5)
               .subscribe(System.out::println);

        System.out.println("\ntakeUntil example:");
        numbers.takeUntil(n -> n >= 5)
               .subscribe(System.out::println);
    }
}

```

- 실행 결과

```
takeWhile example:
1
2
3
4

takeUntil example:
1
2
3
4
5
```

- 차이점:
1. 조건 평가:
      - takeWhile은 조건이 false가 될 때까지 요소를 취한다.
      - takeUntil은 조건이 true가 될 때까지 요소를 취한다.

2. 마지막 요소 처리:
   - takeWhile은 조건을 만족하지 않는 첫 번째 요소를 제외한다.
   - takeUntil은 조건을 만족하는 첫 번째 요소를 포함한다.

3. 사용 시나리오:
   - takeWhile은 특정 조건이 유지되는 동안만 데이터를 처리하고 싶을 때 유용하다.
   - takeUntil은 특정 조건이 만족될 때까지 데이터를 처리하고 싶을 때 유용하다.

### State
- Flux.generate의 state는 Flux 스트림을 생성하는 과정에서 상태를 유지하고 관리하는 중요한 메커니즘이다.

1. state의 목적:
   - 연속적인 값 생성 과정에서 컨텍스트를 유지한다.
   - 이전 생성 단계의 정보를 다음 단계로 전달한다.
   - 복잡한 생성 로직을 구현할 수 있게 해준다.

2. state의 동작 방식:
   - 초기 상태를 정의하고, 각 생성 단계마다 새로운 상태를 반환한다.
   - 상태는 불변(immutable)해야 하며, 각 단계마다 새로운 상태 객체를 생성해야 한다.
   - 생성자는 현재 상태를 받아 다음 값을 생성하고 새로운 상태를 반환한다.

3. state의 구현:
   - Flux.generate는 세 가지 주요 컴포넌트로 구성된다:
   - 초기 상태를 제공하는 Supplier
   - 현재 상태를 기반으로 값을 생성하고 새 상태를 반환하는 BiFunction
   - (선택적) 상태를 정리하는 Consumer

- 이를 코드로 구현한 예제

```java
import reactor.core.publisher.Flux;

public class FluxGenerateStateExample {
    public static void main(String[] args) {
        Flux<String> flux = Flux.generate(
            () -> 0, // 초기 상태
            (state, sink) -> {
                sink.next("Value " + state);
                if (state == 10) {
                    sink.complete();
                }
                return state + 1; // 다음 상태
            },
            (state) -> System.out.println("Final state: " + state) // 상태 정리
        );

        flux.subscribe(System.out::println);
    }
}

```

이 예제에서:
1. 초기 상태는 0입니다.
2. 각 단계에서 현재 상태를 기반으로 "Value X" 형식의 문자열을 생성한다.
3. 상태가 10에 도달하면 스트림을 완료한다.
4. 각 단계 후 상태를 1 증가시킵니다.
5. 생성이 완료되면 최종 상태를 출력한다.

- 주의사항:
  - 상태는 불변이어야 한다. 매 단계마다 새로운 상태 객체를 반환해야 한다.
  - 상태 변경은 반환값을 통해서만 이루어져야 한다. 외부 변수를 수정하는 것은 권장되지 않는다.
  - 복잡한 상태 관리가 필요한 경우, 별도의 상태 객체를 만들어 사용하는 것이 좋다.


### Mono와 Flux의 주요 차합한다

1. 데이터는다:
   - Mon한다최대 1개
   - Flux: 0 ~ N개 (무한대 가능)

2. 사용 사례:
   - Mono: 단일 결과 비동기 작업
   - Flux: 여러 요소를 처리하는 스트림 작업

3. 연산자 지원:
   - 두 타입 모두 다양한 연산자를 제공하지만, Flux는 여러 요소를 다루는 추가적인 연산자들을 제공한다.

4. 성능:
   - Mono는 단일 값 처리에 최적화되어 있어 해당 사용 사례에서 더 효율적일 수 있다.

5. 변환:
   - Flux는 Mono로 쉽게 변환할 수 있다 (예: Flux.next())
   - Mono도 Flux로 변환 가능하다 (예: Mono.flux())
