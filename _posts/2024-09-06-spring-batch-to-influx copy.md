---
title: [Spring Batch 기본 이론 지식 정리]
author: excelsiorKim
date: 2024-09-06 08:30:03 +0900
categories: [Spring, Batch]
tags: [Spring, Batch, InfluxDB]
keywords: [Spring, Batch, InfluxDB]
description: InfluxDB에 대용량 데이터 업로드
toc: true
toc_sticky: true
---

## Spring Batch란?

- 큰 단위의 작업을 일괄 처리 할 때 사용되는 Spring 프레임워크 시리즈 중 하나
- **대부분 처리량이 많고 비 실시간성 처리에 사용**
  - 대용량 데이터 계산, 정산, 통계, 데이터베이스, 변환 등
- **보통 컴퓨터 자원을 최대로 활용하여 빠르고 안정적이게 작업을 처리해야함.**
  - 컴퓨터 자원 사용이 낮은 시간대에 배치를 처리하거나
  - 배치만 처리하기 위해 사용자가 사용하지 않는 또 다른 컴퓨터 자원을 사용
- **사용자 상호 작용으로 실행되기 보단, 스케줄러와 같은 시스템에 의해 실행되는 대상**
  - 예를 들면 매일 오전 10시에 배치 실행, 매주 월요일 12시 마다 실행
- 스프링 배치 실행 단위는 **Job과 Step**으로 이루어짐
- 비교적 간단한 작업(**Tasklet**) 단위 처리와 대량 묶음(**chunk**) 단위 처리

## Job

- Job은 `JobLauncher` 에 의해 실행
- Job은 N개의 Step을 실행할 수 있으며, 흐름(Flow)를 관리할 수 있다.
- Step은 Job의 세부 실행 단위이며, N개가 등록돼 실행된다.
- Step의 실행 단위는 크게 2가지로 나눌 수 있다.
  - **Chunk** 기반 : 하나의 큰 덩어리를 N개 씩 나눠서 실행
  - **Tasklet** 기반 : 하나의 작업 기반으로 실행
    - 배치 처리 과정이 비교적 쉬운 경우 사용
    - 대량 처리를 하는 경우 더 복잡해질 수 있음
    - 하나의 큰 덩어리를 여러 덩어리로 나누어 처리하기 부적합
- Chunk 기반 Step은 `ItemReader`, `ItemProcessor`, `ItemWriter` 가 있다.
  - 여기서 `Item` 은 배치 처리 대상 객체를 의미한다.
- `ItemReader` 는 배치 처리 대상 객체를 읽어 `ItemProcessor` 또는 `ItemWriter` 에게 전달한다.
- `ItemProcessor`는 input 객체를 output 객체로 filtering 또는 processing 해 `ItemWriter` 에게 전달한다.
  - `ItemProcessor` 은 Optional하다
- `ItemWriter` 는 배치 처리 대상 객체를 처리한다.
  - 예를 들면, DB update를 하거나, 처리 대상 사용자에게 알림을 보낸다.

## 메타 테이블

- 배치 실행을 위한 메타 데이터가 저장되는 테이블
- **BATCH_JOB_INSTANCE**
  - Job이 실행되며 생성되는 최상위 계층의 테이블
  - Job_name과 Job_key를 기준으로 하나의 row가 생성되며, 같은 Job_name과 Job_key가 저징될 수 없다.
  - job_key는 BATCH_JOB_EXECUTION_PARAMS에 저장되는 Parameter를 나열해 암호화하여 저장한다.
- **BATCH_JOB_EXECUTION**
  - Job이 실행되는 동안 시작/종료 시간, job 상태 등을 관리
- **BATCH_JOB_EXECUTION_PARAMS**
  - Job을 실행하기 위해 주입된 parameter 정보 저장
- **BATCH_JOB_EXECUTION_CONTEXT**
  - Job이 실행되며 공유해야할 데이터를 직렬화하여 저장
- **BATCH_STEP_EXECUTION**
  - step이 실행되는 동안 필요한 데이터 또는 실행된 결과 저장
- **BATCH_STEP_EXECUTION_CONEXT**
  - Step이 실행되며 공유해야할 데이터를 직렬화해 저장

### Job 테이블 매핑 관계

- JobInstance 테이블 : BATCH_JOB_INSTANCE 테이블과 매핑
- JobExecution 테이블 : BATCH_JOB_EXECUTION 테이블과 매핑
- JobParameters 테이블 : BATCH_JOB_PARAMETER 테이블과 매핑
- ExecutionContext 테이블 : BATCH_JOB_EXECUTION_CONTEXT 테이블과 매핑

