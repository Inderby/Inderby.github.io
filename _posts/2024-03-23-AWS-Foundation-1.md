---
title: [Cloud Essentials. AWS Foundation]
author: excelsiorKim
date: 2024-03-23 23:10:03 +0900
categories: [Cloud, essential]
tags: [Cloud, AWS, essential]
keywords: [Cloud, AWS, essential]
description: 클라우드 기본 지식 공부 기록
toc: true
toc_sticky: true
---

# AWS란?

- AWS는 다양한 글로벌 클라우드 기반 제품을 제공하는 안전한 클라우드 플랫폼이다.
- AWS는 컴퓨팅, 스토리지, 네트워킹, 데이터베이스, 및 기타 IT 리소스 및 관리 도구에 대한 **온디맨드 액세스**를 제공한다.
- AWS는 유연성을 제공한다.
- 필요한 개별 서비스를 사용하는 기간 동안에만 비용을 지불하면 된다.
- AWS 서비스는 빌딩 블록을 조립하는 것 처럼 서로 유기적으로 작동한다.

## AWS 글로벌 인프라

- AWS 글로벌 인프라는 고품질 글로벌 네트워크 성능을 갖춘 유연하고 안정적이며 확장 가능하고 안전한 클라우드 컴퓨팅 환경을 제공하도록 설계 및 구축되었다.

## AWS 리전

- AWS 리전은 지리적 영억이다.
  - 리전 간 데이터 복제는 사용자가 제어한다.
  - 리전 간 통신은 AWS 백본 네트워크 인프라를 사용한다.
- 각 리전은 완전한 이중화 및 짧은 지연시간 네트워크 연결을 제공한다.
- 리전은 세 개 이상의 가용 영역으로 구성된다.

### 리전 선택

- 데이터 거버넌스, 법적 요구사항
- 고객에 대한 근접성(대기 시간)
- 리전 내에서 사용 가능한 서비스
- 비용(리전별로 다름)

### 가용 영역

- 각 리전에는 여러 가용 영역이 있다.
- 각 가용 영역은 AWS 인프라의 완전히 격리된 파티션이다.
  - 현재 전 세계에 99개의 가용 영역이 있다.
  - 가용 영역은 개별 데이터 센터로 구성된다.
  - 내결함성을 갖도록 설계되어있다.
  - 고속 프라이빗 링크를 사용하여 다른 가용 영역과 상호 연결된다.
  - AWS는 복원력을 위하 복수의 가용영역에 데이터와 리소스를 복제할 것을 권장한다.

### AWS 데이터 센터

- AWS 데이터 센터는 보안을 위해 설계됨.
- 데이터 센터에는 일반적으로 50000~80000대의 물리적 서버가 있다.
- 글로벌 엣지 네트워크를 사용하여 사용자의 대기 시간을 감소 시킨다.

## AWS에서 지원하는 컴퓨팅 서비스

![AWS-computing-image](/assets/img/2024-03-23-cloud-aws/aws-computing-img.png)

- Amazon EC2 : 크기 조정이 가능한 아마존 컴퓨팅 시스템을 클라우드의 가상 머신으로 제공해주는 서비스
- Amazon EC2 Auto Scaling : 정의하는 조건에 맞게 EC2 인스턴스를 자동으로 추가하거나 제거 해준다.
- Amazon Elastic Service(ECS) : Docker Container를 지원하는 확장성이 뛰어난 고성능 컨테이너 오케스트레이션 서비스
- Amazon Elastic Container Registry(ECR) : 개발자가 Docker container 이미지를 손쉽게 저장, 관리, 배포할 수 있게 하는 완전관리형 Docker 컨테이너 레지스트리이다.
- Amazon Elastic Beanstalk : batch 또는 Microsoft Internet Information Services(IIS)와 같은 잘 알려진 서버에 웹 애플리케이션 및 서비스를 배포하고 확장할 때 사용되는 서비스이다.
- Lambda : 서버를 프로비저닝하거나 관리하지 않고도 코드를 실행할 수 있게 해줌. 사용하는 컴퓨팅에 대해서만 비용을 지불한다.
- Amazon Elastic Kubernetes Service(EKS) : AWS에서 Kubernetes를 사용하는 컨테이너식 애플리케이션을 손쉽게 배포하고 관리하고 확장할 수 있다.
- AWS Fargate : 서버 또는 클러스터를 관리할 필요 없이 컨테이너를 실행할 수 있는 Amazon ECS , EKS 용 컴퓨팅 엔진이다.

