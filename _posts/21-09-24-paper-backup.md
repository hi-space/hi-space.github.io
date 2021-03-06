---
title: 💡 Paper Backup
tags: paper 💡
mathjax_autoNumber: false
---

Self-supervised learning with image based synthetic teacher model   
(이미지 기반의 가상 교사 모델을 이용한 자기 지도 학습)

<!--more-->

- 감사의 글
- 목차
- 그림 목차
- 표 목차

# 국문 요약 / 영문 요약

> 2쪽 이내

본 논문에서는 Unsupervised Domain Adaptation과 Semi-supervised learning을 결합하여 domain 학습 데이터에 대한 의존성을 감소시키는 동시에 task loss를 최소화 할 수 있는 방안에 대해 연구하고자 한다.

- 핵심어: Unsupervised Domain Adaptation, Semi-supervised learning, Self-training, Pseudo-Labeling, Synthetic data

# 1. 연구 배경

ImageNet의 데이터 공개를 시작으로 Data-driven 기반의 Supervised learning이 여러 visual task 분야에서 좋은 성과를 거뒀다. 검증된 좋은 모델들이 이미 존재하기 때문에, 특정 문제에 맞는 AI모델을 만들기 위해서는 그에 맞는 양질의 데이터를 얼마나 많이 확보하느냐가 결국 모델의 성능을 좌우하게 되었다.

하지만 특정 domain의 task에 맞는 데이터를 확보하고, 그 데이터에 labeling 하는 것은 많은 시간과 비용을 필요로 한다. 이러한 데이터 의존적인 문제들을 해결하기 위해 데이터가 부족하더라도 효과적인 학습을 할 수 있는 다양한 연구들이 진행되고 있다. 유사한 task에서 학습된 pretrain 모델을 가져와 target task에 적용하는 Transfer Learning, Domain Adaptation, 그리고 labeled data가 적거나 없을 때 사용하는 Semi-Supervised Learning, Self-Supervised Learning 등의 연구들이 있다.

![자율주행차량, 로보틱스 분야에서의 synthetic 데이터](/assets/images/21-09-24-paper-autonomous-synthetic-data.png)

Labeling 작업의 비용이 큰 문제도 있지만 특정 상황에 대한 데이터 자체를 얻기 어려운 경우도 있다. 특히나 자율주행차량, 로보틱스 분야에서는 현실 세계에서 얻기 어려운 데이터들이 존재한다. 예를 들어 교차로 한가운데에 사람이 있는 데이터나, 로봇의 카메라 시점에서의 데이터 등은 쉽게 획득할 수가 없을 것이다.

이 경우 실제 세계를 digital twin 하여 시뮬레이션을 구축하는 방안이 있다. 시뮬레이션을 사용하게 되면 현실 세계에서 재현할 수 없는 상황들을 연출하고 환경을 자유자재로 변경하여 데이터들을 추출할 수가 있다. 컴퓨터 그래픽에 의해 RGB 이미지, depth 데이터, 객체의 bounding box, pixel 별 class, optical flow 등의 정확한 Ground Truth 데이터들이 자동으로 생성되기 때문에 이러한 synthetic 데이터를 이용해 AI 학습에 사용할 수 있다.

Synthetic 데이터를 사용해 학습한 모델이 현실 세계에서 잘 동작하기 위해서는 실제 세계와 시뮬레이션 간의 격차가 작아야만 한다. 하지만 시뮬레이션을 최대한 사실적으로 만들기 위해서는 많은 노력이 들어가고, 실제 세계의 모든 데이터들을 정량적으로 모델링하기에는 쉽지 않은 일이다.

본 논문에서는 synthetic 데이터를 사용하여 domain 학습 데이터에 대한 의존성을 감소시키는 동시에 실제 세계에서도 잘 동작할 수 있도록 task loss를 최소화 할 수 있는 방안에 대해 연구하고자 한다.

# 2. 선행 연구

## 2.1. Semantic Segmentation

Semantic segmentation(SS) 는 이미지의 각 픽셀이 어느 클래스에 속하는 지 예측하는 것으로, 이미지의 전반적인 의미와 구조를 파악하여 더 깊이 있게 이해하는 작업이다. 픽셀 수준의 레이블링이 수행되기 때문에 dense labeling task 라고 불리고, 이는 classification 이나 localization에 비해 어려운 문제로 분류된다.

