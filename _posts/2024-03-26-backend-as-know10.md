---
title: [part8. 테스트와 CI/CD]
author: excelsiorKim
date: 2024-03-26 13:10:03 +0900
categories: [Book, back-end]
tags: [back-end, CS, book]
keywords: [back-end, CS, book]
description: 아는 만큼 보이는 백엔드 chapter8
toc: true
toc_sticky: true
---

# 테스트의 개요

백엔드 개발 과정에는 다양한 변수가 존재하고, 그에 따라 즉각 대응하고 수정해야 하는 상황이 필연적으로 발생한다. 예상했던 것보다 시간과 비용이 많이 들 수도 있고, 의외의 부분에서 결함이나 오류가 발생할 수도 있다. 배포하고 난 뒤 웹 애플리케이션을 운영하는 과정에서 오류가 발생할 수도 있다.

이처럼 웹 애플리케이션은 언제 어디서든 오류가 발생할 가능성이 있기 때문에, 신뢰할 수 있고 보다 경제적인 방법으로 개발하기 위한 방법론이 연구되고 있다. 그리고 테스트는 그중에서 많은 개발자가 중요하게 생각하는 방법론이다.

## 테스트의 개념

테스트는 테스트 코드를 작성해 웹 애플리케이션의 기능, 동작, 성능 등을 확인하고 검증하는 것을 말한다. 개발자가 작성한 코드가 테스트를 통과하면 예상대로 동작하는 의미이고, 테스트를 통과하지 못하면 결함이나 오류가 있다는 의미이다.

개발자가 일일이 코드를 실행해보지 않고도 코드가 잘 동작하리라는 것을 보장받을 수 있도록, 발생 가능한 테스트 케이스(test-case, 특정 기능 또는 시나리오의 검증을 위해 설계된 테스트의 단위)를 코드로 작성해 실행함으로써 예상치 못한 부작용을 잡아낸다.

## 테스트의 이점

- 기능적 요구 사항 충족 확인
- 초기의 요구 사항 파악을 위한 도구
- 협업을 위한 문서

# 테스트의 종류

![V-model](/assets/img/2024-03-26-test-and-ci-cd/V-model.png)

## 단위 테스트(unit testing)

소스 코드를 구성하는 가장 작은 단위의 개체를 테스트 하는 것이다.
소스 코드를 이루는 가장 작은 단위의 개체, 즉 개별 함수, 클래스, 메서드 등을 테스트하고 각 개체가 정상적으로 동작하는지 확인한다.

## 통합 테스트(integration testing)

모듈 간의 상호 작용을 테스트 하는 것이다. 단위테스트가 개별 코드를 테스트하는 것이라면, 통합 테스트는 개별 코드를 결합한 기능이 잘 동작하는지 확인하는 것이다.

## 시스템 테스트

시스템 테스트(System testing)는 통합 테스트 이후에 진행하는 테스트입니다. 개발한 웹 애플리케이션이 사용자의 요구 사항을 충족하는지 확인할 뿐만 아니라 전체적인 성능, 안정성, 보안 등의 측면도 테스트 한다.

시스템 테스트는 실제 사용자가 직접 사용하는 것처럼 테스트를 진행하기 때문에 사용자 입장에서 해당 기능이 제대로 동작하고 요구사항에 부합하는지 확인할 수 있다.

또한 이 과정에서는 웹 애플리케이션이 다양한 하드웨어와 운영체제에서 원활하게 동작하는지도 확인할 수 있다.

결론적으로 시스템 테스트는 전체 웹 애플리케이션이 사용자의 요구 사항을 충족하는지, 여러 환경에서도 원활하게 동작하는지 확인한다. 이러한 과정을 통해 최종적으로 상용 단계로 넘어가기 전에 발생할지도 모를 예외나 문제 상황을 최대한 확인하고 예방할 수 있습니다.

# 테스트 주도 개발

테스트를 하면 품질이 더 우수하고 유지보수하기 좋은 웹 애플리케이션을 개발할 수 있다. 테스트의 이러한 장점을 적용한 소프트웨어 개발론이 테스트 주도 개발과 행동 주도 개발이다.

## 테스트 주도 개발

테스트 주도 개발(TDD, Test-Driven Development)은 말 그래도 테스트가 개발을 주도하는 방식이다. 테스트 주도 개발은 기본적으로 빨강, 초록, 리팩터링이라는 세 단계로 나뉜다.

- 빨강(red) : 아직 구현되지 않은 기능에 대해 테스트를 작성하는 단계로, 이는 도로를 만들기 전에 도로 지도를 그리는 것과 같다. 이 상태로 테스트를 돌리면 실패하는 것이 당연하다.
- 초록(green) : 빨강 단계에서 실패한 테스트를 통과하기 위한 코드를 작성하는 단계로, 이 단계에서는 테스트를 통과하는 것이 목표이므로 코드가 완벽하지 않을 수도 있다.
- 리팩터링(refactoring) : 테스트를 통과한 코드를 개선하는 단계로, 이는 만들어진 도로를 보완하고 더 나은 도로로 개선하는 것과 같다. 이 단계에서는 코드의 품질을 향상하는 것이 목표이다.

## 행동 주도 개발

행동 주도 개발(BDD, Behavior-Driven Development)은 테스트 주도 개발을 기반으로 하되, 사용자의 행동을 중심으로 테스트를 하는 개발 방법론이다. 웹 애플리케이션이 어떻게 행동해야하는지에 초점을 맞춘다. 이는 실제 사용자 관점에서 웹 애플리케이션을 바라보는 것과 같다.