### Instance 생성 기준

- JobInstance의 생성 기준은 JobParameters 중복 여부에 따라 생성
- 다른 Parameter로 Job이 생성되면, JobInstance가 생성
- 같은 Parameter로 Job이 생성되면, 이미 생성된 JobInstance가 실행
  - 이때 Job이 재생성 대상이 아니면 에러가 발생
- JobExecution은 항상 새롭게 생성
- Job을 항상 새로운 JobInstance가 생성될 수 있도록 RunIdIncrementer 제공
  - RunIdIncrementer는 항상 다른 Run.id를 Parameter로 설정

### Step 테이블 매핑 관계

- StepExecution : BATCH_STEP_EXECUTION 테이블 매핑 관계
- ExecutionContext : BATCH_STEP_EXECUTION_CONTEXT 테이블과 매핑

### 데이터 사용 범위

- JobExecutionContext : Job 내에서만 공유할 수 있음
- StepExecutionContext : Step 내에서만 공유할 수 있음 (Step 끼리는 공유가 불가능함)

### chunk base step 원리

- reader에서 null을 return 할 때 까지 Step 반복됨
- <INPUT, OUTPUT>chunk(int)
  - reader에서 INPUT을 return
  - processor에서 INPUT을 받아 processing 후 OUTPUT을 return
    - INPUT, OUTPUT은 같은 타입일 수 있음
  - writer에서 List<OUTPUT>을 받아 write

### JobParameters 이해