딥러닝 아키텍처가 나오면서 성능이 상당히 개선되었는데 최근에는 입력 공간 차원을 유지하며 전역의 semantic 을 추출하기 위해 encoder, decoder로 구성된 auto-encoder 구조가 많이 사용되고 있다. 대표적으로 FCN, PSPNet, DRN, DeepLab 등과 같은 아키텍처들이 제안되었으며 좋은 성능을 보이고 있다.

하지만 대량의 labeled 데이터에 의존적이라 데이터셋을 확보하는데에 많은 시간을 들여야한다. 픽셀 별로 annotation 하는 것은 다른 visual task 에 비해 비용과 시간이 많이 소요되기 때문에 원하는 domain의 데이터를 얻기가 쉽지 않다.

이에 따라 synthetic 데이터에 대한 활용처가 높은 task로 분류될 수 있기 때문에 본 논문에서는 semantic segmentation을 downstream task로 설정하여 연구를 진행하고자 한다. 양이 많고 쉽게 얻을 수 있는 synthetic 데이터를 이용하여 지식을 추출하고 이를 활용하여 수동으로 labeling 하는 데이터의 양을 줄이고자 한다. 

## 2.3. Unsupervised Domain Adaptation

일반적으로 Labeled 데이터가 부족하면 pre-trained된 큰 모델을 이용하여 transfer learning을 수행하는 방법이 있다. Transfer learning을 하기 위해서도 해당 domain에 맞는 labeled 데이터가 필요하지만 Domain Adaptation(DA)은 다른 domain(Source)에서 배운 지식을 사용해 새로운 domain(Target)의 labeled 데이터가 없는 상황에서도 모델의 성능을 올릴 수 있는 방법이다. Zero-shot learning, few-shot learning, self-supervised learning과 함께 sample-efficient learning의 한 유형이다.

![](/assets/images/21-09-24-paper-domain-adaptation.png)

Source domain과 target domain 간의 데이터 분포가 다르기 때문에 source domain에서 학습된 모델을 target domain에 직접적으로 transfer 하게 되면 doamin shift(또는 domain gap)와 dataset bias에 의해 오차가 발생할 수 있다. 이 때 Domain Adaptation 방법론을 사용하면 source 와 target 데이터 분포를 유사하게 학습할 수 있다. 

Unsupervised Domain Adaptation(UDA)는 Target domain의 unlabeled 데이터를 사용하기 위해 다른 domain의 labeled 데이터를 사용하는 일종의 Semi-supervised learning의 변형이라고 볼 수 있다. Target domain의 labeled 데이터는 필요하지 않지만 충분한 양의 Unlabeled target 데이터가 필요하다.

![](/assets/images/21-09-24-paper-adaptation-level.png)

DA 문제를 풀기 위한 주요 전략은 source domain과 target domain 사이의 격차를 줄임으로써 예측 모델의 성능 저하를 막는 방법이다. Input-level, feature-level, output-level에서 각각 adaptation 모듈을 추가하여 domain 간 불일치성을 줄일 수 있다.

Adversarial Discriminative Adaptation by Backpropagation(CVPR 2017)는 UDA에 GAN을 도입하여 adversarial adaptation을 처음 제안한 논문으로, featue level에 domain discriminator를 추가하고 adversarial loss를 사용하여 domain discrepancy를 최소화 하는 방법을 제안했다. 

> feature level에 adversarial learning을 도입하여, source, target domain 간 confusion을 유도한다. segmentation 모델을 source, target에서 동일하게 사용하고 feature extractor를 통해 나온 feature값들이 source domain인지 target domain인지 구분하기 어렵게 학습시킴으로써 domain 불일치성을 줄이며 동시에 segmentation 모델이 학습되도록 했다.

Cycle-Consistent Adversarial Domain Adaptation(ICML 2018)에서는 feature level에 adversarial learning을 그대로 사용하고, input-level에서 source domain 이미지를 target 이미지와 유사한 스타일로 생성하도록 generative 모델을 도입한 방법을 제안했다. Generative 모델을 도입함으로써 input 데이터의 숨겨진 확률분포를 찾아내고 유사한 확률분포의 데이터를 생성해 사용하는 방식이다.

