---
title: "[코드트리 챌린지] 1주차 : 여정의 시작"
date: 2023-09-11 13:22:00 +0900
categories: [algorithm, codetree]
tags: [코드트리, 코딩테스트, 코딩테스트실력진단 ]
---

## 들어가기에 앞서
* 대학교 3학년인 지금 최근들어 나는 알고리즘에 대한 중요성을 절실히 느끼고 있다. 이전에 나는 알고리즘은 실무에서 제대로 활용될 일이 드물다는 얘기를 듣고 알고리즘 보단 개발 실력을 키우는 데에 집중하고 있었다. 
* 때문에 나의 알고리즘 실력은 같은 학년에 비해 많이 떨어지는 실력이었고, 기본적인 언어 이해도를 빼곤 알고리즘 기법이나, 분야에 대해 전무했다. 하지만 최근에 코드 트리를 알게되었고, 해당 사이트의 모의 테스트로 진단을 본 결과 객관적인 나의 현재 수준을 알게 되었고 지금부터 알고리즘에 집중해보기로 하였다.

## 1차 실력 진단 결과
![test-result](/assets/img/codetree-week1/test_result.png)

![test-result2](/assets/img/codetree-week1/test_result2.png)

* 현재 나의 실력은 총 11 문제 중에 5문제 밖에 풀지 못하는 수준이었고, 창피할 따름이었다. 코드 트리 사이트에서는 내가 어느 카테고리의 문제에서 막혔는지 자세히 알려줬고, 이에 대한 로드맵을 제시해줬다.

![road-map](/assets/img/codetree-week1/codetree_4.png)

* 해당 로드맵을 토대로 나는 기초에서부터 다시 탄탄히 쌓아 올리자는 마인드로 NOVICE MID부터(NOVICE LOW는 컴퓨터 공학도로써 당연히 알아야할 기초적인 문법에 대한 설명과 자료형, 입출력 등과 관련된 문제들로 구성되어 있어 간단히 훑어보고 헷갈리는 부분을 풀어가는 형식으로 넘어갔다) 차근차근 풀어보기로 했다.

## 1주차에 배운 점
* 사실 나는 'NOVICE MID의 대부분에 내용도 내가 아는 내용이지 않을까?' 라는 생각을 가지고 있었다. 하지만 예상외로 문제를 접근하는 방식이나, 사고해야될 점을 자세히 짚어주고, 이에 대한 충분한 예시와 이론적 설명이 제시 되어있었다.

###  첫 번째
[링크](https://www.codetree.ai/missions/5/problems/maximum-overlapped-segments/introduction)

* 첫번째로 배웠던 점은 최대로 겹치는 구간과 지점에 대한 이론적 지식이었다. 어찌보면 쉬운 이론과 당연한 지식이었지만, 그로 인해 놓칠 수 있는 함정이 있는 이론이다. 그건 바로 구간과 지점에 대한 미묘한 차이이다.
![section-image](/assets/img/codetree-week1/codetree_2.png)

* 만약 인덱스를 지점으로써 바라본다면 인덱스 한개당 하나의 지점이기 때문에 정말 당연하고 쉬운 이론이다.
![spot-image](/assets/img/codetree-week1/codetree_3.png)

* 하지만 인덱스를 **지점**으로써 바라본다면 헷갈리는 요소가 추가된다. 위의 그림과 같이 두 사람이 각각 [2, 4]와 [5, 8]을 지났다면 두 사람이 겹치는 지점은 1곳 밖에 포함이 되지 않는다. 때문에 헷갈리지 않기 위해선 한 사람이 [x, y]를 지났다면 y-1까지의 **구간**을 지난 것이다.
* 이와 관련된 문제로 내가 애먹었던 문제들을 아래 링크로 남겨두겠다
* [링크](https://www.codetree.ai/missions/5/problems/area-been-to-and-from2/description)
* [링크](https://www.codetree.ai/missions/5/problems/painting-white-black/description)

### 두번째
* [링크](https://www.codetree.ai/missions/5/problems/the-moment-we-meet/introduction)
* 이 문제 또한 쉽게 생각할 수 있지만 발상이 떠오르지 않는다면 의미없는 구현을 하게 될 수 있는 이론이다. 
---
A, B가 1초에 1m씩 움직입니다.
A는 9초 동안 앞으로 움직이다가, 3초간 뒤로 오고, 다시 5초간 앞으로 움직입니다.
B는 2초간 뒤로 갔다가, 앞으로 2초 갔다가, 1초간 뒤로 오고, 다시 12초간 앞으로 움직입니다.
A, B가 움직임을 시작한 이후에 다시 만나게 되는 시간은 몇 초 뒤일까요?

---
* 다음과 같이 A와 B가 독립적인 규칙을 가지고 움직이는 경우 어떤식으로 두 A, B가 만나는 타이밍에 대해 계산할 수 있는지에 대한 문제이다.
* 이 같은 경우 A, B 개별적으로 시뮬레이션을 진행하되, 각각의 사람이 매 초마다 어느 위치에 있었는지를 기록해주는 식으로 이 문제를 해결해 볼 수 있다.
![move-AB](/assets/img/codetree-week1/codetree_5.png)

* 이 이론과 관련된 내용으로 내가 애먹은 문제들의 링크를 밑에 남겨둔다
* [링크](https://www.codetree.ai/missions/5/problems/robot-moving-from-side-to-side/description)
* [링크](https://www.codetree.ai/missions/5/problems/correlation-between-shaking-hands-and-infectious-diseases2/description)

## 느낀점
* 여타 알고리즘 문제 사이트와 달리 코드트리는 순간의 착각으로 발생할 수 있는 기본적인 실수들까지 캐치해주며 이론적인 설명과 함께 그와 관련된 문제들을 다양하게 내준다는 생각이 들었다.
* 아마 원래의 나였으면 맞왜틀을 계속 외치며, 해답 코드를 보고는 자괴감과 우울감에 빠졌을 것이다.
* 어찌보면 코드 트리야 말로 알고리즘을 처음 접하는 사람이나 생소한 사람들이 알고리즘 고수로 향할 수 있는 가장 빠른길이 아닐까라고 감히 생각한다. 앞으로도 열심히 코드 트리에서 문제를 풀어야겠다!


