---
title: Synthetic data
category: AI
tags: ai
---

## Intro

* Google, Apple, Amazon과 같은 대기업은 제품/서비스를 통해 무제한의 다양한 데이터들을 얻어낼 수 있지만, 그 외의 기업들은 데이터에 대한 액세스가 제한적이거나 비싸다.
* Synthetic Data : 실제 event 에 의해 생성되는 것이 아니라 인위적으로 제조된 정보. 시각적 데이터 뿐만 아니라 음성, 센서 (LiDAR, Radar, GPS 등) 의 정보들도 존재한다.
* 데이터는 ML의 cold start 문제로 남아있다. 현재 데이터를 캡처하는 기업은 데이터를 직접 labeling 하고 있지만 느리고 비용이 많이 들고, 품질이 떨어질 수 있다. Synthetic Data는 이러한 제약을 우회하여 데이터를 demonstrate 하는데 도움이 된다.
* Synthetic Data는 민감한 데이터들을 포함하지 않고 작업을 할 수 있도록 해주기도 한다.

![](/assets/images/20-05-18-synthetic-data-2021-11-02-15-27-57.png)

* Synthetic Data는 programming 방식으로 생성되는 데이터로, 게임 엔진, 음성 합성 모델로 생성된 오디오 등이 될 수 있다. 기존의 augmentation (crops, flips, rotations, distortions) 과 다르게 다양한 데이터 모델을 생성할 수 있다. 
* 임의의 수의 이미지 데이터를 생성할 뿐만 아니라 annotation 도 손쉽게 생성할 수 있다. (Bounding boxes, segmentation masks, Depth maps, 그 외의 다양한 meta data)

<!--more-->

## Benefit

* 데이터 생성 및 캡처에 대한 의존도 감소
* 수작업 labeling 보다 저렴하고 빠름
* 실제 캡쳐하기 어려운 데이터를 생성할 수 있음 (ex, 수중, 군사 분쟁 지역 시각 컨텐츠 등)
* Edge case 를 만들어 training에 중요한 데이터를 생성할 수 있음
* 많은 양의 데이터 생성
* 완벽하게 annotated 된 데이터 제공
* 더 빠르게 labeling 반복 지원
* 개인 정보 보호 문제 감소

realistic 한 visual data는 artist가 제작하며, 현실과 최대한 비슷하게 보이도록 제작한다. realistic 한 데이터를 만드는 프로세스는 programming 하는 것 보다 시간이 오래걸린다.

Unreal, Unity, Blender와 같은 게임 엔진을 사용해서 프로그래밍 방식의 synthetic data를 만들 수 있다. 그리고 GAN \(Generative Adversarial Network\) 또는 Domain Randomization을 사용한 Domain Adaptation 과 같은 기술을 사용하여 데이터의 permutation을 높일 수 있다.

* Synthetic Data는 초기 AI 모델을 training 시키는 속도를 높일 때 사용할 수 있다.
* 원하는 형태의 synthetic data를 만들어서 Testing 알고리즘으로 사용할 수 있다. 원하는 결과에 대한 데이터를 원칙에 따라 특정 알고리즘 조합으로 만들 수 있기 때문에 이를 통해 검증에 대한 시간, 비용을 절약할 수 있다.
* Requirements에 맞게 labeled 된 large scale 데이터를 보다 적은 코스트로 생성할 수 있다. 또한 반복 테스트를 통해 데이터를 수정 및 개선하여, 필요한 데이터에 맞게 조금씩 수정해나가며 데이터를 수집할 수 있다.
* Training 데이터에 특정 label이 너무 많은 경우 Overfitting 이 발생할 수 있다. Synthetic data는 이러한 경우를 해결하기 위해 균형잡힌 데이터셋을 만들어 제공할 수 있다.
* 실제 얻기 어려운 희귀한 데이터의 상황을 연출하여 해당 데이터를 얻을 수 있다. 

## Type

* Fully Synthetic : original data를 하나도 포함하지 않는다. 
* Partially Synthetic : sensitive 한 데이터들은 synthetic data로 대체할 수 있다. 

## Technique

