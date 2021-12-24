---
title: Simplifying Semi-Supervised Learning with Consistency and Confidence (FixMatch)
category: AI
tags: ai paper
article_header:
    type: overlay
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/21-10-03-fixmatch-diagram.png
---

- [Paper](https://arxiv.org/ftp/arxiv/papers/2001/2001.07685.pdf)
- [Github](https://github.com/google-research/fixmatch)
- Semi-supervised learning에서 Pseudo-labeling과 consistency regularization을 결합한 방법
  
<!--more-->

# Methods

아주 적은 양의 labeled data로 모델을 학습시키고 unlabeld image에 대하여 weakly-augmented를 적용하여 모델에 넣는다. 그렇게 얻어낸 prediction에서 pseudo-label을 뽑아내고 다시 같은 이미지에 대해 strongly-augmented를 적용하여 얻어낸 prediction과 앞서 얻어낸 pseudo-label이 비슷하도록 학습을 시킨다.

![](/assets/images/21-10-03-fixmatch-diagram.png)

1. Unlabeled 데이터를 Weakly-augmentation 하고 모델을 통해 prediction 값을 얻어낸다.
2. Class probability에서 threshold 값을 넘는 클래스를 one-hot pseudo-label을 취한다. (해당 클래스의 확률을 1로)
3. 같은 이미지에 대해 Strong-augmentation 한다
4. Strong augmentation 이미지를 input으로 사용하여 얻어진 class probability 와(Cross-entropy loss), weaky augmentation에서 얻어진 pseudo label 결과와 함께 모델을 학습시킨다. 

# Related Works

## Consistency Regularization

Consistency Regularization의 기본 개념은 같은 이미지에서 변형을 주었으면 모델은 비슷한 예측치를 내야한다는데서 비롯한다. 아래 수식을 통해서 보면 $\alpha$  라는 같은 augmentation 방법을 부여하더라도 $\alpha$와 $p_m$이 결국에는 stochastic function, 즉 확률적인 함수이기 때문에 서로 다른 값을 가질 수 있게 된다. 50% 확률로 그림을 90도로 rotate 시키는 augmentation 기법을 적용한다고 했을 때 어떤 강아지 그림은 rotate가 되고 어떤 강아지 그림은 rotate가 되지 않을 수 있다는 것이다. 하지만 모델 입장에서는 두 그림 모두 '강아지'라고 인식을 하도록 만들어야한다는 것이 consistency regularization 개념이다.

$$ \sum_{b=1}^{\mu B} \parallel p_m(y | \alpha(u_b)) - p_m(y | \alpha (u_b)){\parallel}_2^2 $$

- $\hat q_b = argmax(q_b)$ : hard label
- $T$ : threshold hyperparameter

## Pseudo Labeling

Labeled data를 가지고 모델을 학습 시킨 후에 unlabeled data에 새로운 label을 부여한 pseudo-labeld data와 기존 labeled data를 가지고 모델을 최종 학습시킨다. 이 때, 모든 pseudo-labeled data를 사용하는 것이 아니라 사전에 지정한 threshold $T$를 넘는 label에 대해서만 진행한다.

$$ \frac{1}{\mu B} \sum_{b=1}^{\mu B}1(max(q_b)\geq T)H(\hat q_b, q_b) $$

## Augmentation

### Weak Augmentation

flip & shift를 사용
- flip의 경우 horizontal flip으로 prob은 0.5로 준다. 또한 translate는 12.5%로 수평,수직 방향 모두 움직인다.

### Strong Augmentation

- AutoAugment 적용(RandAugment or CTAugment) 후에 Cutout을 다시 적용
  - **RandAugment**: 왜곡의 정도를 결정하는 하나의 fixed global magnitude hyperparameter를 사용한다. 이 magnitude값은 validation set에 대해서 최적화되어야 하지만 저자들은 semi-supervised에서는 고정된 하나의 magnitude값을 사용하는 것보다 매 training step마다 미리 정의된 범위안에서 랜덤하게 샘플링하여 사용하는 것이 더 성능이 좋았다고 한다.
  - **CTAugment**: 변형의 정도를 결정하는 magnitude의 매우 넓은 범위를 bin들로 나눈다. 그리고 각 bin들에 대해 weight이 할당되는데 초기에는 모두 1을 가진다. 그러고는 이 weight값에 따라 확률을 가지며 랜덤하게 샘플링이 된다. 이 weight값은 학습도중에 update가 된다.


# References

- [https://2-chae.github.io/category/2.papers/29](https://2-chae.github.io/category/2.papers/29)
- [https://ainote.tistory.com/6](https://ainote.tistory.com/6)
- [https://cool24151.tistory.com/81](https://cool24151.tistory.com/81)