> Input level에서의 domain gap을 줄이기 위해 source domain의 이미지를 target 이미지와 유사한 스타일로 생성하도록 generative 모델을 사용하는 방법이 사용된다. Generative 모델을 도입함으로써 input 데이터에 대한 숨겨진 확률분포를 찾아내고, 이에 discriminator를 더해 domain confusion을 유도할 수 있다. 결과적으로 input-level의 adaptation module은 유사한 확률분포의 데이터를 생성해 내는 데에 목표가 있다. 

Learning to Adapted Structured Output space(CVPR 2018)에서는 두 domain의 이미지 스타일이 다르더라도 segmentation 결과에 포함되어 있는 공간 레이아웃, local context 등의 구조화된 특성은 유사하기 때문에 ouput-level에 adversarial learning을 도입하여 segmentation 예측 결과 distribution을 유사하게 학습하는 방법을 제안했다.

![](/assets/images/21-09-24-paper-da-methods.png)

정리하면 기존의 UDA연구는 source domain의 이미지를 target 이미지와 유사한 스타일로 생성하기 위해 generative 모델을 사용하거나, domain confusion을 위해 discriminator 를 사용하거나, adversiarl learning을 통해 두 domain의 차이를 측정하는 방식으로 많이 연구되고 있다. discrepancy-based methods, adversarial discriminative methods, generative model을 변형하거나 적절이 섞어가며 다양한 방법들이 제안되고 있다. 그 외에도 분류기 불일치 분석, 엔트로피 최소화, 커리큘럼 학습과 같은 방법도 제안된다.

본 논문에서는 taget domain과 유사한 스타일의 이미지를 생성하여 학습 데이터로 사용하고, feature-level 과 output-level에 adversarial learning을 도입함으로써 domain gap을 줄이고자 한다.

> - adversarial learning : target의 distribution이 source와 유사하도록 학습을 진행. source 쪽에서 학습한 feature들을 target domain condition으로 옮겨주겠다 라는 의미.
> - Generative network : target image를 source와 유사하게 만들어서 학습을 시키자. 혹은 source의 style을 target에 맞게 재생산해서 학습시키자

> - Entropy minimization, Generative 모델, adversarial learning을 통해 domain invariance representation을 추출하는 방법이 좋은 결과를 가져왔다. 
> - 엔트로피 최소화[31, 43], 생성 모델링[16, 12] 및 적대적 학습[42, 41]을 통해 domain 불변 표현을 추출하는 UDA 방법에 의해 인상적인 결과가 달성되었습니다. 
> - 메인 간 불일치를 줄이기 위해 수많은 UDA 방법[19, 42, 43, 34]은 적대적 학습을 도입하여 분포 일관성에 중점을 둡니다. 이미지에서 이미지로의 변환[21, 54]에서 영감을 받아 소스 데이터에 따라 대상 이미지를 생성하기 위해 UDA 방법 범주가 제안되었습니다[19, 18]. 
> - 대상 의사 레이블을 사용한 자체 감독은 비교적 간단하지만 효율적인 접근 방식이지만[6, 55] 감독을 위한 소스 데이터가 필요합니다.
> - 주류 접근 방식 중 하나는 적대적 학습[42, 41, 6, 5, 17, 37, 19]이며, 판별자를 사용하여 두 domain의 차이를 측정하는 것을 목표로 합니다. 
> - 생성 네트워크[38, 16, 50]를 활용하여 주석이 달린 소스 이미지에 스타일 전송 기술을 적용하여 대상 스타일 이미지를 생성하는 것입니다. 
> - 적대적 학습, 생성 기반, 분류기 불일치 분석, 자가 학습, 엔트로피 최소화, 커리큘럼 학습과 같은 (상호 배타적이지 않은) 범주를 기반

## 2.3. Semi-supervised learning

Semi-supervised learning(SSL)은 소량의 labeled 데이터과 다량의 unlabeled 데이터를 사용하여 학습하는 방식이다.