- 배치 실행에 필요한 값을 parameter를 통해 외부에서 주입
- JobParameter는 외부에서 주입된 Parameter를 관리하는 객체
- Parameter를 JobParameter와 SpringEL(Expression Language)로 접근
  - String parameter = jobParameters.getString(key, defaultValue);
  - @Value(“#{jobParameters[key]}”)

### @JobScope과 @StepScope의 이해

- @Scope는 어떤 시점에 bean을 생성/소멸 시킬 지 bean의 lifeCycle을 설정
- @JobScope는 job 실행 시점에 생성/소멸
  - Step에 선언
- @StepScope는 Step의 실행 시점에 생성/소멸
  - Tasklet, Chunk(ItemReader, ItemProcessor, ItemWriter)에 선언
- Spring 의 @Scope과 같은 것
- Job과 Step 라이프 사이클에 의해 생성되기 때문에 Thread Safe하게 작동
  - 여러 Step에서 하나의 Tasklet을 동시에 실행한다면 각 step마다 새로운 Tasklet을 실행하기 때문이다.
- @Value(“#{jobParameter[key]}”)를 사용하기 위해 @JobScope와 @StepScope는 필수

### ItemReader Interface 구조

- 배치 대상 데이터를 읽기 위한 설정
  - 파일, DB, 네트워크 등에서 읽기 위함
- Step에 ItemReader는 필수
- 기본 제공되는 ItemReader 구현체
  - file, jdbc, jpa, hibernate, kafka, etc…
- ItemReader 구현체가 없으면 직접 개발
- ItemStream은 ExecutionContext로 read, write 정보를 저장
- CustomItemReader 예제 참고

### CSV 파일 데이터 읽기

- FlatFileItemReader 클래스로 파일에 저장된 데이터를 읽어 객체에 매핑

### JDBC 데이터 읽기

- Cursor 기반 조회
  - 배치 처리가 완료 될 때까지 DB Connection이 연결
  - DB Connection 빈도가 낮아 성능이 좋은 반면, 긴 Connection 유지 시간이 필요
  - 하나의 Connection에서 처리되기 때문에, Thread Safe 하지 않음
  - 모든 결과를 메모리에 할당하기 때문에, 더 많은 메모리를 사용
- Paging 기반 조회
  - 페이징 단위로 DB Connection을 연결
  - DB Connection 빈도가 높아 비교적 성능이 낮은 반면, 짧은 Connection 유지 시간 필요
  - 매번 Connection을 하기 때문에 Thread Safe
  - 메이징 단위의 결과만 메모리에 할당하기 때문에, 비교적 더 적은 메모리를 사용

###  JPA 데이터 읽기

- 4.3+ 에서 Jpa 기반 Cursor ItemReader가 제공 됨
- 기존에는 Jpa는 Paging 기반의 ItemReader만 제공됨

### ItemWriter interface의 구조 이해

- ItemWriter는 마지막으로 배치 처리 대상 데이터를 어떻게 처리할 지 결정
- Step에서 ItemWriter는 필수
- 예를 들어 ItemReader에서 읽은 데이터를
  - DB에 저장, API로 서버에 요청, 파일에 데이터를 write
- 항상 write가 아님
  - 데이터를 최종 마무리하는 것이 ItemWriter

### JdbcBatchItemWriter 데이터 쓰기

- JdbcBatchItemWriter는 jdbc를 사용해 DB에 write
- JdbcBatchItemWriter는 bulk insert/update/delete 처리
  - insert into person (name, age, address) values(1,2,3),(4,5,6);
- 단건 처리가 아니기 때문에 비교적 높은 성능

### Jpa데이터 쓰기

- JpaItemWriter는 JPA Entity 기반으로 데이터를 DB에 write
- Entity를 하나씩 EntityManager.persist 또는 EntityManager.merge로 insert

### ItemProcessorinterface 구조

- ItemReader에서 읽은 데이터를 가공 또는 filtering
- Step의 ItemProcessor는 optional
- ItemProcessor는 필수는 아니지만, 책임 분리를 분리하기 위해 사용
- ItemProcessor는 I(Input)을 O(output)로 변환하거나
- ItemWriter의 실행 여부를 판단할 수 있도록 filtering 역할을 한다.
  - ItemWriter는 not null만 처리 한다.(즉, null이면 필터링 된다)

### JobExecutionListener, StepExecutionListener 이해

- 스프링 배치에서 전 처리, 후 처리를 하는 다양한 종류의 Listener 존재
  - interface 구현
  - @Annotation 정의
- Job 실행 전과 후에 실행할 수 있는 JobExecutionListener
- Step 실행 전과 후에 실행할 수 있는 StepExecutionListener

### StepListener 이해

- Step에 관련된 모든 Listener는 StepListener를 상속
  - StepExecutionListener
  - SkipListener
    - @OnSkipInRead
    - @OnSkipInWrite
    - @OnSkipInProcess
  - ItemReaderListener
    - @BeforeRead
    - @AfterRead
    - @OnReadError
  - ItemProcessListener
  - ItemWriteListener
    - @BeforeWrite
    - @AfterWrite
    - @OnWriteError
  - ChunkListener

### skip 예외 처리

- step 수행 중 발생한 특정 Exception과 에러 횟수 설정으로 예외처리 설정
- skip(NotFoundNameException.class), skipLiimit(3)으로 설정된 경우
  - NotFoundNameException 발생 3번까지는 에러를 skip한다
  - 이후 4번째 발생이 일어날 경우 Job과 Step의 상태는 실패로 끝나며, batch가 중단된다.
  - 단, 에러가 발생하기 전까지 데이터는 모두 처리된 상태로 남는다.
- step은 chunk 1개 기준으로 Transaction 동작을 한다

### Retry 예외 처리1

- Step 수행 중 간헐적으로 Exception 발생 시 재시도 설정
  - DB Deadlock, Network timeout 등
- Retry(NullPointerException.class), retryLimit(3)으로 설정된 경우
  - NulPointerException이 발생할 경우 3번까지 재시도
- 더 구체적으로 retry를 정의하려면 RetryTemplate 이용

### Retry 예외 처리2

- RetryListener.open
  - return false인 경우 retry를 시도하지 않음
- RetryTemplate.RetryCallback
- RetryListener.onError
  - maxAttempts 설정값 만큼 반복
- RetryTemplate.RecoveryCallback
  - maxAttempts 반복 후에도 에러가 발생한 경우 실행
- RetryListener.close

### Async Step 적용하기

- itemProcessor와 ItemWriter를 Async로 실행
- java.util.concurrent에서 제공되는 Future 기반 Asynchronous
- Async를 사용하기 위해서는 Spring-Batch-integeration 필요

### Mutli-Thread Step 적용하기

- Async-Step은 ItemProcessor와 ItemWriter 기준으로 비동기 처리
- Multi-Thread step은 Chunk 단위로 멀티스레드를 처리
- Thread-Safe한 ItemReader 필요

### Partition Step 적용하기

- 하나의 Master 기준으로 여러 Slave Step을 생성해 Step 기준으로 Mutli-Thread 처리
- 예를 들어
  - item이 40000개, Slave step이 8개면
  - 하나의 Slave Step 당 5000건 씩 나눠서 처리
- Slave Step은 각각 하나의 Step으로 동작

### ParallelStep

- n개의 Thread가 Step 단위로 동시 실행
- Multi-Thread Step은 chunk 단위로 동시 실행했다면, Parellel Step은 step 단위로 동시 실행
