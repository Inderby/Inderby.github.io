---
title: [Publisher/Subscriber 구현을 통해 원리 이해하기]
author: excelsiorKim
date: 2024-09-18 14:30:03 +0900
categories: [back-end, web]
tags: [back-end, web, Reactive-Programming]
keywords: [back-end, web, Reactive-Programming]
description: Reactive Programming in java
toc: true
toc_sticky: true
---

- 이번엔 간단한 구현을 통해 Publisher와 subscriber의 동작 원리를 이해하려고 한다.

### 필요한 의존성
- build.gradle 파일 설정

```gradle
plugins {
    id 'java'
}

group = 'com.example'
version = '1.0-SNAPSHOT'

sourceCompatibility = '21'
targetCompatibility = '21'

repositories {
    mavenCentral()
}

ext {
    reactorVersion = '2023.0.4'
    logbackVersion = '1.5.3'
    fakerVersion = '1.0.2'
    junitVersion = '5.10.1'
}

dependencies {
    implementation platform("io.projectreactor:reactor-bom:${reactorVersion}")
    implementation 'io.projectreactor:reactor-core'
    implementation 'io.projectreactor.netty:reactor-netty-core'
    implementation 'io.projectreactor.netty:reactor-netty-http'
    implementation "ch.qos.logback:logback-classic:${logbackVersion}"
    implementation "com.github.javafaker:javafaker:${fakerVersion}"

    testImplementation "org.junit.jupiter:junit-jupiter-engine:${junitVersion}"
    testImplementation 'io.projectreactor:reactor-test'
    testImplementation platform('org.junit:junit-bom:5.10.0')
    testImplementation 'org.junit.jupiter:junit-jupiter'
}

test {
    useJUnitPlatform()
}

tasks.withType(JavaCompile) {
    options.encoding = 'UTF-8'
}
```

1. Reactor Core (io.projectreactor:reactor-core)
   - 버전: 2023.0.4 (reactor-bom을 통해 관리됨)
   - 설명: 리액티브 스트림 구현체로, 비동기 및 이벤트 기반 프로그래밍을 위한 핵심 라이브러리이다.

2. Reactor Netty Core (io.projectreactor.netty:reactor-netty-core)
   - 버전: 2023.0.4 (reactor-bom을 통해 관리됨)
   - 설명: Netty를 기반으로 한 리액티브 네트워크 프로그래밍을 위한 코어 컴포넌트이다.

3. Reactor Netty HTTP (io.projectreactor.netty:reactor-netty-http)
   - 버전: 2023.0.4 (reactor-bom을 통해 관리됨)
   - 설명: Netty를 사용한 리액티브 HTTP 클라이언트 및 서버 구현을 제공한다.

4. Logback Classic (ch.qos.logback:logback-classic)
   - 버전: 1.5.3
   - 설명: 로깅을 위한 라이브러리로, SLF4J API의 구현체이다.

5. JavaFaker (com.github.javafaker:javafaker)
   - 버전: 1.0.2
   - 설명: 테스트 데이터 생성을 위한 가짜 데이터 생성 라이브러리이다.

6. JUnit Jupiter Engine (org.junit.jupiter:junit-jupiter-engine)
   - 버전: 5.10.1
   - 설명: JUnit 5 테스팅 프레임워크의 테스트 엔진이다.

7. Reactor Test (io.projectreactor:reactor-test)
   - 버전: 2023.0.4 (reactor-bom을 통해 관리됨)
   - 설명: Reactor 기반 애플리케이션의 단위 테스트를 지원하는 테스트 유틸리티 라이브러리이다.

- logback.xml 파일 설정

```xml
<!-- http://dev.cs.ovgu.de/java/logback/manual/layouts.html -->
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} %-5level [%15.15t] %cyan(%-30.30logger{30}) : %m%n</pattern>
        </encoder>
    </appender>
    <logger name="io.netty.resolver.dns.DnsServerAddressStreamProviders" level="OFF"/>
    <root level="INFO">
        <appender-ref ref="STDOUT" />
    </root>
</configuration>
```