Self-training은 소량의 labeled 데이터과 다량의 unlabeled 데이터를 사용하여 학습하는 방식인 Semi-supervised learning(SSL)의 한 방법이다. Labeled 데이터로 충분히 학습된 모델을 사용하여 unlabeled 데이터에 pseudo-labeling 을 하고, pseudo labeled 데이터와 labeled 데이터를 함께 학습에 사용하는 방식이다. 

Noisy Student(CVPR 2020)는 대표적인 Teacher-student 기반의 Self-training으로, labeled 데이터로 학습된 teacher를 이용해 unlabeled 데이터에 pseudo-labeling 하고 이 데이터에 노이즈를 추가하여 student 모델을 학습하는 방법이다. Student 모델이 어느정도 학습이 되면 teacher 모델로 설정하고 다시 pseudo labeling을 수행하게 만들어 반복적인 학습 파이프라인을 구성했고 이를 통해 SOTA를 기록할 정도로 좋은 성능을 보인 연구이다. Meta Pseudo Labels(CVPR 2021)은 Teacher 모델과 Student 모델을 동시에 학습하는 방법으로, Teacher 모델이 잘 학습되어 있지 않더라도 student 모델과 유기적으로 학습할 수 있도록 Co-training 방식을 제안하였다.

> Noise를 추가함으로써 regularization 효과를 주고 새로운 input들에 대해 robust 하게 학습하도록 한다.

최근 많이 사용되는 SSL 방법은 Consistency regularization으로, Unlabeled 데이터에 noise를 추가하더라도 예측 분포는 유지된다는 아이디어에 기안한 방법이다. 노이즈가 없는 원본 데이터와 노이즈가 주입된 데이터를 동일한 예측 분포로 학습하는 것이 consistency regularizatio의 목표이다. 
Virtual Adversarial Training는 입력 데이터의 adversarial transformation를 생성하고 예측 결과 간의 KL-divergence를 측정하여 학습시키는 방법을 제안했다. 
Unsupervised Data Augmentation for Consistency Training는 노이즈 대신 최신 augmentation 기법인 AutoAugment를 사용하여 예측 결과 간 KL-divergence를 Loss로 사용한 방법으로, 적은 labeled 데이터만으로 supervised learning을 뛰어넘는 성능을 보였다.

그리고 MixMatch, FixMatch 등 Pseudo-labeling과 augmentation, consistency regularization을 포함한 다양한 regularization 기법을 함께 사용하여 다양한 semi-supervised learning 연구들이 나오고 있다.

본 논문에서는 학습된 adaptation 모델을 사용해 unlabeled target 데이터에 pseudo-label을 생성하고, 학습 데이터로 재사용할 수 있도록 Self-training 파이프라인을 구성함으로써 target domain에 맞게 모델을 fine-tuning 한다.

> - 대상 domain의 작은 이미지 세트에만 주석을 달고 반 지도 학습(SSL) 기술을 사용하여 레이블이 지정되지 않은 데이터를 충분히 활용하는 것입니다[10, 30, 9, 29]. SSL 설정에서 레이블링된 데이터의 부족으로 인해 획득한 모델은 레이블링된 데이터의 소량에 과적합될 위험이 있습니다. 다른 domain에서 사용 가능한 레이블이 지정되지 않은 제한된 레이블이 지정된 데이터를 효과적으로 활용하는 방법은 픽셀 단위 예측 작업에 대한 모델의 정확도와 일반화를 개선하는 데 핵심입니다
> - 자체 훈련[21, 51, 23, 14]에 기반한 일부 방법은 레이블이 지정되지 않은 데이터의 의사 레이블을 생성하고 이를 사용하여 모델을 미세 조정하는 데 사용되었습니다.
> - Self-Training : unlabeled data를 위해 pseudo-labels를 생성해서 학습하는 방법. 스스로 필요한 GT를 나름대로 만들어서 그걸 기반으로 학습한다는 의미
> - 본 논문에서는 학습된 adaptation 모델을 사용해 unlabeled 데이터에 pseudo-label을 생성하고, 학습 데이터로 재사용 할 수 있도록 Self-training 파이프라인을 구성한다.

# 3. 제안 방법

