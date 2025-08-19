---
title: [cold publisher vs hot publisher]
author: excelsiorKim
date: 2024-09-26 14:30:03 +0900
categories: [back-end, web]
tags: [back-end, web, Reactive-Programming]
keywords: [back-end, web, Reactive-Programming]
description: Reactive Programming in java
toc: true
toc_sticky: true
---

### Cold Publisher

1. Cold Publisher:
   - 구독자(subscriber)가 구독할 때마다 데이터 스트림을 처음부터 새로 생성한다.
   - 각 구독자는 전체 데이터 시퀀스를 받는다.
   - 구독자가 없으면 데이터를 생성하지 않는다(lazy).
   - 예: 파일 읽기, 데이터베이스 쿼리 실행

예시 코드:
```java
Flux<Integer> coldPublisher = Flux.range(1, 5);
coldPublisher.subscribe(System.out::println); // 1, 2, 3, 4, 5 출력
coldPublisher.subscribe(System.out::println); // 다시 1, 2, 3, 4, 5 출력
```

2. Hot Publisher:
   - 구독 여부와 관계없이 데이터를 생성한다.
   - 구독 시점 이후의 데이터만 받는다.
   - 여러 구독자가 동시에 같은 데이터 스트림을 공유한다.
   - 예: 마우스 이벤트, 실시간 주식 가격 업데이트
   - `refCount(int)` 를 사용하면 데이터 전송을 위한 최소 구독자 수를 지정할 수 있다.
   - `cache()` 함수를 지정하여 새로운 구독자에게 이전 데이터를 전송할 수 있다.

예시 코드:
```java
ConnectableFlux<Long> hotPublisher = Flux.interval(Duration.ofSeconds(1)).publish();
hotPublisher.connect();

Thread.sleep(2000); // 2초 대기

hotPublisher.subscribe(data -> System.out.println("Subscriber 1: " + data));
Thread.sleep(2000); // 2초 대기

hotPublisher.subscribe(data -> System.out.println("Subscriber 2: " + data));
```

이 예시에서 Subscriber 1은 2, 3, 4...를 받고, Subscriber 2는 4, 5, 6...을 받게 됩니다.

주요 차이점을 요약하면:
1. 데이터 생성 시점: Cold는 구독 시, Hot은 구독과 무관하게 생성
2. 데이터 공유: Cold는 각 구독자에게 새 스트림, Hot은 여러 구독자가 공유
3. 리소스 사용: Cold는 구독 시에만 리소스 사용, Hot은 지속적으로 리소스 사용 가능