### 컴퓨팅 서비스 분류

![aws-service](/assets/img/2024-03-23-cloud-aws/aws-service.png)

## Elastic Load Balancing

- 들어오는 애플리케이션 또는 네트워크 트래픽을 하나 이상의 가용 영역의 여러 대상에 분배
- 시간이 지나면서 애플리케이션 트래픽이 변함에 따라 로드 밸런서 확장

## AWS에서 지원하는 스토리지 서비스

### Amazon S3

- 데이터를 버킷 내에 객체로 저장
- 무제한 스토리지
  - 단일 객체는 5TB로 제한
- 100%에 가까운 내구성
- 버킷 및 객체에 대한 세분화된 액세스

### Amazon EBS

- 인스턴스용 영구 블록 스토리지
- 복제를 통해 보호
- 여러가지 드라이브 유형
- 몇 분만에 규모 확장 또는 축소
- 프로비저닝한 만큼만 비용 지불
- 스냅샷 기능
- 암호화 사용 가능

### 공유 파일 시스템

여러 인스턴스가 동일한 스토리지를 사용해야 하는 경우 어떻게 하는가

- Amazon EBS는 하나의 인스턴스에만 연결된다.
- Amazon S3는 옵션이지만 이상적이지는 않다.
- Amazon EFS와 FSx는 이 작업에 적합하다.

### Amazon S3 Glacier

- 소량 데이터 아카이빙 및 장기 백업
- 3 ~ 5 시간 또는 12시간 이내
- Amazon S3 콘텐츠를 Amazon Glacier에 라이프 사이클 아카이빙하도록 구성할 수 있다.
- 장기 저장 및 액세스 빈도가 낮은 데이터 저장 용도로 사용

### Amazon Aurora

- 엔터프라이즈급 관계형 데이터베이스
- MySQL 또는 PostgreSQL 호환
- 표준 MySQL 데이터베이스보다 최대 5배 빠름
- 표준 PostgreSQL 데이터베이스보다 최대 3배 빠름
- Amazon S3에 지속적인 백업
- 지연 시간이 짧은 읽기 전용 복제본 최대 15개

### Amazon DynamoDB

모든 규모에서 사용 가능한 빠르고 유연한 NoSQL 데이터베이스 서비스

- 완전 관리형
- 지연 시간이 짧은 쿼리
- 세분화된 엑세스 제어
- 리전 및 글로벌 옵션

## 네트워크 서비스

![aws-network](/assets/img/2024-03-23-cloud-aws/aws-network.png)

### 보안, 자격 증명 및 규정 준수 서비스

![aws-security](/assets/img/2024-03-23-cloud-aws/aws-security.png)

- AWS IAM : AWS 사용자 및 그룹을 생성하고 관리할 수 있다.
- AWS cognito : 웹 및 모바일 애플리케이션에 사용자 가입, 로그인 및 액세스 제어 기능을 추가할 수 있다.
- AWS artifact : AWS 보안 및 규정 준수 보고서에 대한 온디맨드 액세스를 제공한다.
- AWS KMS : 다양한 AWS 서비스에 대한 암호화 키 사용을 제어할 수 있다.
- AWS shield : AWS에서 실행되는 애플리케이션을 보호하는 관리형 DDos 방어 서비스이다.
  ![aws-res](/assets/img/2024-03-23-cloud-aws/aws-responsibilty.png)