* **Domain Adaptation** : label이 있는 데이터셋 \(source\)를 사용하여 unlabeled 데이터셋 \(target\)을 classify 하는 작업. low quality의 synthetic data와 실제 데이터를 사용하여 synthetic data 자체를 더 개선시킬 수 있다.

![](/assets/images/20-05-18-synthetic-data-domain-randomization.png)

* **Domain randomization** : 이것 또한 reality gap을 줄여주는데 도움이 된다. 네트워크가 이미지의 필수 기능에 집중하는 법을 배우도록, 비사실적인 방식으로 환경을 교란시킴으로써 photo realism을 포기한다. image scene, lighting position, intensity, texture, scale, position 등을 바꿈으로써 다양한 환경을 만들 수 있다. 하나의 simulation 데이터셋에서 모델을 학습시키는 대신 시뮬레이터를 무작위화하여 모델을 다양한 데이터에 노출시킨다. 진입률이 낮기 때문에 인기있는 기술이다. 수동으로 randomize 하는 대신 programming 방식으로 synthetic data를 생성할 수 있기 때문에 시간을 더욱 단축시킬 수 있다.

> domain randomization intentionally abandons photorealism by randomly perturbing the environment in non-photorealistic ways to force the network to learn to focus on the essential features of the image
>
> ㅡ Training Deep Networks with Synthetic Data: Bridging the Reality Gap by Domain Randomization

* Synthetic Data와 Real Data를 training에 활용한다면, 80% - 90% 정도는 synthetic data를 사용하고 10% - 20% 는 real data를 사용한다.
* Research 들은 training data의 100%를 Synthetic data로 활용하여 real data로 training 시키는 것과 동일한 수준의 정확도로 모델을 생성할 수 있는 기술을 연구 중이다. 
* Cross-domain applications 는 synthetic data 의 활용도가 높은 곳이다. 예를 들어 자율주행차량 모델을 개발할 때 San Francisco의 데이터로만 훈련시킨 후 Tokyo 에서 주행을 할 때, Tokyo에 대한 데이터가 아예 없는 것보다 Synthetic data를 섞어서 training 시킨 모델이 훨신 나을 것이다.

## Problem

* 필요한 feature 들에 대해서 replicate 해야한다. 이는 어려울 수 있다.
* 사용자가 눈으로 봤을 때는 알 수 없는 data의 hidden effect가 있을 수 있다. 이는 성능에 영향을 미칠 수 있다.
* 특정 물체 식별과 같은 간단한 문제에는 나쁘지 않지만 다양한 형태의 물체 분류와 같은 복잡한 task는 어려울 수 있다.
* 가장 주요한 문제는 정확도. synthetic data의 통계적 속성이 원본 데이터의 속성과 일치하는지 확인해야 한다.
* Synthetic data는 Reality Gap 문제가 있다. Domain 내에서 synthetic data로 training 시킨 경우, real 한 data로 training 시킨 경우보다 동일하거나 더 나은 성능을 내는 경우는 거의 없다. Domain 내의 synthetic data는 중력, 관성과 같은 physics를 포함해야하기 때문에 문제가 될 수 있다. physics를 완벽하게 모델링 하기는 쉽지 않지만 게임 엔진은 발전하고 있다. 
* OpenAI의 경우 Domain Randomization을 사용하여 객체를 합성하는 데이터 생성 파이프라인을 구축했다. (Domain Randomization and Generative Models for Robotic Grasping)

![](/assets/images/20-05-18-synthetic-data-2021-11-02-15-28-23.png)

* 서로 다른 유형의 synthetic data를 혼합해도 긍정적인 효과가 있을 수 있다. Domain randomized 된 데이터와 photo realistic한 데이터를 혼합하여 training 한 모델이 real한 데이터와 synthetic data를 섞어서 training 한 모델과 비슷하다는 연구도 있다. domain randomization이 real data와 경쟁하기 위해서는 real data를 통한 fine-tuning 이 필요하다. (Deep Object Pose Estimation for Semantic Robotic Grasping of Household Objects)
* 하지만 100% synthetic data로만 training 시켜서 성공시킨 사례가 아직 없다. 

## Case

* Synthetic data 사용 사례는 광범위하다. Computer vision application의 경우 자율주행 (AV, 로봇, 드론), agtech, 부동산, 비디오 감, CPG, 리테일, defense 등.