행동 주도 개발에서는 '사용자 스토리'라는 개념을 사용해 테스트를 작성한다.
사용자 스토리는 '사용자로서 나는 이러한 기능을 원한다. 따라서 웹 애플리케이션을 실행해 이러한 이득을 얻고 싶다'라는 형석으로 작성한다. 즉 실제 사용자의 요구 사항을 반영한 테스트 케이스를 작성하는 것이다.

사용자 스토리에 맞는 주요 요구 사항을 특징(feature)으로 정의하고, 특징을 충족하기 위한 시나리오(Scenario)를 작성한다. 이때 현재 주어진 환경(Given), 사용자의 행위(When), 그에 따른 기대 결과(Then) 등을 작성한다.

# CI/CD

이미 배포한 소스 코드에 새로운 API를 추가하거나 기존 API를 수정했다면 소스 코드를 다시 빌드 및 테스트한 후 재배포해야 한다. 그런데 이는 생각보다 자주 일어나는 일이다. 따라서 이러한 과정을 자동화하지 않으면 그때마다 수동으로 서버에 들어가 작업해야 하며, 재배포가 잦을수록 실수가 발생할 가능성도 높아진다. 이에 소스 코드를 안정적으로 빌드하고 배포하기 위해 CI/CD라는 자동화 방법론이 탄생했다.

## CI/CD의 개념

CI/CD(Continuous Integration / Continuous Deployment)는 지속적인 통합/지속적인 배포'라는 의미이다.

CI는 소스 코드의 변경 사항을 자동으로 빌드 및 테스트해 통합하는 것으로, 이를 통해 소스 코드의 품질을 높이고 빠른 시간 내에 버그를 해결할 수 있다.

CD는 웹 애플리케이션을 지속적으로 개발하고 테스트해 언제든 배포 가능한 상태로 유지하는 것을 말한다.
개발된 웹 애플리케이션이 자동화된 빌드-테스트-배포 프로세스를 거쳐 언제든지 배포가 가능한 상태로 유지된다.

소스 코드를 빌드-테스트-배포하는 과정을 거쳐 개발을 추진하는 프로세스를 CI/CD 파이프라인(CI/CD pipeline)이라고 한다. 이 과정에서 개발자는 반복적인 빌드-테스트-배포 작업을 자동화하고, 재배포 과정에서 버그를 찾아 빠르게 수정할 수 있다. 따라서 웹 애플리케이션의 릴리즈 사이클이 단축되고 개발자의 높은 생산성이 유지된다.

## CI/CD 도구

CI//CD도구는 CI/CD 파이프라인을 구축하는 데 사용되는 도구로, CI를 제공하는 CI 도구, CD를 제공하는 CD도구, 둘 다 제공하는 CI/CD도구가 있다.

- CI 도구
  - 젠킨스 : 가장 인기 있는 오픈소스 CI도구로, 다양한 플러그인을 제공하므로 기능을 확장할 수 있다.
  - 깃허브 액션 : 깃허브에서 제공하는 자체 CI도구로, 소스 코드가 변경되면 이를 감지하고 자동으로 빌드 및 테스트를 진행한다.
  - 트래비스 CI : 깃허브와 통합해 사용할 수 있는 CI 도구로, 설정이 간단하고 다양한 개발 언어와 프레임워크를 지원한다.
  - 서클 CI : 클라우드 기반의 CI 도구로, 빠른 속도와 쉬운 설정, 유연한 확장성이 장점이며 도커와 통합할 수 있어 편리하다.
- CD 도구
  - AWS 코드디플로이 : AWS에서 제공하는 CD도구로, 웹 애플리케이션의 배포를 자동화해 개발자가 빠르고 안정적으로 배포할 수 있다,
- CI/CD 도구
  - 깃랩 CI/CD : 깃랩에서 제공하는 통합 CI/CD 도구로, 코드 저장소와 통합해 빌드-테스트-배포를 자동화한다.
  - 젠킨스 X : 쿠버네티스 위에서 동작하는 통합 CI/CD 도구로, 클라우드 네이티브 애플리케이션의 빌드-테스트-배포를 자동화한다.

이 중에서 젠킨스 + AWS 코드디플로이, 깃허브 액션 + AWS 코드디플로이, 깃랩 CI/CD가 대중적으로 많이 사용되고 있다.

## 데브옵스와 CI/CD의 차이

CI/CD는 데브옵스와 비교되곤한다. 데브옵스와 CI/CD는 둘 다 웹 애플리케이션 개발 프로세스를 개선하는 데 도움이 되지만 접근 방식이 서로 다르다. 데브옵스는 문화와 프로세스에 중점을 두고 CI/CD는 도구와 자동화에 중점을 두는데, 이 두 가지 접근 방식을 함께 사용하면 개발 프로세르를 극적으로 개선할 수 있다.

- 데브옵스
  데브옵스(DevOps)는 웹 애플리케이션 개발 팀과 운영 팀의 협력, 소통을 강조하는 문화와 방법론을 의미한다. 개발 팀과 운영 팀 사이의 갈등과 불협화음을 해소해 웹 애플리케이션 개발 및 배포 프로세르를 개선하는 것이 목표이다. 개발 문화, 개발 조직, 개발 프로세스, 개발 도구 등 다양한 측면이 데브옵스에 포함될 수 있다.

데브옵스가 보다 넓은 개발 문화와 개발 방법론을 포함한다면, CI/CD는 데브옵스를 실현하기 위한 구체적인 프로세스와 도구의 일부분이라고 볼 수 있다.