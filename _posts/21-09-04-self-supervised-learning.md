---
title: Self-supervised learning
category: AI
tags: ai
article_header:
    type: overlay
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/21-09-04-self-supervised-learning-2021-09-04-16-00-10.png
---

self-supervised 는 전혀 라벨링이 되어있지 않은 데이터로 학습을 진행하는 방식이다
- Pretext task
- Contrastive Learning

<!--more-->

# Background

Labeling 작업에는 많은 노력과 비용이 요구된다. 이러한 문제들을 해결하기 위해 아래와 같은 approach로 연구가 되고 있다.

- Transfer Learning
- Domain Adaptation
- Semi-Supervised Learning
- Weakly-supervised Learning
- Self-supervised Learning

![](/assets/images/21-09-04-self-supervised-learning-2021-09-04-15-59-42.png)

- self-supervised 는 전혀 라벨링이 되어있지 않은 데이터로 학습을 진행하는 방식이다

---

# History

## Pretext task (2014 ~ 2018)

self-supervised learning에서는 사용자가 새로운 문제를 정의하는 과정이 필요하다. 이를 pretext task라고 하는데, pretext task를 학습함으로써 network 는 데이터 자체에 대한 이해를 높일 수 있게 된다.

- **pretext task 로 pre-train 된 모델을 사용자가 풀고자하는 문제인 downstream task로 transfer learning 하는 접근 방식**이 Self-supervised learning의 핵심개념이다.  
- Network로 하여금 만든 pretext task를 학습하게 하여 데이터 자체에 대한 이해를 높일 수 있게 하는 방식이다.
- 대표적인 방식으로 Exemplar, context prediction, jigsaw puzzle count, rotation 가 있다.

![](/assets/images/21-09-04-self-supervised-learning-2021-09-04-16-00-10.png)

- pretext task를 잘 정의해서 주어진 입력 이미지들에 대한 정보를 잘 추출하는 방식
- Exemplar, context prediction, jigsaw puzzle count, rotation

## Contrastive Learning (2018 ~)

- Contrastive Learning을 활용하여 주어진 입력 이미지들에 대한 정보를 추출하는 방식
- NPID, CPC, MoCo, SimCLR

## New Approach (Non-Contrastive Learning)

- Network 구조나 clustering 개념을 도입하여 주어진 입력 이미지들에 대한 정보를 추출하는 방식
- BYOL, PCL

---

# Pretext task

![](/assets/images/21-09-04-self-supervised-learning-2021-09-04-16-00-10.png)

- self-supervised learning에서는 사용자가 새로운 문제를 정의하는 과정이 필요하다. 이를 pretext task라고 한다. pretext task를 학습함으로써 network 는 데이터 자체에 대한 이해를 높일 수 있게 된다. 이렇게 pre-train 된 모델을 사용자가 풀고자하는 문제인 downstream task로 transfer learning 하는 접근 방식이 self-supervised learning의 핵심 개념이다.

# Contrastive Learning

Contrastive Learning은 Positive pair와 Negative Pair로 구성된다. Positive pair 끼리는 거리를 좁히고, Negative pair 끼리는 거리를 멀리 띄워놓는 것이 학습 원리이다.

- 같은 image에 서로 다른 augmentation을 가한 뒤, 두 positive pair의 feature representation은 거리가 가까워지도록 학습을 하고
- 다른 image에 서로 다른 augmentation을 가한 뒤, 두 negative pair의 feature representation 은 거리가 멀어지도록 학습을 한다.

![](/assets/images/21-09-04-self-supervised-learning-2021-09-04-16-01-14.png)

위의 예시를 봤을 때, 강아지와 의자를 각각 augmentation 취한 후,

동일한 image로 augmentation 된 것 끼리는 각각 positive pair로 학습시키고, 

다른 image의 augmentation된 것과는 negative pair로 학습시킨다.

## NPID

![](/assets/images/21-09-04-self-supervised-learning-2021-09-04-16-01-36.png)

- 2020년 11월 20일, CVPR

### **Motivation**

모델이 따로 instance간의 구분을 학습하지 않아도, 잘 추출된 특징값은 instances 간의 유사도 정보를 가지고 있을거다.

### Framework

- input image를 128차원의 feature vector로 변환하고,  vector space에서 classification을 처리
- pretext task가 감당할 수 없었던 연산문제 또한 해결
- memory bank 사용

## Moco

- positive sample과 negative sample을 많이 확보해야하는데,

## PIRL

### Motivation

어떤 pretext task를 선정하는지 상관없이, 같은 이미지이면 transformation후에도 visual semantic을 변화시키지 않을거다.

즉, origin image 와 transformation image 를 representation 해서 feature를 뽑아, 그 feature 간의 거리를 가깝게 하는 방식으로 접근

### Framework

![](/assets/images/21-09-04-self-supervised-learning-2021-09-04-16-01-50.png)

- 같은 이미지에서 나온 presentation은 transformation과 무관하게 유사해야하고, 다른 이미지와 겹치지 않아야 함
- Cosine similarity를 사용

---

# Reference

- [고려대학교 DMQA 연구실](http://dmqm.korea.ac.kr/activity/seminar/284)
- [[CV] Self-supervised learning(자기주도학습)과 Contrastive learning - 스스로 학습하는 알고리즘](https://daeun-computer-uneasy.tistory.com/37)
- [https://www.youtube.com/watch?v=5BCQ7T2Rw1w&list=PLpIPLT0Pf7IoTAvBJ6FX1vAi-PIeSw9xK&index=31](https://www.youtube.com/watch?v=5BCQ7T2Rw1w&list=PLpIPLT0Pf7IoTAvBJ6FX1vAi-PIeSw9xK&index=31)
- [Unsupervised Visual Representation Learning Overview (Self-Supervision)](https://seongkyun.github.io/study/2019/11/29/unsupervised/)
- [Pretex task 리뷰에 대한 세미나 자료](http://dmqm.korea.ac.kr/activity/seminar/284)