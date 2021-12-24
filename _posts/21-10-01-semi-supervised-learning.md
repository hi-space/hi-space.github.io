---
title: Semi-supervised learning
category: AI
tags: ai
article_header:
    type: overlay
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/21-10-01-semi-supervised-learning-semi-supervised-learning.png
---

<!--more-->

# Approach

Semi-supervised learning 은 소량의 labeled 데이터를 사용한 학습 방법이다. 소량의 labeled 데이터로만 학습하게 되면 overfitting 과 같은 문제가 발생할 가능성이 크다. 그래서 **소량의 labeled 데이터와 대량의 unlabeled 데이터를 함께 사용하여 성능을 향상**시키기 위해 semi-supervised learning이 사용된다. 

![](/assets/images/21-10-01-semi-supervised-learning-semi-supervised-learning.png)

- **Generative Models**: Gaussian Mixture, Deep Generative Model
- **Graph Based**: Label Propagation (이웃이 많이 속한 label이 내 label)
- **Self-Training**: Self-Training, Co-Training
- **Consistency Regularization**: Pi-Model, Mean Teacher

이와 같은 접근법들이 많이 사용되어 왔고 더불어 다양한 regularization 방법들이 적용되어 왔다. (Weight decay, Mixup, Entropy Minimization)

# 1. Self-Training

가장 간단한 semi-supervised learning 방법으로, Labeled data로 충분히 학습된 모델을 사용하여 unlabeled data에 pseudo-labeling을 한다. 그리고 labeled data 와 pseudo-labeled data 를 섞어서 학습을 하는 방식이다. 학습 초기에는 labeled 데이터만 학습에 사용하다가 점진적으로 pseudo-labeling 데이터들도 학습에 사용한다.

![](/assets/images/21-10-01-semi-supervised-learning-self-training.png)

1. 충분한 Labeled data로 모델을 학습
2. 1번을 통해 학습된 모델로 Unlabeled data 를 prediction
3. Prediction 값 중에서 confidence가 높은 것에 Pseudo Labeling
4. Labeled data 에 Pseudo Labeled 데이터를 포함시키고 1번부터 반복

iteration을 반복할 수록 Labeled 데이터가 더욱 늘어나고, 확장된 데이터셋으로 모델의 성능도 함께 향상될 수 있다. 하지만 처음 1번 과정에서 얻어지는 모델의 성능이 좋지 않으면 Pseudo labeling 작업에서 error rate 가 증가하기 때문에 전체적인 모델 성능이 떨어질 수도 있다.

- 가장 간단한 semi-supervised learning
- 어떤 알고리즘이라도 적용 가능 (wrapper method)
- 초반의 잘못된 예측이(early mistakes) 잘못된 길로 인도할 수 있다.
- 완전히 converge 한다고 할 수 없다

## Pseudo-label

> [Pseudo-Label: The Simple and Efficient Semi-Supervised Learning Method for Deep Neural Networks(2013)](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.664.3543)

2013년에 발표된 Pseudo-Label 논문이다. Labeled 이미지와 Unlabeled 이미지를 동시에 학습시키는 방법이다.

![](/assets/images/21-10-01-semi-supervised-learning-2021-10-04-11-59-23.png)

1. Labeled 데이터를 일반적인 Cross-entropy loss의 Supervised learning 방식으로 학습
2. Labeled 데이터로 학습된 동일한 모델을 통해 Unlabeled 데이터를 prediction 한다. 이 때, Pseudo-label로 maximum confidence class 가 사용된다 (confidence가 높은 클래스를 true label 로 사용)
3. Model prediction과 unlabeled 데이터의 pseudo label을 비교하여 Cross-entroy loss가 계산된다.

$$ L = L_{labeled} + \alpha_{t} * L_{unlabeled} $$

처음 $\alpha_{t}$ 는 0 ~ 100 step 까지 0으로 설정해서 labeled 데이터로만 모델이 학습되도록 설정하고, 100 step 이후부터는 $\alpha_{t}$ 의 값을 늘려나는 형태로 학습한다. 이와 같이 설정하면 optimization이 local minima에 빠지는 것을 예방하는데 도움을 준다고 한다.

![](/assets/images/21-10-01-semi-supervised-learning-2021-10-04-12-03-33.png)

