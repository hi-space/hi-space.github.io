---
title: Single-Source Unsupervised Visual Domain Adaptation 정리
category: AI
tags: ai paper
article_header:
    background_image: false
cover: /assets/images/21-09-04-source-free-domain-adaptation-duda-pipeline.png
---

A Review of Single-Source Deep Unsupervised Visual Domain Adaptation 정리
  
<!--more-->
  
- 2020
- [Paper](https://arxiv.org/pdf/2009.00155.pdf)
- [Related Post](https://hi-space.github.io/ai/2021/09/04/source-free-domain-adaptation-eng.html)

# Abstract

대량의 데이터셋을 이용해 Neural network를 훈련시키는 방식은 vision task에서 좋은 성과를 거둬왔다. 하지만 많은 응용 프로그램에서 레이블이 있는 데이터를 대량으로 얻는 것은 많은 비용과 시간이 소요된다. label이 없는 데이터에 대해 training 하기 위해서, 이미 label이 있는 데이터로 학습되어 있는 네트워크를 사용해 target domain으로 adaptation 하는 방법들이 연구되고 있다. 하지만 안타깝게도 도메인간 직접적으로 transfer 하는 방식은 domain shift나 dataset bias 때문에 잘 동작하지 않는다. 
Domain adaptation은 source domain의 모델이 target domain으로 변경되더라도 잘 동작하돌록 모델을 학습시키는 패러다임이다. 이 때 target domain은 source domain과 완전히 별개의 domain 이 아닌, 어느정도 연관이 되어 있는 다른 domain을 가리킨다.

이 논문에서는 vision task에 중점을 맞춰 single-source deep unsupervised doamin adaptation 의 최신 연구와 새로운 관점에 대해 논의한다.
- 다양한 domain adaption 전략의 정의
- 기존 벤치마크 데이터셋 설명
- domain adaptation 전략 비교
  - discrepancy-based method
  - adversarial discriminative methods
  - adversarial generative methods
  - self supervision-based methods
- 향후 연구 방향과 해결책 제시

# 1. Introduction

가장 naive 하게 생각해볼 수 있는 방법은 labeld 된 대량의 데이터로 학습된 모델을 target domain 의 데이터에 적용하는 것이다. 즉, direct로 transfer 하는 방식이다. 하지만 이는 domain shift와 dataset bias 로 인해 성능이 좋지 않아서 사용되지 않는다. pre-trained된 source model을 target domain으로 fine-tuning 하는 방식이 있을 수 있겠지만, target domain에 맞게 transfer 하기 위해서는 많은 학습 데이터로 재학습을 해야한다. 

Domain Adaptation(domain transfer)는 labeled source domain에서 모델을 학습해서 unlabeled target domain 에서 사용할 수 있게 하는   transfer learning 의 특수한 형태이다.
sample-efficient learning 의 유형이라고 할 수도 있다. (zero-shot learning, few-shot learning, self-supervised learning 등이 sample-efficient learning의 예시)

### UDA(Unsupervised Domain Adaptation)

UDA(Unsupervised Domain Adaptation)는 target domain의 label을 필요로 하지 않지만, 일반적으로 학습을 위해서는 충분한 unlabeld target sample 이 필요하다. 

### Self-supervised learning(SSL)

Self-supervised learning(SSL)은 사람이 제공한 label을 사용하지 않고 input data의 고유한 패턴과 속성을 인지하는 학습 패러다임이다. 데이터 자체만을 기반으로 auxiliary tasks를 구성하고 이 task를 잘 수행하도록 만들어 network가 의미있는 representation 을 뽑아낼 수 있도록 학습한다.

SSL은 auxiliary task과 loss function을 정하는 것이 중요한 요소이다.
- auxiliary task: model 이 입력 데이터의 reprresentation을 학습하도록 설계한다.
- loss function: model prediction과  fixed target 간의 차이, sample 의 유사성 (contrastive loss)나 확률 분포 간의 차이 (adversarial loss)를 측정하도록 정의한다.
  
SSL은 보통 다른 domain 으로의 transfer를 구체적으로 다루진 않는다.

### Single-source Domain Adaptation

Single-source Unsupervised Domain Adaptation의 주요 과제는 domain shift (source domain과 target domain간의 distribution 차이)이다.domain shift 에는 covariate shift, label shift, concept drift 가 있을 수 있다.

![image-level object classification과 pixel-wise semantic segmentation 에 대한 domain shift](/assets/images/21-09-04-source-free-domain-adaptation-domain%20shift.png)

> image-level object classification과 pixel-wise semantic segmentation 에 대한 domain shift 예시이다.  
> labeled source domain으로 training 된 모델을 unlabeled target domain 에 적용하면 성능의 매우 악화되는 것을 볼 수 있다.

#### Object classification

- Clipart 도메인에서 Resnet-50 모델을 학습하면 96%의 accuracy를 얻을 수 있다.
- Art 도메인에서 모델을 학습시키고 Clipart 도메인에서 테스트하면 정확도가 34.9%로 크게 떨어진다.

#### Semantic segmentation

- CARLA2, GTA-V3, Autoware.AI4는 자율주행 연구에 널리 사용되는 세가지 시뮬레이터이다. 시뮬레이션을 사실적으로 만들기 위한 노력은 계속되고 있으나, 실제 데이터의 모든 특성을 모델링 하는 것을 어렵다
- Cityspcaes 도메인에서 FCN 모델을 학습시키면 62.6% mIoU를 얻을 수 있다
- Cityscpaes 로 학습한 모델을 GTA domain에 적용하면 21.7% mIoU 로 크게 떨어진다.

##### **Semantic segmentation Datasets**

> **Cityscapes**
> - 차량 시점에서 독일과 주변국의 50개 도시들을 돌면서 얻어진 이미지 데이터셋
> - 5,000개의 pixel-wise semantic labels & images
>   - training set: 2,975
>   - validation set: 500 image
>   - test set: 1,595

> **GTA**
> - 사실적으로 렌더링된 GTA 게임에서 얻어진 데이터셋
> - 24,966개의 pixel-wise semantic labels & images
> - Cityscpaes와 동일하게 맞춘 19개 클래스

> **SYNTHIA (SYNTHIA-RANDCITYSCAPES)**
> - 도로 환경에서의 synthetic dataset Cityscapes와 pair 를 맞춤
> - 9,400개의 pixel-wise semantic labels & images
> - 16개의 클래스, one void class, some unnamed classes

초기 UDA는 deep learning 을 사용하지 않고 source domain과 target domain의 feature distribution을 맞추는 형식이였다. (sample re-weighting, intermediate subspace transformation)
이후에는 end-to-end learning domain-invariant features 방식으로 바뀌었다.  
일반적으로 source domain과 target domain에 대한 모델을 각각 represent 하기 위해서 두개의 stream이 결합된 아키텍처가 사용된다.
일반적으로 clasification 에서 사용하는 task loss (cross entropy loss emd) 외에도, domain shift를 함께 다루기 위해 discrepancy loss, adversarial loss, self-supervision loss 등을 함께 결합하여 사용한다. 

## DUDA Pipelines

![DUDA pipelines](/assets/images/21-09-04-source-free-domain-adaptation-duda-pipeline.png)

> Single-source Unsupervised Domain Adaptation 파이프라인에서 널리 사용되는 framework 유형들  
> 이 기본 구조들을 약간 변경하거나 다른 파이프라인과 결합하여 사용한다

### Discrepancy-based Methods

![](/assets/images/21-09-04-source-free-domain-adaptation-discrepancy-based-methods-table.png)

Network stream의 activation layer 에서 souce domain과 target domain의 discrepancy를 명시적으로 측정하는 방식이다.

source 와 target domain distribution 사이를 측정하는 것을 정의하고, 이 정의를 기반으로 target risk 값을 최소화 하는 방향으로 알고리즘을 설계한다.

##### Pros
- 다른 방법론에 비해 이론적인 guarantee가 있다.
- backbone 네트워크에 큰 block을 추가하지 않기 때문에 전체 네트워크 아키텍처는 복잡하지 않다.
- 계산 효율성이 다른 방법론에 비해 높으며 많은 양의 데이터셋이 필요하지 않다. 
- 네트워크가 그다지 복잡하지 않기 때문에 훈련하기 쉽고 hyperparameter 조정이 덜 필요하다.

##### Cons
- 다양하고 복잡한 데이터셋에는 적용하기 어렵고 성능도 좋지 않다.
- pixel-level 을 판단하는 복잡한 task 보다는 image-level representation 에 사용한다.

### Adversarial Discriminative Models

![](/assets/images/21-09-04-source-free-domain-adaptation-adversarial-discriminative-model-table.png)

domain confusion을 조장하기 위해 domain discriminator와 adversarial objective 를 사용한다.
DA 문제를 해결하기 위해 가장 널리 사용되는 방법이다. 

- 데이터 확장성이 좋아서 다양한 종류의 데이터셋에서 잘 작동한다
- Discriminator와 feature extractor 간의 min-max game이기 때문에 작은 데이터셋에서는 항상 잘 작동하지는 않고, 최적화 하기도 상대적으로 어렵다
- 시각적으로 source domain과 target domain 간의 시각적 격차를 줄이기 위해 주로 GAN 과 같은 generative model 를 사용하기 때문에 이론적 gurantee가 없다. 
- semantic segmentation, object detection 과 같은 고차원 task 에서에서 잘 수행된다
- generative model에 대한 의존도 때문에 source, target domain의 균일한 형태의 시각적 pattern이 만들어져야 한다.
- 더 복잡한 데이터셋으로 쉽게 확장할 수 없다.
- generative model을 강력하게 훈련하기 위해 대규모 데이터셋이 필요하다. 그렇기 때문에 더 많은 컴퓨팅 리소스와 복잡한 최적화 프로세스가 필요하다
- Discrepancy-based methods와 마찬가지로 source, target domain 간의 feature distribution을 align 시키려는 방법이다. 

데이터 확장성 측면에서 다양한 종류의 데이터 세트에서 잘 작동합니다. 판별기와 특징 추출기 간의 최소-최대 게임 수렴에 의존하기 때문에 작은 데이터 세트에서 항상 잘 작동하는 것은 아니며 최적화하기도 상대적으로 어렵습니다.

### Adversarial Generative Models

![](/assets/images/21-09-04-source-free-domain-adaptation-adversarial-generarive-model-table.png)

domain discriminative model에 generative component(GAN 기반)를 결합한 구조이다. 
> GAN: generative adversarial nets (include a generator, discriminator)

generator는 임의의 노이즈 z를 입력으로 사용하여 가상 이미지를 생성하는 반면, discriminator는 generator 및 실제 이미지 x의 출력을 입력으로 사용하여 이미지가 실제인지 생성되었는지 여부를 분류한다. 
discriminator는 실제 이미지와 생성된 이미지를 올바르게 분류할 확률을 최대화 하기 위해 학습하고,
동시에 generator는 discriminator가 실수할 확률을 최대화하기 위해 이미지를 생성하도록 학습한다.

### Self-supervised-based Methods

![](/assets/images/21-09-04-source-free-domain-adaptation-self-supervised-methods-table.png)

original task network 에 input 데이터의 representation을 위한 auxiliary self-supervised learning task를 통합하는 형태이다. task model과 함께 self-supervised learning 을 학습시키면 source 와 target domain을 더 가깝게 만다는데에 도움이 된다.

- downstream task에 대한 가정이 없으며, 복잡한 작업에 적용할 수 있다
- 데이터 확장성 측면 좋다. 복잡한 데이터셋에도 적용할 수 있다
- downstream task와 함께 학습시킬 수 있는 간단한 작업이다
- 일반적으로 Discrepancy-based Methods 보다 성능이 좋지만 Adversarial Discriminative & Generative Models 보다는 성능이 떨어지는 편이다
- 

### Others

최근에 의사 라벨링은 여러 DA 방법에서 악용되었습니다. Panet al. [69] 및 Hu et al. [64] 대상 도메인의 이미지에 의사 레이블을 할당한 다음 프로토타입을 기반으로 도메인 정렬을 수행했습니다. 이러한 방법은 주로 이미지 분류에 사용됩니다. Zou et al.
[95]는 의사 레이블이 높은 신뢰도의 예측에서 생성되는 의미론적 분할을 위한 자체 훈련에서 의사 레이블을 사용하는 것을 제안했습니다. 그러나 의사 레이블은 노이즈가 많기 때문에 잘못된 클래스에 대해 지나치게 확신하는 레이블 믿음이 진행되어 오류가 전파될 수 있습니다. 이 문제를 해결하기 위해 Zou et al. [96]은 의사 레이블이 교대 최적화를 통해 공동으로 최적화된 연속 잠재 변수로 처리되는 신뢰 정규화된 자체 훈련 프레임워크를 제안했습니다. 레이블 정규화와 모델 정규화는 두 가지 유형의 신뢰 정규화로 제안됩니다.

- Zhang et al. [101]은 의미론적 분할에서 도메인 갭을 최소화하기 위해 커리큘럼 스타일 학습 접근 방식을 제안했습니다. 이 방법은 먼저 대상 도메인에 대한 몇 가지 필요한 속성을 추론하기 위해 쉬운 작업을 해결한 다음 교육 프로세스 중에 이러한 추론된 속성을 따르도록 대상 도메인의 네트워크 예측을 적용합니다.

![](/assets/images/21-09-04-source-free-domain-adaptation-duda-framework-comparision.png)

## Qualitaive Comparision

- Theory Guarantee: 알고리즘에 의해 상한이 최소화 될 수 있는지 여부
- Efficiency: 알고리즘의 학습 및 추론의 계산 비용
- Task scalability: 알고리즘이 semantic segmentation, object detection 과 같은 복잡한 작업에 적용 가능한지 여부
- Data scalability: 크고 복잡한 데이터셋에 적용 가능한지 여부
- Data dependency: 작은 데이터셋으로 잘 훈련될 수 있는지 여부
- Optimizability: 알고리즘이 학습하기 쉽고 hyperparameter 조정이 덜 필요한 경우
- Performance: 알고리즘이 얼마나 잘 수행되는지

### Semantic Segmentation Evaluation 방법

- GTA 또는 SYNTHIA -> Cityscapes
- mIoU가 평가지표로 사용된다
- SYNTHIA는 다양한 관점에서의 이미지가 포함되어 있기 때문에 GTA 보다는 Cityscpaes 와 더 큰 domain gap을 가지고 있다.
