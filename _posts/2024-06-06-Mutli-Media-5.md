---
title: [Multi-Media-Computing.5 video]
author: excelsiorKim
date: 2024-06-06 22:30:03 +0900
categories: [CS, Multi-Media-Computing]
tags: [CS, Multi-Media, Multi-Media-Computing, Image]
keywords: [CS, Multi-Media, Multi-Media-Computing, Image]
description: 멀티 미디어 컴퓨팅 공부
toc: true
toc_sticky: true
---

### Video Color Signals
- Composite color signal
  - Luminance 와 Chrominance라는 두 시그널의 조합으로 표현된다.
- Component color signal
  - Red, Green, Blue로 조합하여 표현되는 방식

### Digital Video
- Video Quality는 size와 연관되어 있다.
  - Broadcast Quality는 특정 주파수 대역을 사용하여 한정적이기 때문에 좋은 퀄리티를 내지 못한다.
  - www의 경우는 band가 여유 있기 때문에 좋더 좋은 퀄리티를 낼 수 있다.
- Transmission Time
  - 14.4k Modem, ADSL, Cable Modem
  - 지금은 광케이블을 쓴다.

### Digital Video Challenge
- Large file size
- Hardware performance
- Distribution method

### Digital Video Quality
- Screen resolution : 수평, 수직의 픽셀 갯수
- Frame rate : 30 프레임부터는 사람이 잘 끊김을 느끼지 못한다.
- Compression method : 압축 알고리즘이 퀄리티를 좌우한다.

### Compression the Video
- Intra-frame(내부 프레임) : 이미지 내의 압축
- Inter-frame(외부 프레임) : 비디오에서 연속된 프레임은 비슷할 것이기 때문에 이러한 특성에서 발생하는 중복을 줄이는 것(카메라를 움직일 땐 별도의 문제이기에 다른 방법으로 해결한다)
  - 변화가 생긴 부분만 새롭게 바꿔주고 동일한 부분은 이전 프레임꺼를 사용하는 방식으로 접근
- VBR(Variable bit rate)
  - CBR(constant bit rate) : bit rate을 고정하는 것
    - compression을 했을 때 compression rate가 바뀌기 때문에 퀄리티가 계속 바뀐다.
  - VBR : 시간에 따라서 bit rate가 계속해서 바뀌는 것
- 영상에서또한 Spatial Redundancy 와 temporal Redundancy가 존재한다.

### Prediction
- Local Motion Compensation
- Global Motion Compensation

### Modalities
- Intra frame : spatial(이미지 압축 방식과 비슷하게 이뤄진다)
- Inter frame : temporal


## MPEG
### GOP(Group Of Picture) 구조
- 프레임을 3가지 타입으로 나눈다
  - Intra-frame(I-frame)
    - I-프레임은 독립적으로 압축되는 프레임이다.
    - 다른 프레임의 참조 없이 자체적으로 복원 가능하다.
    - JPEG 압축과 유사한 방식으로 압축되며, 공간적 중복성을 제거한다.
    - GOP (Group of Pictures)의 시작 프레임으로 사용되며, 주기적으로 삽입된다.
    - I-프레임은 P-프레임과 B-프레임의 참조 프레임으로 사용된다.
    - 압축률은 상대적으로 낮지만, 화질은 가장 좋다.
    - **I-frame이 독립적인 것이기 때문에 random access point가 된다.**
  - Prediction-frame(P-frame)
    - P-프레임은 이전의 I-프레임이나 P-프레임을 참조하여 압축되는 프레임이다.
    - 참조 프레임과의 차이(잔차)를 부호화하여 압축한다.
    - 움직임 추정(Motion Estimation)과 움직임 보상(Motion Compensation)을 사용하여 프레임 간의 시간적 중복성을 제거한다.
    - P-프레임은 이후의 P-프레임이나 B-프레임의 참조 프레임으로 사용될 수 있다.
    - I-프레임보다 압축률이 높지만, 화질은 상대적으로 낮다.
  - Bidirectional-frame(B-frame)
    - Bidirectional Interpolation을 통해 reconstruct한 프레임이다.
    - B-프레임은 이전과 이후의 I-프레임이나 P-프레임을 참조하여 압축되는 프레임이다.
    - 양방향 예측을 사용하여 이전과 이후 프레임 모두에서 움직임을 추정하고 보상한다.
    - B-프레임은 다른 프레임의 참조 프레임으로 사용되지 않는다.
    - 압축률이 가장 높지만, 화질은 상대적으로 가장 낮다.
    - B-프레임의 사용으로 인해 인코딩 지연이 발생할 수 있다.
- 원리
  ![GOP](/assets/img/2024-06-06-Multi-Media-5/GOP.png)
  - I-frame을 8 \* 8 sub block으로 나눈 뒤 각 블럭의 MSE를 통해 변화량을 체크한다.
  - P-frame의 block으로 각각 매칭해봤을 때 MSE 차이가 특정 크기 이상 발생하는 부분을 남겨놓는다.
    - 비슷한 블럭은 남기지 않고 I-frame을 참조한다.
  - B-frame의 경우 I-frame과 P-frame의 차이에서 Interpolation을 통해 이미지를 생성 시켜본다.
    - 그 뒤 참조 프레임과 비교하여 생성된 이미지가 참조 프레임과 비슷하다면 기록하지 않고 버린다. (나중에 디코딩할 때 생성 시키면 되기 때문이다)
    - 하지만 생성된 이미지가 완전 다를 경우 기록해놓는다.
- 무한정 P-frame과 B-frame만 쓸 수 없기 때문에 I-frame을 중간 중간 넣는다.
- GOP의 크기에 따라 압축률과 distortion의 정도가 달라진다
  - GOP의 크기가 짧으면 압축률이 떨어진다.
  - GOP의 크기가 길면 distortion이 커진다.
- P-frame 사이의 거리 또한 중요한 입력 파라미터이다.
