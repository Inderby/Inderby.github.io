---
title: [Reactive Programming 이론]
author: excelsiorKim
date: 2024-09-17 14:30:03 +0900
categories: [back-end, web]
tags: [back-end, web, Reactive-Programming]
keywords: [back-end, web, Reactive-Programming]
description: Reactive Programming in java
toc: true
toc_sticky: true
---

## 동기,비동기 / blocking, non-blocking
- 동기적이다 : 어떤 작업을 수행함에 있어서 순차적으로 처리한다는 것이다.(즉, 순서가 명확하다)
- 비동기적이다 : 여러 작업이 동시에 수행될 수 있다는 것이다.(즉, 순서가 명확하지 않다)

### 1. 동기(Sync) + 블로킹(Blocking):
 - 가장 전통적인 방식이다.
 - 작업이 완료될 때까지 프로그램 실행이 멈춘다.
 - 한 번에 하나의 작업만 처리한다.
 - 예: 파일을 읽을 때 파일 읽기가 완료될 때까지 다른 작업을 수행하지 않는다.

### 2. 비동기(Async) + 블로킹(Blocking):
 - 거의 사용되지 않는 조합이다.
 - 비동기적으로 작업을 시작하지만, 결과를 기다리는 동안 블로킹 상태가 된다.
 - 비동기의 이점을 살리지 못하는 방식이다.
 - 예: 비동기 API를 호출하고 즉시 결과를 기다리면서 블로킹된다.

### 3. 동기(Sync) + 논블로킹(Non-blocking):
 - 작업 완료 여부를 주기적으로 확인한다.
 - 작업이 완료되지 않았다면 다른 작업을 수행할 수 있다.
 - 폴링(polling) 방식으로 구현된다.
 - 예: 파일 읽기를 시작하고, 주기적으로 완료 여부를 확인하며, 그 사이에 다른 작업을 수행한다.

### 4. 비동기(Async) + 논블로킹(Non-blocking):
 - 현대적인 고성능 애플리케이션에서 많이 사용되는 방식이다.
 - 작업을 시작하고 즉시 제어권을 반환한다.
 - 작업이 완료되면 콜백, 프로미스, 또는 이벤트를 통해 알림을 받는다.
 - 동시에 여러 작업을 효율적으로 처리할 수 있다.
 - 예: Node.js의 비동기 I/O 작업, JavaScript의 fetch API 등이 이 방식을 사용한다.

## Communication Patterns

![communication-pattern](/assets/img/2024-09-17-reactive-programming-1/communication-pattern.png)

### Request + Response:
- 가장 일반적이고 전통적인 통신 방식이다.
- 클라이언트가 서버에 요청을 보내고, 서버는 처리 후 단일 응답을 반환한다.
- 간단하고 직관적이며, RESTful API에서 주로 사용된다.
- 적합한 사용 사례: 작은 데이터 조회, CRUD 작업 등
- 예: HTTP GET 요청으로 사용자 정보 조회

### Request + Streaming Response:
- 클라이언트가 단일 요청을 보내고, 서버는 데이터를 여러 부분으로 나누어 스트리밍 방식으로 응답한다.
- 대량의 데이터를 전송할 때 유용하며, 클라이언트는 전체 데이터를 기다리지 않고 부분적으로 처리할 수 있다.
- 적합한 사용 사례: 대용량 파일 다운로드, 실시간 로그 스트리밍 등
- 예: 대용량 비디오 파일 스트리밍

### Streaming Request + Response:
- 클라이언트가 데이터를 여러 부분으로 나누어 스트리밍 방식으로 요청을 보내고, 서버는 모든 데이터를 받은 후 단일 응답을 반환한다.
- 대용량 데이터를 서버로 전송할 때 유용하다.
- 적합한 사용 사례: 대용량 파일 업로드, 점진적 데이터 처리 등
- 예: 대용량 로그 파일 업로드 및 분석 결과 반환

### Streaming Request + Streaming Response:
- 클라이언트와 서버 모두 데이터를 스트리밍 방식으로 주고받는다.
- 실시간 양방향 통신이 필요한 경우에 사용된다.
- 복잡한 구현이 필요하지만, 매우 유연하고 효율적인 통신이 가능한다.
- 적합한 사용 사례: 실시간 채팅, 화상 통화, IoT 데이터 스트리밍 등
- 예: gRPC를 사용한 양방향 스트리밍

