---
title: [Cloud Essentials. AWS Billing and Cost Management]
author: excelsiorKim
date: 2024-03-29 15:10:03 +0900
categories: [Cloud, essential]
tags: [Cloud, AWS, essential]
keywords: [Cloud, AWS, essential]
description: 클라우드 기본 지식 공부 기록
toc: true
toc_sticky: true
---

# Billing and Cost Management

결ㅔ 및 미납 청구서를 확인하고, 결제 방법을 관리하고, 비용 및 사용량을 모니터링 및 분석할 수 있다.

이 서비스 제품군을 사용하여 다음을 수행할 수 있다.

- AWS 비용을 예측하고 계획
- 비용이 임계값을 초과하거나 임계값에 근접하면 알림을 받음
- AWS 리소스에 대한 최대 투자를 평가한다.
- 여러 AWS 계정을 사용하는 경우 회계를 간소화한다.

대시 보드 페이지를 통해 서비스 별 금액과 총 금액을 확인할 수 있다.

## Cost Explorer

콘솔 기반 비용 및 사용량 보고를 볼 수 있다.

- UI
- API
- CSV
  와 같은 형식으로 정보를 제공해준다.

또한 기본 보고서 및 사용자 지정 보고서를 만들 수 있다.

### AWS Current Usage Report(CUR)

엑셀 형식으로 각종 청구 비용과 서비스 정보를 제공해준다.

이를 AWS S3 버킷에 저장하고 이를 Cost Intelligence 를 이용해 시각화 가능

### AWS Budgets

AWS 비용 모니터링을 구현하는데 도움이 되는 무료 AWS 서비스이다.

- 비용 및 사용량 차이에 미리 대비할 수 있다.
- 사용자 지정 비용 및 사용 예산을 설정하여 AWS 지출을 관리한다.
- 여러 차원에서 비용 또는 사용량을 추적한다.

알아야 할 점

- AWS는 예산 예측을 생성하기 위해 약 5주간의 사용 데이터를 필요로 한다.
- 예산 알림은 알림당 최대 10개의 이메일 주소와 1개의 Amazon SNS로 전송할 수 있다.
- AWS Budgets을 AWS Chatbot과 통합하여 Slack과 Amazon Chime을 통해 알림을 받을 수 있다.
- 최대 50개의 AWS Budgets Report가 포함되며, 각 보고서에는 최대 50개의 예산이 포함되어 있다. 이를 최대 50명에게 제공할 수 있다.

### AWS Cost Anomaly Detection

무료 모니터링 기능으로써 고급 기계 학습 기술을 사용하여 비정상적인 지출과 근본 원인을 식별한다.

세 단계를 통해 상황에 맞는 비용 모니터를 새엇ㅇ하고 비정상적인 지출이 감지되면 알림을 받을 수 있다.
