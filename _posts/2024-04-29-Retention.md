---
title: [Retention 이란?]
author: excelsiorKim
date: 2024-04-29 12:30:03 +0900
categories: [Spring, Programming]
tags: [Spring, gradle, Retention, annotation]
keywords: [Spring, gradle, Retention, annotation]
description: Retention 어노테이션의 역할과 다양한 옵션에 대해 알아본다.
toc: true
toc_sticky: true
---

# @Retention이란?

- Spring에서 `@Retention` 어노테이션은 다른 어노테이션 유지 정책(retention policy)을 지정하는데 사용되는 메타 어노테이션(meta-annotation)이다.
- 유지 정책은 어느테이션이 적용되는 시점과 범위을 지정한다.

## Retention의 다양한 속성

- `@Retention` 어노테이션은 `java.lang.annotation.Retention`에 정의되어 있으며, `RetentionPolicy` 열거형(enum)을 값으로 받는다.
- `RetentionPolicy`는 다름과 같은 세가지 값을 가질 수 있다.
  - `RetentionPolicy.SOURCE` : 어노텡니션이 컴파일 시점까지만 유지되고, 컴파일된 클래스 파일에는 포함되지 않는다. 이는 주로 컴파일러에게 정보를 제공하기 위한 용도로 사용된다.
  - `RetentionPolicy.CLASS` : 어노테이션이 컴파일 시점까지 유지되고, 컴파일된 클래스 파일에 포함되지만, 런타임에는 사용할 수 없다. 이는 기본 값이다.
  - `RetentionPolicy.RUNTIME` : 어노테이션이 컴파일 시점뿐만 아니라, 런타임에도 유지되며, 리플렉션(reflection)을 통해 어노테이션 정보에 접근할 수 있다. 이는 Spring에서 가장 일반적으로 사용되는 유지 정책이다.

### Spring의 대부분의 어노테이션이 Runtime Policy인 이유

- Spring이 런타임에 리플렉션을 사용하여 어노테이션 정보를 읽고, 해석하기 때문이다.
- 예를 들어, `@Controller`, `@Service`, `@Repository` 등의 어노테이션은 모두 런타임에 유지되어, Spring 컨테이너가 해당 클래스를 스캔하고 빈(bean)으로 등록할 수 있게 한다.
- 따라서, Spring에서 새로운 어노테이션을 정의할 때는 대부분의 경우 `@Retention(RetentionPolicy.RUNTIME)`을 사용하여 런타임에 어노테이션 정보를 유지하도록 설정한다.