## Reactive Streams Specification
- Reactive Streams Specification은 비동기 스트림 처리를 위한 표준을 정의한 규격이다.

### 목적
- 비동기적이고 비차단(non-blocking) 방식의 백프레셔(back-pressure)를 갖춘 스트림 처리 표준을 제공한다.
- 다양한 구현체 간의 상호운용성을 보장한다.

### 주요 구성요소:
- Publisher: 데이터 항목의 시퀀스를 생성한다.
- Subscriber: Publisher로부터 데이터를 수신하고 처리한다.
- Subscription: Publisher와 Subscriber 간의 연결을 나타낸다.
- Processor: Publisher와 Subscriber의 역할을 모두 수행하는 중간 처리 단계이다.

### 핵심 인터페이스:

```java
public interface Publisher<T> {
    void subscribe(Subscriber<? super T> subscriber);
}

public interface Subscriber<T> {
    void onSubscribe(Subscription s);
    void onNext(T t);
    void onError(Throwable t);
    void onComplete();
}

public interface Subscription {
    void request(long n);
    void cancel();
}

public interface Processor<T, R> extends Subscriber<T>, Publisher<R> {
}

```
### 주요 규칙:
- Publisher는 Subscriber에게 요청된 것보다 더 많은 onNext 신호를 보내서는 안 된다.
- Publisher는 순차적으로 신호를 보내야 하며, 병렬로 보낼 수 없다.
- Publisher는 Subscription이 취소되면 신호 전송을 중단해야 한다.
- Subscriber는 Subscription.request(long n)를 통해 처리할 수 있는 항목 수를 제어한다.
- Subscriber는 Subscription.cancel()을 호출하여 구독을 취소할 수 있다.

### 백프레셔(Back-pressure):
- Subscriber가 처리할 수 있는 속도로 데이터를 수신할 수 있게 해주는 메커니즘이다.
- Subscription.request(long n) 메서드를 통해 구현된다.
### 이점:
- 비동기 스트림 처리의 표준화
- 시스템 부하 조절 및 리소스 관리 개선
- 다양한 라이브러리와 프레임워크 간의 상호운용성

### 구현체 및 사용 사례:
- RxJava, Project Reactor, Akka Streams 등이 이 명세를 구현하고 있다.
- 대규모 데이터 처리, 실시간 스트리밍, 반응형 마이크로서비스 아키텍처 등에서 사용된다.

### Reactive Programming이란?
- 비동기적이고 논블로킹 방식으로 메시지 스트림을 처리하는 방식의 프로그래밍이다.
- 디자인 패턴 중 옵저버 패턴에 기반한 방식이다.

### Reactive Stream 구현체
- Akka Stream
- rxJava2
- Reactor
  - Spring WebFlux
  - R2DBC
  - Redis
  - Elasticsearch
  - Mongo

## Publisher/Subscriber Communication

1. Subscriber의 연결 요청
2. Publisher가 `onSubscribe` 를 호출한다.
3. 이를 통해 Subscriber는 `Subscription` 이라는 객체를 통해 Publisher와 대화할 수 있다.
4. Subscription을 통해 Subscriber가 Publisher에게 item 요청을 하면 Publisher는 `onNext` 메서드를 활용하여 제공한다.
   - 이때 Publisher는 오직 요청된 횟수 이하의 항목만 제공할 수 있다.
5. Publisher가 더이상 보낼 Item이 없거나, 이미 모든 Item을 발송했을 경우 `onComplete` 메서드를 호출하여 Subscriber에게 알린다. (연결이 끝난 것이다)
6. Publisher가 요청을 처리하는 과정에서 문제가 발생할 경우 `onError` 를 호출한다. 이를 통해 예외 세부 사항을 Subscriber에게 전달한다.

### 용어
- Publisher
  - Source
  - Observable
  - Upstream
  - Producer
- Subscriber
  - Sink
  - Observer
  - Downstream
  - Consumer
- Processor
  - Operator