### Subscriber 구현
- 구독하는 개체로써 subscription을 통해 Publisher에게 데이터를 요청한다.

```java

package com.inha.university.sec01.subscriber;

import org.reactivestreams.Subscriber;
import org.reactivestreams.Subscription;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class SubscriberImpl implements Subscriber<String> {
    private static final Logger log = LoggerFactory.getLogger(SubscriberImpl.class);
    private Subscription subscription;

    public Subscription getSubscription() {
        return subscription;
    }

    @Override
    public void onSubscribe(Subscription subscription) {
        this.subscription = subscription;
    }

    @Override
    public void onNext(String email) {
        log.info("received: {}", email);
    }

    @Override
    public void onError(Throwable throwable) {
        log.error("error", throwable);
    }

    @Override
    public void onComplete() {
        log.info("completed");
    }
}

```

### Publisher 구현
- subscriber에 subscription을 주입해주는게 끝이다.(뭔가 휑하다..)

```java
package com.inha.university.sec01.publisher;

import org.reactivestreams.Publisher;
import org.reactivestreams.Subscriber;

public class PublisherImpl implements Publisher<String> {
    @Override
    public void subscribe(Subscriber<? super String> subscriber) {
        var subscription = new SubscriptionImpl(subscriber);
        subscriber.onSubscribe(subscription);
    }
}

```

### Subscription 구현
- request 메서드 호출을 데이터를 가져오며, 메인 로직을 이곳에서 구현한다.


```java
package com.inha.university.sec01.publisher;

import com.github.javafaker.Faker;
import org.reactivestreams.Subscriber;
import org.reactivestreams.Subscription;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class SubscriptionImpl implements Subscription {
    private static final Logger log = LoggerFactory.getLogger(SubscriptionImpl.class);
    private static final int MAX_ITEMS = 10;
    private final Subscriber<String> subscriber;
    private final Faker faker;
    private boolean isCancelled;
    private int count = 0;
    public SubscriptionImpl(Subscriber subscriber) {
        this.subscriber = subscriber;
        this.faker = Faker.instance();
    }

    @Override
    public void request(long requested) {
        if(isCancelled) return;

        log.info("subscriber has requested {} items", requested);

        if(requested > MAX_ITEMS){
            this.subscriber.onError(new RuntimeException("validation failed"));
            this.isCancelled = true;
            return;
        }


        for (int i = 0; i < requested && count < MAX_ITEMS; i++) {
            count++;
            this.subscriber.onNext(this.faker.internet().emailAddress());
        }

        if(count == MAX_ITEMS) {
            log.info("no more data to produce");

            this.subscriber.onComplete();
            this.isCancelled = true;
        }

    }

    @Override
    public void cancel() {
        log.info("subscriber has cancelled");
        this.isCancelled = true;
    }
}

```

### Publisher, Subscriber, Subscription의 관계
- **Publisher:**
  - 데이터 스트림의 소스이다.
  - 데이터 항목을 생성하고 발행한다.


- **Subscriber:**
  - Publisher가 발행한 데이터를 소비한다.
  - Publisher로부터 데이터를 받아 처리한다.


- **Subscription:**
  - Publisher와 Subscriber 사이의 연결을 나타낸다.
  - 데이터 흐름을 제어한다 (예: 백프레셔 메커니즘).

- 관계
  - Publisher는 Subscriber에게 데이터를 제공한다.
  - Subscriber는 Publisher에 구독을 요청한다.
  - Publisher는 구독 요청을 받으면 Subscription을 생성한다.
  - Subscription은 Publisher와 Subscriber 사이의 중개자 역할을 한다.
  - Subscriber는 Subscription을 통해 데이터 요청 및 구독 취소 등을 수행한다.