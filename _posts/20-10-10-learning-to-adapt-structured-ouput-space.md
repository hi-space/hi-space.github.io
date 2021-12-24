---
title: Learning to Adapt Structured Output Space for Semantic Segmentation (AdaptSegNet)
category: AI
tags: ai paper
article_header:
    type: overlay # 포스트 내부에서 헤더 오버레이 적용여부
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/20-10-10-learning-to-adapt-structured-ouput-space-2021-09-04-18-49-09.png
---

- CVPR 2018
- [Paper](https://arxiv.org/pdf/1802.10349.pdf)
- [Github](https://github.com/wasidennis/AdaptSegNet)
- semantic segmentation에서 domain adaptation을 위한 adversarial learning을 적용

<!--more-->

# Introduction

Semantic segmentation은 pixel-level의 GT에 의존하지만 보이지 않는 image domain에는 잘 일반화 되지 않을 수 있다. 본 논문에서는 semantic segmentation에서 domain adaptation을 위한 adversarial learning 방법을 제안한다.

- **adversarial learning** : target의 distribution이 source와 유사하도록 학습을 진행. source 쪽에서 학습한 feature들을 target domain condition으로 옮겨주겠다 라는 의미.
  
- **Generative network** : target image를 source와 유사하게 만들어서 학습을 시키자. 혹은 source의 style을 target에 맞게 재생산해서 학습시키자
  
- **Self-Training** : unlabeled data를 위해 pseudo-labels를 생성해서 학습하는 방법. 스스로 필요한 GT를 나름대로 만들어서 그걸 기반으로 학습한다는 의

---

# Methods

![](/assets/images/20-10-10-learning-to-adapt-structured-ouput-space-2021-09-04-18-49-09.png)

target domain에서 annotation을 사용할 수 없는 경우, source domain과 target domain 간의 gap을 좁히기 위해 domain adaptation 기술이 제안되었다.

Image Classification 문제에서는 두 도메인의 feature들을 align 시켜서 두개의 도메인에 대해 generalize 되도록 했다. Semantic segmentation 에서도 비슷하게 feature space 내에서 adversarial learning 하려는 노력들이 있었다.

하지만 semantic segmentation 에서는 image classification에서와 달리 appearance, shape, context 들을 포함하여 다양한 visual 신호들을 encoding 해야하기 때문에 high-dimensional 복잡성 문제를 겪을 수 있다. 그래서 본 논문에서는 feature adaptation을 하는 대신 adapting pixel-level prediction task를 풀려고 한다.

semantic segmentation에서는 공간적으로나 local 적으로나 다양한 정보들이 포함되어 있다. 두 도메인의 이미지 appearance가 다르더라도 segmentation output의 공간 레이아웃, local context 와 같이 상당히 유사한 부분이 많다. (domain이 다를 때 appearance는 gap이 크지만, 공간 layout, context 등의 구조화된 특성은 유사하다)

아이디어는 단순하다. Source, Target domain에서 예측된 label 의 distribution을 서로 유사하게 만드는 것이다. GAN을 기반으로 아래와 같이 두가지 부분으로 구성된다.

1. output result를 예측하기 위한 segmentation model
2. input이 source segmentation model에서 나온 건지 target segmentation model에서 나온 건지 구별하기 위한 discriminator

adversarial loss와 함께, segmentation model이 source와 target 의 output space에 유사한 distribution을 만들기 위한 discriminator 만드는 것이다.

![](/assets/images/20-10-10-learning-to-adapt-structured-ouput-space-2021-09-04-18-50-11.png)

- Source Domain 이미지와 Target Domain 이미지를 같은 Segmentation model에 넣는다. 
- Source prediction 에서는 source의 GT에 기반하여 segmentation loss를 계산한다.
- Discriminator Network 에서는 target 을 source와 비슷하게 만들기 위해서 input이 source 인지 target 인지 판별하는 discriminator를 사용한다. 그러면 target prediction에 대한 adversarial loss가 계산되고, segmentation network로 back-prop 된다. 
- 이걸 하나의 adaptation module 이라고 하면, 이것을 multi-level로 구성하여 사용했다.

---

# Expermients

![](/assets/images/20-10-10-learning-to-adapt-structured-ouput-space-2021-09-04-18-50-38.png)

GTA 데이터에서 Cityscapes 데이터로 adapted 시킨 결과물이다. feature adaptation 시킨 것보다 segmentation을 adaptation 한 본 연구의 결과물이 좋을 것 확인할 수 있다.

![](/assets/images/20-10-10-learning-to-adapt-structured-ouput-space-2021-09-04-18-50-57.png)

---

## Reference

- [[CVPR'18] Learning to Adapt Structured Output Space for Semantic Segmentation](https://hyungukchoi.blogspot.com/2019/12/cvpr18-learning-to-adapt-structured.html)

- [신경망을 의도적으로 속이는 방법](https://medium.com/@jongdae.lim/%EA%B8%B0%EA%B3%84-%ED%95%99%EC%8A%B5-machine-learning-%EB%A8%B8%EC%8B%A0-%EB%9F%AC%EB%8B%9D-%EC%9D%80-%EC%A6%90%EA%B2%81%EB%8B%A4-part-8-d9507cf20352)

- [AdaptSeg & ADVENT teardown reports](https://junha1125.github.io/blog/pytorch-docker-git/2021-06-11-AdapSegAdvent/)

- [Reproduce Github](https://github.com/seominseok0429/domain-adaptation-for-segmentation-toolbox)