## Noisy Student

> [Noisy Student: Self training with Noisy Student improves ImageNet classification (CVPR 2020)](https://arxiv.org/pdf/1911.04252.pdf)

Teacher-Student 모델을 구성하여 각각 training 시키는 방식.

1. labeled image로 teacher model을 학습
2. 학습한 teacher model를 사용해 많은 unlabeled image에 pseudo label을 생성
   - 높은 auccuracy 로 labeling 하기 위해 Noise를 사용하지 않음
3. labeled image와 pseudo labeled image를 결합하고 noisy를 추가하여 student model을 학습
   - Noisy: dropout, stochastic depth, data augmentation
   - Noisy Studnet model은 teacher model 보다 크다
4. 학습된 student model을 teacher로 사용하여 pseudo label을 다시 생성 (step 2 부터 반복)

![](/assets/images/21-10-01-semi-supervised-learning-noisy-student.png)

## Meta Pseudo Labels

> [Meta Pseudo Labels CVPR(2021)](https://arxiv.org/pdf/2003.10580.pdf)

Noisy Studnet 에서는 Teacher와 student 모델이 따로 학습되었다. Teacher 모델이 labeled 데이터로 잘 학습되지 않으면 student 모델에게 knowledge distilation 할 때 잘못된 지식을 전파할 수 있다. Meta Pseudo labels은 그러한 문제점을 해결하기 위해 Teacher 와 student 모델을 동시에 학습하는 방법이다.

![](/assets/images/21-10-01-semi-supervised-learning-meta-pseudo-labels.png)

Student 가 학습되는 동안 labeled image 에 대한 studnet 의 성능이 teacher 에게 reward 로 전달되고, 이것을 Loss 함수의 파라미터로 사용한다. 
- Student는 teacher에서 생성된 pseudo labeled data로 학습
- Teacher은 student가 labeled image에 대해 얼마나 잘 작동하는지의 reward signal로 학습

# 2. Consistency regularization

![](/assets/images/21-10-01-semi-supervised-learning-2021-10-04-12-57-49.png)

이 방법은 Unlabeled 데이터에 noise를 추가하더라도 그 prediction은 유지된다는 아이디어로 시작한다. **noise가 없는 원본 데이터와 noise가 주입된 데이터를 동일한 class 분포로 예측하도록 학습**하는 것이 Consistency regularization 방법이다. noise는 image augmentation, gaussian noise와 같은 input noise를 사용할 수도 있고 dropout을 사용해 architecture 자체에 통합될 수도 있다.

1. 모델을 이용해 Unlabeled data의 distribution을 prediction
2. Unlabeled data 에 noise 추가 (data augmentation)
3. 1번의 prediction 을 augmented data 의 정답 label로 사용해 모델을 학습

- 데이터에 약갼의 augmentation을 가하더라도 데이터의 정답 label은 동일하다는 성질을 이용. 
- 최근 SSL SOTA에서 많이 사용됨
- 동일한 이미지를 각기 다르게 augmentation 해서 predictive probability distribution을 유사하게 학습하는 방법이다. (loss는 두 확률분포의 차이)

> **Consistency Training**: SSL의 분야 중 하나로, 입력 데이터/피처맵/은닉층에 약간의 노이즈(Gaussian/dropout/adversarial)를 추가했을때 모델의 예측이 바뀌지 않도록 모델을 규제하며 학습시키는 방법이다.

![](/assets/images/21-10-01-semi-supervised-learning-consistency-regularization.png)

formula: 

$$ \sum_{b=1}^{\mu B} \parallel p_m(y | \alpha(u_b)) - p_m(y | \alpha (u_b)){\parallel}_2^2 $$


## $\Pi$-model

> [$\Pi$-model: Temporal Ensembling for Semi-Supervised Learning (ICLR 2017)](https://arxiv.org/abs/1610.02242)

Consistency Training의 초석이 된 논문으로, labeled/unlabeled 이미지 모두에 augmentation을 취해 prediction 하는 방식이다.

![](/assets/images/21-10-01-semi-supervised-learning-2021-10-04-13-08-31.png)

1. Labeled 데이터와 Unlabeled 데이터 상관없이, 무작위로 2개의 augmentation 을 취한다
2. Dropout 이 있는 모델을 사용해 augmentation된 2개의 데이터를 prediction
3. 2개의 prediction의 제곱 차이를 consistenccy loss 로 사용
4. augmentation 취한 데이터가 labeled 데이터라면 cross-entropy loss 도 함께 계산
5. Total Loss = cross-entropy loss + $w(t)$ * consistency loss

저자는 Labeled 데이터만 사용한 SL에서도 이 방법이 Consistency를 증가시켜 모호한 sample도 더 잘 구분한다고 주장한다.

## Mean Teacher

> [Mean teachers are better role models: Weight-averaged consistency targets improve semi-supervised deep learning results](https://arxiv.org/pdf/1703.01780.pdf)

![](/assets/images/21-10-01-semi-supervised-learning-mean-teacher.png)

Teacher model과 studnet model이 서로 각각의 모델을 가지고 있다.

## Virtual Adversarial Training

> [Virtual Adversarial Training: A Regularization Method for Supervised and Semi-Supervised Learning](https://arxiv.org/pdf/1704.03976.pdf)

Consistency regularization을 위해 adversarial 개념을 사용한다. input 데이터에 단순한 noise를 주는 것이 아니라, adversarial transformation을 사용해 loss의 값을 최대한 해치는 방향으로 데이터를 생성한다.

![](/assets/images/21-10-01-semi-supervised-learning-virtual-adversarial-training.png)

1. 원본 데이터와 adversarial 데이터 간의 KL-diverse가 최대화 되도록 이미지의 adversarial transformation를 생성
2. Labeled/Unlabeled 데이터 원본과 adversarial transformation 데이터에서 각각 model prediction
3. KL-divergence는 consistency loss로 사용
4. Labeled 이미지의 경우 cross-entropy loss도 계산
6. Total loss = cross-entropy loss + $\alpha$ * consistency loss

## Unsupervised Data Augmentation

> [Unsupervised Data Augmentation for Consistency Training](https://arxiv.org/pdf/1904.12848.pdf)

단순한 noise 대신 최신 data augmentation 기법인 AutoAugment 사용

![](/assets/images/21-10-01-semi-supervised-learning-2021-10-04-13-27-56.png)

1. Unlabeld image에 AutoAugment를 사용해 augmentation 이미지를 생성
2. 동일한 모델을 사용해 원본 이미지와 augmentation 이미지를 prediction
3. 두 개의 prediction의 KL-divergence는 consistency loss로 사용
4. Labeled image는 consistenccy loss를 계산하지 않고, cross-entropy loss 만 계산
5. Total loss = cross-entropy loss + $\alpha$ * consistency loss

이미지 뿐만 아니라 텍스트에도 적용할 수 있느데, 20개의 labeled 데이터만으로 fully supervised를 뛰어넘는 성능을 냈다.

# 3. Hybrid

## MixMatch

Entropy minimization, Label consistency regularization, mixup을 모두 적용한 방법

> [MixMatch: A Holistic Approach to Semi-Supervised Learning](https://arxiv.org/pdf/1905.02249.pdf)

![](/assets/images/21-10-01-semi-supervised-learning-mix-match-temp-sharpen.png)
![](/assets/images/21-10-01-semi-supervised-learning-mix-match.png)

1. Unlabeld 데이터에 대해 K개의 augmentation 데이터를 생성하고, predictoin 평균을 구해 temperature sharpening을 통해 sharpen 하여 pseudo-labeling으로 사용한다 (Entropy Minimization)
2. Augmentation 된 labeld, unlabeld 데이터를 모두 결합해 Mixup 한다 
3. SL loss는 cross-entropy loss, unsupervised loss 는 모델 출력값의 차이(MSE)가 된다 (consistency regularization)

## ReMixMatch

- MixMatch 후속 알고리즘으로, Consistency Training과 Mixup을 사용한 방법. 
- **Distribution Alignment**: Unlabeled data의 predictive distribution을 labeled data의 실제 분포에 ㅁ자춰 조정
- **Augmentation Anchoring**: Augmentation 할 때 Weak augmentation이 적용된 데이터 예측 분포를 strong augmentation이 적용된 데이터의 target으로 이용

## FixMatch

> [FixMatch: Simplifying Semi-Supervised Learning with Consistency and Confidence](https://arxiv.org/pdf/2001.07685.pdf)

Pseudo-labeling과 consistency regularization을 결합한 방법. weak augmentation과 strong augmentation(RandAugment, CTAugment)에 pseudo-labeling 방법을 함께 도입

![](/assets/images/21-10-01-semi-supervised-learning-fixmatch.png)

1. Labled 데이터를 통해 SL 학습 (cross-entropy loss)
2. Unlabeled 데이터를 Weakly-augmentation 하고 모델을 통해 prediction 값을 얻어낸다.
3. Class probability에서 threshold 값을 넘는 클래스를 one-hot pseudo-label을 취한다. (해당 클래스의 확률을 1로)
4. 같은 이미지에 대해 Strong-augmentation 한다
5. Strong augmentation 이미지를 input으로 사용하여 얻어진 class probability 와(Cross-entropy loss), weaky augmentation에서 얻어진 pseudo label 결과와 함께 모델을 학습시킨다. 

# Regularization

## Entropy Minimization

![](/assets/images/21-10-01-semi-supervised-learning-entropy-minimization.png)

- Unlabeled data를 학습시킬 때 pseudo-labels을 그대로 사용하지 않고 entropy minimization 시켜준다.
- unlabeled data의 hidden representation을 학습할 때, entropy가 다소 높다면 다른 클래스와의 decision boundary 주변에 feature vector가 위치할 (또는 그렇게 되도록 학습이 될) 가능성이 높고, 성능 저하를 불러일으킬 수 있습니다.
- pseudo-label의 maximum softmax probability를 기준으로 사용
- prediction probability의 confidence를 높이기 위해 사용한다. 주로 softmax temperature를 이용
- 모델을 좀 더 confident 하게 학습하는 것이 목표이다. loss에 entropy minimization term 을 추가하여 학습한다.

## Mixup, Cutout, CutMix

이미지에서 자주 사용되는 Regularization 방법으로, 간단하지만 높은 효과를 보이고 있다.

![](/assets/images/21-10-01-semi-supervised-learning-cutmix.png)

# Sharpening Predictions

unlabeled 데이터에 대한 모델의 예측이 평평하여 (over-flat) 학습이 잘 되지 않을 수 있다는 문제는 어떻게 해결하기 위해 세가지 방법을 소개한다. (Unsupervised Data Augmentation for Consistency Training 논문)

- Confidence-based masking: 예측의 confidence가 낮은 unlabeled 데이터를 학습에 이용하지 않습니다.
- Entropy minimization: augmented 데이터의 예측값이 낮은 entropy를 가지도록 (=예측이 더 sharp하도록) entropy objective term을 전체 objective에 추가합니다.
- Softmax temperature controlling: unlabeled 데이터의 예측값을 계산할 때 1 미만의 softmax temperature를 적용하여 augmented 데이터의 타겟이 더 sharp해지도록 합니다.

논문 저자에 따르면 labeled 데이터가 매우 적을 경우 confidence-based masking과 softmax temperature controlling이 유용하고, 상대적으로 labeled 데이터가 많은 경우 entropy minimization이 효과가 있다고 합니다.

# Evaluation

Semi-supervised learning 성능을 평가하기 위해, 보통 Labled 데이터를 적게 사용하고 Unlabeld 데이터를 많이 사용해 테스트한다.

# Referecne

- [[Semi-supervised learning 방법론 소개](https://blog.est.ai/2020/11/ssl/)](https://blog.est.ai/2020/11/ssl/)
- [https://jiwunghyun.medium.com/semi-supervised-learning-%EC%A0%95%EB%A6%AC-a7ed58a8f023](https://jiwunghyun.medium.com/semi-supervised-learning-%EC%A0%95%EB%A6%AC-a7ed58a8f023)
- [https://seewoo5.tistory.com/8](https://seewoo5.tistory.com/8)
- [https://amitness.com/2020/07/semi-supervised-learning/](https://amitness.com/2020/07/semi-supervised-learning/)
- [https://medium.com/@nainaakash012/meta-pseudo-labels-6480acb1b68](https://medium.com/@nainaakash012/meta-pseudo-labels-6480acb1b68)
- [http://dsba.korea.ac.kr/seminar/?mod=document&uid=248](http://dsba.korea.ac.kr/seminar/?mod=document&uid=248)