Synthetic 데이터만을 학습된 모델은 real data와의 domain gap이 존재하기 때문에, 실제 real world 에서 사용하기에는 성능이 좋지 않다. 그렇기 때문에 synthetic 데이터만으로 학습된 모델을 직접 adaptation 하여 모델을 학습시키지 않고, unlabeled real data에 pseudo-labeling 해주는 teacher 모델로 사용하고자 한다.

Adapted data로 task model을 충분히 학습시키고, 학습된 모델을 unlabeled 데이터에 pseudo labeling을 해준다. 어느정도 labeled real data가 모이면 adapted data와 real data를 함께 입력으로 넣어 task model을 학습시키고, 점차 real dat의 비율을 늘려 나감으로써 real world에서도 잘 동작하는 SSL 학습 루프를 구성한다.

![](/assets/images/21-09-24-paper-overview.png)

1. Real data와 synthetic data를 사용해 GAN based DA 모델을 학습시킨다.
2. DA 모델의 Generator를 이용해 synthetic data로부터 adapted synthetic data를 생성한다.
3. Adapted synthetic data와 GT 데이터를 사용해 task model을 충분히 학습시킨다.
4. 학습된 task model을 teacher 모델로 사용하여, unlabeled real data에 pseudo-labeling 한다.
5. Adapted synthetic data와 pseudo labeling된 real data를 이용해 task model을 재학습시킨다.
6. 4, 5 과정을 반복한다.

위와 같은 학습 루프로 반복적으로 학습을 수행하며 synthetic data와 real data 간의 domain gap을 줄이고, 동시에 pseudo-labeling 모델의 성능을 함께 향상시킬 수 있는 시스템을 구성한다. 그리고 데이터에 다양한 Noise 기법 실험, unbalanced data 보강 등의 기법들을 이용해 synthetic data의 이점을 살리고 accuracy를 끌어올릴 수 있도록 한다.
Real dataset과 Synthetic dataset를 이용해 semantic segmentation을 수행하고 Class 별 IoU, mean IoU, pixel accuracy를 비교하여 제안한 방법으로 성능이 좋아짐을 확인한다.

# Entripoy minimization

$$ entropy(p) = - \sum_{c=1}^C p_c \log p_c $$

## CutMix Segmentation

$$ r_x \sim Unif \{ 0, W \}, r_w = W \sqrt {1- \lambda}
\\
r_y \sim Unif \{ 0, H \}, r_h = H \sqrt {1- \lambda}
\\
B = \{ r_x , r_y, r_w, r_h \}
\\
$$

$$
\tilde {x} = M \odot x_s + (1-M) \odot x_t
\\
\tilde {y} = \lambda_{y_s} + (1- \lambda)y_t
$$

$$L_{seg} = -\sum_{h=1}^{H}\sum_{w=1}^{W}\sum_{c=1}^{C} y_s^i log(P(x_s^i)) - \lambda_{cm} \sum_{h=1}^{H}\sum_{w=1}^{W}\sum_{c=1}^{C} y_c^i log(P(x_c^i)) $$


$$ L_{adv} = \mathbb{E}[ log(D_p(P(x_s^i))) + log (1 - D_p(P(x_t^j))) ]
 $$

## Input-level Adaptation

먼저 Input-level에서의 domain gap을 줄이기 위해 source domain의 image appearance가 target domain과 유사하도록 생성해낸다. 대표적인 image-to-image translation 방법인 CycleGAN을 이용하여 target domain 스타일의 이미지로 변형시켜 domain adaptation 모델의 source 데이터로 사용하고자 한다.

$$ L(G, F, D_x, D_Y) = L_{GAN}(G, D_Y, X, Y) + L_{GAN}(F, D_x, Y, X) + \lambda L_{cyc}(G, F) $$

$$ G^*, F^* = arg min_{G, F} max_{D_x, D_y} L(G, F, D_x, D_y) $$

Adversiarl loss와 cycle consistency loss를 결합한 방법으로, $X$가 source domain 샘플, $Y$가 target domain 샘플이라고 했을 때, $X \rightarrow Y$로 가는 GAN의 adversarial loss와 $Y \rightarrow X$로 가는 GAN의 adversarial loss에 각각 원본으로 복구하는 cycle consistency loss를 더해준 값이 total loss가 되고, 이 loss를 최소화 하는 방향으로 $G: X \rightarrow Y$와 $F: Y \rightarrow X$를 학습시킨다.

