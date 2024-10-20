---
title: [Spring Batch 개념]
author: excelsiorKim
date: 2024-08-15 12:30:03 +0900
categories: [Spring, Batch]
tags: [Spring, Batch]
keywords: [Spring, Batch]
description: 스프링 배치 기본 개념
toc: true
toc_sticky: true
---

# Batch 기본 개념

![Spring-Batch-Concept](/assets/img/2024-08-15-spring-batch/spring-batch-concept.png)

## Batch Processing(배치 처리)
- 대량의 데이터를 일괄적으로 처리하는 작업
- 실시간 처리와 대비되는 개념
- 주로 정기적으로 실행되거나 대량의 데이터 처리가 필요한 경우 사용

### Job
- Spring Batch의 가장 상위 개념
- 하나의 배치 처리 작업 단위
- 하나 이상의 Step으로 구성

### Step
- Job을 구성하는 독립적인 처리 단계
- 실제 배치 처리를 정의하고 제어하는데 필요한 모든 정보를 포함
- ItemReader, ItemProcessor, ItemWriter로 구성될 수 있음

### ItemReader
- 데이터를 읽어오는 인터페이스
- 다양한 소스(데이터베이스, 파일 등)로부터 데이터를 읽어올 수 있음

### ItemProcessor
- ItemReader로 읽어온 데이터를 처리하는 인터페이스
- 데이터 변환, 필터링, 유효성 검사 등의 작업 수행

### ItemWriter
- 처리된 데이터를 저장하는 인터페이스
- 데이터베이스, 파일 등 다양한 대상에 데이터를 쓸 수 있음

## entity 개념
### JobRepository
   - 배치 처리 정보를 저장하고 관리하는 저장소
   - Job의 실행 정보, Step의 실행 정보 등을 저장

### JobLauncher
- Job을 실행하는 역할
- Job과 JobParameters를 인자로 받아 배치 작업을 시작

### JobInstance
- 특정 Job의 실행 단위
- 같은 Job이라도 다른 JobParameters로 실행되면 새로운 JobInstance가 생성됨

### JobExecution
- JobInstance에 대한 한 번의 실행 시도
- 실행 상태, 시작시간, 종료시간 등의 정보를 가짐

### StepExecution
- Step에 대한 한 번의 실행 시도
- 실행 상태, 커밋 횟수, 처리된 아이템 수 등의 정보를 가짐

### ExecutionContext
- 배치 작업의 실행 상태를 저장하는 공유 객체
- Job이나 Step의 실행 중 데이터를 저장하고 공유할 때 사용

### JobParameters
- Job을 실행할 때 필요한 파라미터들
- 같은 Job이라도 다른 JobParameters로 실행하면 새로운 JobInstance가 생성됨

### Chunk-oriented Processing
- 대량의 데이터를 일정 단위(chunk)로 나누어 처리하는 방식
- 메모리 사용을 효율적으로 관리하고 트랜잭션 처리를 용이하게 함

### Tasklet
- Step의 실행 단위
- 단일 태스크를 수행하는 데 사용되며, Chunk-oriented Processing과는 다른 방식으로 Step을 구성할 때 사용
