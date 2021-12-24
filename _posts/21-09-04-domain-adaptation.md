---
title: Domain Adaptaion
category: AI
tags: ai
article_header:
    type: overlay # 포스트 내부에서 헤더 오버레이 적용여부
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/21-09-04-domain-adaptation-domain-gap.png
---

<!--more-->

# Domain Adaptation

![](/assets/images/21-09-04-domain-adaptation-domain-gap.png)

# Unsupervised Doamin Adaptation

UDA는 semi-supervised learning의 변형으로, unlabeld 데이터를 사용하기 위해 다른 distribution의 labled dataset 을 사용하는 방법이다. real 이미지의 labeling 에 비해 더 쉽게 labeled 데이터를 받아올 수 있도록 synthetic 데이터를 사용하곤 한다.

- 레이블이 지정된 소스 도메인에서 모델을 학습하는 것을 목표로 하는 전이 학습의 특수 형태
- 제로샷 학습, 퓨얼샷 학습, 자기 지도 학습과 함께 샘플 효율적인 학습
- Target 데이터의 GT 데이터를 필요로 하지 않지만, 일반적으로 모델을 훈련시키기 위해 충분한 수의 Unlabeled Target 데이터가 필요하다
- 다른 task 에서 배운 지식을 사용해 정답 label이 없는 새로운 상황에서도 모델을 성능을 높이는 것을 목적으로 한다
- source domain과 target domain 간의 데이터 분포가 다르다고 가정하면, domain adaptation은 주로 source 및 target domain에서 공통 특징을 학습한다

# Related Work

GAN을 이용해 input-level에서 source 와 target domain 간의 gap을 줄이는 방법이 있다.(like style-transfer) 그리고 Discriminator를 사용해 source와 target 데이터 간의 confusion 을 유도할 수도 있다. 

Pseudo labeling 은 offline 이라는 label을 미리 계산하고 이후에 모델을 업데이트 하는데 사용한다. 여러 라운드 동안 이 프로세스를 반복하는 형식이다. 이 방법은 adversarial training, style transfer 모델 등에 의존적이고, labeling 시에 불안정할 수 있기 때문에 추가 정규화가 필요하다. 이에 따라 entropy minimization을 추가 적용하거나 dorpout 기반 샘플링을 하기도 한다.

Semantic segmentation은 classification 문제와 달리 spatial 한 정보를 사용해야 한다. 

## Discrepancy-based Methods

![](/assets/images/21-09-04-domain-adaptation-discrepancy-methods.png)

source doamin과 target domain의 discrepancy를 명시적으로 측정하는 방식

- 다른 방법론에 비해 이론적으로 설명 가능
- 전체 네트워크 아키텍처가 간단
- 복잡한 데이터셋에는 적용 어려움
- Image-level representation 에서 사용

## Adversarial Discriminative Models

![](/assets/images/21-09-04-domain-adaptation-adversarial-discrimintavie.png)

Domain confusion을 위해 discriminator와 adversarial loss를 사용

- DA 문제를 해결하기 위해 가장 널리 사용되는 방법
- 작은 데이터셋에서 잘 동작하지 않고 최적화하기 상대적으로 어려움 
- 데이터 확장성이 좋아서 고차원 task 에 적용 가능 

## Adversarial Generative Models

![](/assets/images/21-09-04-domain-adaptation-adversarial-generative.png)

표면적인 gap도 줄이기 위해 Adversarial Discriminator Model에 generative model 결합

- Source domain과 target domain 간의 시각적 격차를 줄임
- Source, target domain이 균일한 형태의 pattern 으로 표현되어야 함
- 대규모 데이터셋이 필요하고 많은 컴퓨팅 리소스와 최적화 프로세스 필요


## Self-supervised-based Methods

![](/assets/images/21-09-04-domain-adaptation-self-supervised.png)

Task network 에 auxiliary task를 통합해 함께 학습

- Downstream task에 대한 가정이 없음
- 데이터 확장성이 좋아서 복잡한 task에 적용 가능
- 일반적으로 Discrepancy-based Methods 보다 성능이 좋고
- Adversarial Discriminative & Generative Model 보다는 성능이 떨어지는 편