![](/assets/images/21-09-24-paper-style-transfer.png)
> GTA5(Source) 이미지를 Cityscapes(Target) 스타일로 image translation 한 결과

Source domain과 target domain의 분포를 구분할 수 없도록 학습시키는 방법이기 때문에 domain 간 gap이 줄었을 것이라고 가정할 수 있고, 해당 모델을 통해 생성된 데이터는 target domain 분포에 좀 더 가까운 형태가 될 수 있다. Source 데이터를 그대로 사용하는 것 보다 Target domain에 맞게 이미지를 변형하여 target task 모델에 사용하면 성능이 좋아지는 것을 볼 수 있었는데, 이에 관한 실험 결과는 4.3에서 확인할 수 있다.

# 4. 실험 및 결과

> UDA 기술의 주요 측면은 한 데이터세트에서 획득한 지식을 다른 컨텍스트로 이전하는 기능입니다. 따라서 고려된 데이터는 UDA 알고리즘의 설계 및 평가에서 근본적인 역할을 합니다. 이 섹션에서는 가장 흥미로운 애플리케이션 시나리오 중 하나에 초점을 맞춥니다. 
> 감독되지 않은 domain 적응 시나리오에서 대상 domain에 있는 실제 샘플의 값비싼 레이블이 교육에 필요하지 않다는 점을 강조합니다. 그러나 제한된 수의 실제 대상 샘플은 알고리즘의 성능을 테스트(때로는 검증)하기 위해 수동으로 레이블을 지정해야 합니다. 반면에 소스 domain에 해당하는 대규모 합성 데이터 세트에는 지도 학습에 활용되는 주석이 장착되어 있습니다.

## 4.1. 데이터셋

![](/assets/images/21-09-24-paper-dataset.png)

Cityscapes는 유럽 50개 도시의 거리에서 차량 시점으로 캡쳐한 2048x1024 픽셀의 이미지 데이터셋이다. 2,975개의 training set, 500개의 validation set, 1,595개의 test set으로 총 5,000개의 이미지 데이터와 pixel-wise semantic label로 구성되어 있다. 추가로 label 되어 있지 않은 19,998 개의 raw 이미지 데이터도 제공된다.

GTA5는 도시 운전을 위한 대규모 synthetic 데이터셋 중 하나이다. 사실적인 그래픽을 보여주는 GTA V 게임을 통해 렌더링 되었고 미국 스타일 도시에서의 자동차 관점에서 촬영되었다. 24,966개의 1914x1052 픽셀의 이미지와 pixel-wise semantic label로 구성되어 있다. Label은 Cityspcaes의 클래스에 맞게 19개 클래스로 재매핑 할 수 있다.

|  Dataset   |   Type    | # of Labeled Data | # of Raw Data | # of Classes |
| :--------: | :-------: | :---------------: | :-----------: | :----------: |
| Cityscapes |   Real    |       5,000       |    19,998     |      19      |
|    GTA     | Synthetic |      24,966       |       -       |      19      |

실험에서 서술하는 Source 데이터는 Synthetic 데이터인 GTA5이고, Target 데이터는 실제 데이터인 Cityscapes로 이다. Cityscapes와 GTA5는 시각적인 차이가 존재하지만 비슷한 차량의 높이에서 도로 상황을 캡쳐한 데이터라는 점에서 구조가 유사하다고 볼 수 있다. GTA5 데이터가 Cityscapes 데이터 양에 비해 약 5배 가량 많기 때문에 GTA5의 데이터를 최대한 활용하여 이미지의 전반적인 의미와 구조를 학습하고, Cityscapes의 unlabeled 데이터까지 적절히 이용하여 Cityscapes 환경에서 잘 동작하는 Semantic segmentation 모델을 구성하였다.

## 4.2. 평가 Metric

