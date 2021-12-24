---
title: Self training with Noisy Student improves ImageNet classification
category: AI
tags: ai paper
article_header:
    type: overlay # 포스트 내부에서 헤더 오버레이 적용여부
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/20-10-10-self-training-with-noisy-student-framework.png
---

- CVPR 2020
- [Paper](https://arxiv.org/pdf/1911.04252.pdf)
- [Github](https://github.com/google-research/noisystudent)
- SOTA 모델에 unlabeled image를 활용하여 성능을 향상시키는 학습 방법

<!--more-->

# Introduction

labeled 데이터로 pretraining 한 다른 방법들과 달리 unlabeled 데이터와 ImageNet 데이터만으로 SOTA 1위를 기록

# Methods

Teacher-student 기반의 Self-training 프레임워크로 구성되어 있다.

![](/assets/images/20-10-10-self-training-with-noisy-student-framework.png)

1. labeled image로 teacher model을 학습
2. 학습한 teacher model를 사용해 많은 unlabeled image에 pseudo label을 생성
   - 높은 auccuracy 로 labeling 하기 위해 Noise를 사용하지 않음
3. labeled image와 pseudo labeled image를 결합하고 noisy를 추가하여 student model을 학습
   - Noisy: dropout, stochastic depth, data augmentation
   - Noisy Studnet model은 teacher model 보다 크다
4. 학습된 student model을 teacher로 사용하여 pseudo label을 다시 생성 (step 2 부터 반복)

Noisy를 추가함으로써 regularization 효과를 주고 다른 Input 들에 대해 robust 하게 학습한다. 

![](/assets/images/20-10-10-self-training-with-noisy-student-pseudo.png)

## Reference

[갈아먹는 Image Classification [1] Noisy Student](https://yeomko.tistory.com/42)

[[논문 읽기] Noisy Student(2020) 리뷰, Self-training with Noisy Student improves ImageNet classification](https://deep-learning-study.tistory.com/554)