평가 metric으로는 Semantic segmentation 평가 시에 주로 사용되는 클래스 별 IoU(Intersection over Union) 와 mIoU(Mean Intersection over Union)를 사용한다. $TP_i$, $FP_I$, $FN_i$ 는 특정 클래스 $i$의 True Positive, False Positive, False Negative 이고 $N$은 클래스의 갯수이다.

$$ {IoU}_i = \left( \frac{TP_i}{FP_i + FN_i + TP_i} \right) $$

$$ mIoU = \sum_{i=1}^N \left( \frac{IoU_i}{N} \right ) $$

## 4.3. Input-level Adaptation

### 4.3.1. Semantic Segmentation

Source 이미지에서 Target 이미지 스타일로 재생성된 데이터가 domain gap을 줄이는데 얼마나 기여하였는지 확인하기 위한 실험이다. 해당 모델을 Source domain 이미지와 Target 이미지 스타일로 재생성된 이미지(Source ➔ Domain)에 적용했을 때 mIoU를 비교하고 성능이 나아짐을 확인한다.

![](/assets/images/21-09-24-paper-sem-seg.png)

우선 일반적인 Semantic segmentation 모델과의 비교 대조군을 위해 SOTA 모델인 FCN과 DeepLab v3+ 성능 비교를 수행하였다. Backbone은 동일하게 Resnet-50으로 설정하였고 Cityscapes의 Labeled 이미지들로 Supervised-learning 방식으로 학습시켰다.

|    model    | road | sidewalk | building | wall | fence | pole | traffic light | traffic sign | vegetation | terrain | sky  | person | rider | car  | truck | bus  | train | motorcycle | bicycle | Pixel Accuracy | mIoU  |
| :---------: | :--: | :------: | :------: | :--: | :---: | :--: | :-----------: | :----------: | :--------: | :-----: | :--: | :----: | :---: | :--: | :---: | :--: | :---: | :--------: | :-----: | :------------: | :---: |
|     FCN     | 0.97 |   0.81   |   0.9    | 0.26 | 0.45  | 0.54 |     0.67      |     0.75     |    0.91    |  0.53   | 0.93 |  0.78  | 0.57  | 0.93 | 0.48  | 0.7  | 0.36  |    0.56    |  0.75   |     94.693     | 68.03 |
| DeepLab v3+ | 0.98 |   0.84   |   0.92   | 0.56 | 0.59  | 0.64 |      0.7      |     0.78     |    0.92    |  0.64   | 0.95 |  0.81  | 0.63  | 0.94 | 0.69  | 0.76 | 0.43  |    0.63    |  0.76   |     95.826     | 74.97 |

DeepLab v3+가 더 좋은 성능을 보이기도 하였고, 버전에 따라 여러가지 변형 모델이 많이 나오고 있기 때문에 앞으로의 실험에서는 DeepLab 기반의 모델을 Semantic segmentation task 모델로 사용한다.

### 4.3.2. Image-to-image translation

이번 실험에 사용한 모델은 Resnet-50을 backbone으로 한 DeepLab v3+ 모델로 target domain 샘플로만 학습되었고, Source domain과 Source ➔ Target domain 테스트에 사용한 데이터의 갯수는 24,966로 동일하게 설정하여 실험했다.

![](/assets/images/21-09-24-paper-style-transfer-prediction.png)

위 이미지는 Labeled 데이터 없이 Cityscapes 이미지와 GTA 이미지만 이용하여 CycleGAN을 학습한 결과물이다. 3열의 GTA ➔ Cityscapes는 GTA 이미지를 입력 데이터로 하여 Cityscapes 스타일로 재생성한 이미지 결과물이다.
GTA5 데이터를 예측한 결과는 시각적으로 봤을 때 많은 노이즈가 섞여있는 것을 볼 수 있는데, Cityscapes와 GTA5의 데이터 자체의 domain gap에 의해 Cityscapes 데이터에 적용했을 때 보다 더 많은 예측 오차가 발생하는 것을 볼 수가 있다. 반면에 GTA5 ➔ Cityscapes 데이터를 예측한 결과는 GTA5 데이터에 비해 적은 오차를 보이고 있다.

|                         | Pixel Accuracy | mIoU  |
| :---------------------: | :------------: | :---: |
|      Source Domain      |     72.10      | 30.18 |
| Source ➔ Target Domain  |     79.07      | 36.26 |
|      Target Domain      |     95.82      | 74.97 |

GTA5 이미지를 그대로 사용하는 것에 비해 generative 모델을 통해 target domain 스타일로 생성된 데이터를 이용했을 때 mIoU가 +6% 증가하였고, target domain과의 gap이 줄어든 것을 알 수 있다.

## 4.3. Data dependency 실험

- 적은 target 데이터셋으로 잘 훈련될 수 있는지 확인하기 위해 labeled 데이터를 줄여가며 실험
- 작은 데이터셋으로 잘 훈련될 수 있는지 여부
- SSL 성능을 평가하기 위해 보통 Labled 데이터를 적게 사용하고 Unlabeld 데이터를 많이 사용해 테스트한다.

![](/assets/images/21-09-24-paper-data-dependency.png)

![](/assets/images/21-09-24-paper-citys-seg.png){:width="300px"}
> cityscapes labeld 데이터 줄여가며 실험

```sh
# cityscapes epoch 20000
# 2975: 68.03 (epoch: 20,) / 69.12 (epoch: 30,)
# 2000: 56.68 (epoch: 20,)
# 1000: 58.87 (epoch: 20,)
# 500: 55.35 (epoch: 20,)
# 100: 40.5 (epoch: 20,)
```

```sh
# 제안 모델
# 2975: 48.39 (epoch: 20,) / 56.39 (epoch 40,)
# 1000: 48.1 (epoch 20,)
# 500: 49.82 (epoch 20,) / 51.6 (epoch 30,)
# 100: 45.97 (epoch 20,) / 49.19 (epoch 40,)
```

![](/assets/images/21-09-24-paper-regularization-train.png)
- training loss

![](/assets/images/21-09-24-paper-regularization.png)
- 파란색 선은 cityscapes_d100
- 회색선은 제안 방법으로 citys 데이터 100개만 사용
- validation loss

## 4.5. Performance 실험

- 알고리즘이 얼마나 잘 수행되는지
- SOTA 모델과 비교
- single origin 학습 결과 (ce + adversiarl loss)
- 

# 5. 결론

# 6. 참고 문헌

- [Cycle-GAN](Unpaired Image-to-Image Translation using Cycle-Consistent Adversarial Networks)

---

1. Abstract
2. Introduction
3. Related Work
4. Method
5. Experiments
6. Conclusions
7. References

---

# 링크

- [논문심사일정](https://eyonsei.yonsei.ac.kr/info.asp?mid=m03_10)
- [학위논문 작성법](https://graduate.yonsei.ac.kr/graduate/academic/notice_haksa.do?mode=view&articleNo=28347&article.offset=0&articleLimit=10&srCategoryId1=363)

# Backup

![](/assets/images/21-09-24-paper-image-translation-experiments.png)

### Domain Adaptation 

```sh
# Source: GTA
# Target: Cityscapes
# domain adaptation 모델을 통해 task 모델의 mIoU 측정
===>road:       83.99
===>sidewalk:   10.25
===>building:   74.36
===>wall:       4.47
===>fence:      3.08
===>pole:       10.36
===>light:      11.83
===>sign:       11.46
===>vegetation: 84.24
===>terrain:    15.83
===>sky:        68.71
===>person:     39.09
===>rider:      0.15
===>car:        79.1
===>truck:      26.16
===>bus:        17.3
===>train:      0.0
===>motocycle:  2.78
===>bicycle:    0.01
===> mIoU: 28.59
```

```sh
# Source: Cityscapes 스타일의 GTA5 데이터
# Target: Cityscapes
# domain adaptation 모델을 통해 task 모델의 mIoU 측정
===>road:       89.43
===>sidewalk:   26.21
===>building:   82.72
===>wall:       25.88
===>fence:      22.92
===>pole:       36.15
===>light:      40.42
===>sign:       38.88
===>vegetation: 84.1
===>terrain:    37.55
===>sky:        83.52
===>person:     59.09
===>rider:      26.71
===>car:        83.65
===>truck:      28.55
===>bus:        36.64
===>train:      0.14
===>motocycle:  14.6
===>bicycle:    26.76
===> mIoU: 44.42
```
