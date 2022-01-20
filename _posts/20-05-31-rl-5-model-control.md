---
title: "[RL] 5. Model Free Control"
category: AI
tags: ai reinforcement-learning
mathjax_autoNumber: false
---

<!--more-->

# 5. Model Free Control

이번 단원에서는 환경 없이 최적의 policy를 찾는 방법에 대해서 배워볼 예정이다. 이전 강의에서는 policy를 estimate 했지만, 이번에는 optimal을 찾을거다. 이번 장에서 배울 control 문제는 여전히 table lookup 방식을 사용하고 있는데, state가 많아지만 이런 방식으로는 풀 수 없다. 전체적인 reinforcement learning의 흐름을 배운다고 보면 된다. 나중에는 수많은 state에 대해 대응할 수 있는 function approximate을 배울 예정이다.

## Introduction

![](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-42-42.png)

MDP 모델을 모르거나, MDP 모델을 알더라도 문제가 너무 커서 sampling 해서 풀어야 할 경우, model-free control 방식을 사용한다. 

![](/assets/images/20-05-31-rl-5-model-control-On%20and%20Off-Policy%20Learning.png)

Model-Free Control 방식에는 On-policy 와 Off-policy 두가지 방식이 있다.

On-policy는 최적화 하고자 하는 policy 와 환경에서 경험을 쌓는 behavior policy 두가지가 같은 것이고, Off-Policy는 다른 policy가 쌓아놓은 경험으로부터 배우는 것이다. 그래서 "Look over someone's shoulder" 라고 표현한다.

## On-Policy learning

![](/assets/images/20-05-31-rl-5-model-control-Generalised%20Policy%20Iteration.png)

이전에 optimal policy를 찾는 과정인 policy iteration에 대해서 배웠다. 위의 방식을 사용해서는 model-free에서 사용할 수는 없을까? Monte-Carlo policy evaluation을 그대로 model-free에서도 적용할 수 있을까?

### Monte-Carlo Policy Evaluation

![](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-43-21.png)

$V$만 학습해서는 greedy 하게 선택을 할 수가 없다. model-free 환경이기 때문에 다음 state를 알 수 없기 때문이다. model을 알아야 $$V$$에 대한 greedy policy를 improvement 할 수 있다.

![Model-Free Policy Iteration Using Action-Value Function](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-43-31.png)

그렇다면 Q에 대해서 greedy policy improvement 할 수 있을까? 그렇다. $$V(s)$$는 못구하더라도 $$Q(s, a)$$는 구할 수 있다. 어떤 state에서 action을 취해보고 나온 return 값들의 평균을 구하기만 하면 되기 때문에 $$Q$$에 대한 greedy policy improvement는 할 수 있다. 

![Generalised Policy Iteration with Action-Value Function](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-44-17.png)

$Q$를 이용해서 하고 policy evaluation을 하고, policy improvement는 greedy 하게 선택하면 되는걸까? 아니다. 이 경우 greedy 하게만 움직이면 충분히 많은 곳을 가볼 수 없기 때문에 exploration이 되지 않는다.

![Example of Greedy Action Selection](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-44-41.png)

두가지 문 중 하나의 문을 선택하는 문제가 있다고 하자. left로 갔을 때의 value 값은 0이고 right 로 갔을 때의 value 값을 받는다고 했을 때, Greedy Action Selection 을 하게 되면 계속해서 left 문을 다시 시도하지 않고 right 문만 선택하게 된다. 

### $\epsilon$-greedy Exploration

![e-Greedy Exploration](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-49-02.png)

그래서 나온 것이 $\epsilon$-greedy Exploration 이다. $\epsilon$의 확률로 랜덤하게 다른 action을 선택하는 거다. 그럼 모든 action을 exploration 할 수 있다. 

![](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-49-32.png)

$\epsilon$-greedy 를 사용해도 policy가 improve 되는 것을 증명하는 수식이다.

![Monte-Carlo Policy Iteration](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-49-40.png)

이제 정리가 됐다. $Q$를 이용해 Monte-Carlo policy evaluation을 하고, $\epsilon$-greedy로 policy improvement 하면 Model-free 에서 control이 가능해진다.

### Policy Iteration

![Monte-Carlo Control](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-50-10.png)

이 과정을 더 빠르게 할 수 있는 방법이 있다. Monte-Carlo policy evaluation이 $$Q = q_{\pi}$$로 수렴할 때 까지 수행할 수도 있겠지만, evaluation이 끝날 때 까지 기다리지 않고 매 episode 마다 바로 improvement 하는 방법이 있다. \($$Q \approx q_{\pi}$$ -&gt; 근사적으로 같음을 뜻한\)

Policy Iteration에서는 Evaluation 이 수렴할 때 까지 했었는데, 그렇게 하지 않고 한번 evaluation 하고 Policy Improvement 해도  Optimal로 수렴할 수 있다. 

### GLIE

![GLIE](/assets/images/20-05-31-rl-5-model-control-glie.png)

1. 모든 state-action 페어들이 무한히 많이 반복되어야 한다. \(exploration\)
2. Policy는 결국 greedy policy에 수렴해야 한다. \(Exploitation\)

위의 두가지 성질은 trade-off 관계이지만 이 두가지 조건 모두를 만족하는 것이 GLIE 이다. 학습할 때 충분히 Exploration 했다면 Greedy policy에 수렴하는 것이 GLIE 이다. 

$\epsilon$-greedy policy는 항상 greedy 한 선택을 하는 것이 아니기 때문에 GLIE 하지 않다. 시간이 지나감에 따라 $$\epsilon$$값이 0으로 수렴하게 되면 $$\epsilon$$-greedy 또한 GLIE 가 될 수 있다. $$\epsilon$$을 $$\frac{1}{k}$$로 하게 되면 $$\epsilon$$-greedy 가 optimal policy에 수렴하는 것이 증명되어 있다. 

### Sarsa

위에서는 On-Policy control 할 때 MC를 사용했었다. 비슷한 control loop로 TD도 사용할 수 있다. TD를 사용하게 되면 이와 같은 이점이 있다.

* variance가 작다
* online learning 이 가능하다
* sequence가 끝나지 않더라도 학습이 가능하다

MC에서 TD로 바꾸는 것은 단순하다.

* $$Q(S, A)$$에 TD를 적용
* $$\epsilon$$-greedy policy improvement 적용
* 매 time-step 마다 update

![SARSA](/assets/images/20-05-31-rl-5-model-control-sarsa.png)

$$
Q(S, A) \leftarrow Q(S, A) + \alpha(R + \gamma Q(S', A') - A(S, A))
$$

TD\(0\)의 value function 을 action-value function으로 바꿔주면 Sarsa 가 된다. 

S에서 A를 하면 R을 받게 되고 S'에서 A'라는 액션을 하는 backup diagram이다. 현재 state-action pair에서 다음 state와 다음 action 을 보고 update 하기 때문에 위와 같이 표현할 수 있다. 매 time-step 마다 $$Q$$를 업데이트하고 바로 policy improvement 한 후, improve 된 policy로 이동하는 것을 반복한다.

![On-Policy Control With SARSA](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-51-11.png)

#### Sarsa Algorithm

![Sarsa Algorithm for On-Policy Control](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-51-45.png)

1. $S$를 initialize
2. $Q$ value로 $$S$$에서 $$A$$를 선택
3. 매 time-step 마다

   1. $A$를 선택해서 나온 $$R$$, $$S'$$를 얻는다
   2. $Q$value로 $$S'$$에서의 $$A'$$를 선택
   3. $R$, 와 다음 action의 $$Q$$value \($$Q(S', A')$$\)를 가지고 현재의 $$Q$$value를 업데이트 $$Q(S, A) \leftarrow Q(S, Q) + \alpha [R + \gamma Q(S', A') - Q(S, A)]$$

4. $S$가 끝날 때 까지 위의 과정 반복

이 과정으로 Sarsa가 진행된다. policy는 따로 정의하지 않고 $$Q$$를 보고 $$\epsilon$$-greedy하게 action을 선택하는 것 자체가 policy가 된다.

Sarsa가 optimal action-value function에 수렴\($$ Q(s, a) \rightarrow q_*(s, a)$$\)하기 위해서는 아래와 같은 조건을 만족시켜야 한다. 이론적으로는 그렇지만 practically 이런 조건을 만족시키지 않아도 잘 수렴하는 편이다.

* GLIE 해야 한다.
  * 모든 state-action pair에 방문해야 한다
  * greedy policy에 수렴해야 한다.
* step size가 Robbins-Monro 해야 한다.
  * $$\sum_{t=1}^{\infty} \alpha_t = \infty$$    : step size가 충분히 커야 한다.
  * $$ \sum_{t=1}^{\infty} \alpha_t^2 < \infty$$   : Q value를 수정하는 정도가 점점 작아져야 한다.

#### Windy Gridworld

![Sarsa on the Windy Gridworld](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-52-30.png)

* S에서 G까지 최적의 길찾기 문제
* 아래의 숫자만큼 바람이 불어 강제로 그 숫자만큼 위로 올라가게 된다.
* 매 time-step 마다 reward = -1
* Undiscounted

episode가 쌓일 수록 점점 더 빨리 기울기가 증가하는 것으로 보아, 목표에 더 빨리 달성됨을 알 수 있다. 이 때 Q를 array로 만드다면 state \* action 갯수의 크기가 필요하다. 

#### n-Step Sarsa

![n-Step Sarsa](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-52-46.png)

#### Forward View Sarsa\($$\lambda$$\)

![Forward View Sarsa\(lambda\)](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-53-06.png)

#### Backward View Sarsa\($$\lambda$$\)

![Backward View Sarsa\(lambda\)](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-53-21.png)

#### Sarsa\($$\lambda$$\) Algorithm

![Sarsa\(lambda\) algorithm](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-53-37.png)

#### Sarsa\($$\lambda$$\) Example

Sarsa는 action을 하고 선택하고 나서 그 해당 칸만 업데이트 했는데, Sarsa\($$\lambda$$\)는 그 action에 대한 모든 칸들을 업데이트 한다. 계산량은 많아질 수 밖에 없지만 그만큼 정보전파는 빨라진다.

![Sarsa\(lambda\) Gridworld Example](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-53-49.png)

GridWorld를 예시로 보면, one-step Sarsa 의 경우 goal에 도착하기 직전 값만 업데이트 한다. up 이라는 action이 좋다고 반영하는거다. Sarsa\($$\lambda$$\)는 goal에 도착한 순간, 도착한 과정들의 eligibility trace를 봐서 그 책임만큼 다같이 업데이트를 해준다.

## Off-Policy Learning

On-Policy Learning에서 action을 선택할 때 사용하는 policy와 그 policy를 평가하는 policy는 $$Q$$로 동일했다. Off-Policy Learning에서는 behavior policy와 평가를 하기 위핸 target policy가 다르다.

* $$v_{\pi}(s)$$ 나 $$q_{\pi}(s, a)$$를 계산해서 target policy인 $$\pi(a|s)$$를 evaluate 한다.
* behavior policy인 $$\mu(a|s)$$는 $$\{S_1, A_1, R_2, ..., S_t\} \sim \mu$$ 를 따른다.
* $$\mu$$ : behavior policy
* $$\pi$$ : target policy

이렇게 behavior policy와 target policy를 분리함으로써 생기는 이점들이 있다.

* 사람이나 다른 agent의 행동을를 보고 배운다.

> 누군가의 행동을 보고 배운다는 것이 supervised learning과 비슷한 느낌이 들긴 하지만 다른 개념이다. supervised learning은 그 agent를 보고 그대로 행동을 따라하는 것이라고 하면, off-policy에서는 그 행동을 보고 어떻게 행동을 할 지를 배우는 것이다. 어떤 상황에서 어떻게 행동을 해야 할 지 가르쳐주지 않더라도 스스로 다양한 경험을 토대로 행동하는 법을 배운다.

* old polices $$\pi_1, \pi_2, .., \pi_{t-1}$$로부터 만들어진 경험을 Re-use 할 수 있다.

> On-Policy는 하나의 경험이 생기면 그 경험으로 Policy를 업데이트 하고 그 이후에는 업데이트된 새로운 policy로 action을 선택하기 때문에 이전의 경험들은 사용될 수 없었다. Off-Policy는 경험한 policy가 그대로 남아있기 때문에 그 경험을 re-use 할 수 있게 된다.

* exploratory policy를 따르면서 optimal policy를 배우기 때문에, 탐험적으로 움직이면서 동시에 안전하게 optimal 한 policy를 배워나갈 수 있다. 
* 하나의 policy를 따르면서 multiple policy를 배울 수 있다.

이러한 Off-Policy Learning을 가능하게 하는 방법으로는 Importance Sampling과 Q-Learning 두가지가 있다.

### Importance Sampling

![Importance Sampling](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-54-16.png)

* $$X \sim P$$  : X 분포는 확률분포 P에서 샘플링 된 것
* $$f(X)$$ : 어떤 input 이 들어갔을 때 어떤 output이 나오는 임의의 함수

확률분포 $$P$$로 $$f(X)$$의 기댓값을 구함으로써 다른 확률분포 $$Q$$의 $$f(X)$$의 기댓값을 구하려고 한다. sample을 구하기 어려운 어떤 확률분포 $$Q$$가 있을 때, 상대적으로 sample을 구하기 쉬운 다른 확률분포 $$P$$를 이용해 $$Q$$의 기댓값을 구하는 것이다. $$P$$ 분포에서의 확률과 $$Q$$분포에서의 확률, 그 비율을 곱해주면 된다. 

![Importance Sampling for Off-Policy Monte-Carlo](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-54-29.png)

action을 할 때마다 $$\mu$$와 $$\pi$$의 비율들이 나오게 될텐데, 그 비율을 계속 곱해줌으로써 값을 교정해주는 것이다. action 수 만큼 교정 term이 있다고 보면 된다.

episode가 끝나고 return을 계산할 때 importance sampling을 하기 위해 매 step 마다 $$\pi/\mu$$를 해줘야 한다. 각 step 에 받은 reward는 $$\mu$$라는 policy를 따라서 얻어진 값이기 때문에 $$\pi$$ 라는 policy를 따랐을 때의 reward를 계산해야 하기 때문이다.

하지만 Monte-Carlo에서 이 방법은 사용할 수가 없다. term의 variance가 너무 커서 현실적으로 사용하기 어렵다. 그래서 MC 대신 TD를 사용한다.

![Importance Sampling for Off-Policy TD](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-54-42.png)

action 하나에 대해 target policy에서 behavior policy를 나눈 비율을 곱해준다. 매 step 마다 연산해줘야 하는 MC보다 variance가 훨씬 적어서 사용할 수 있다. 하지만 importance sampling을 쓰기 때문에 원래 TD 보다는 variance가 높다. 그래서 나오게 된 것이 Q-Learning 이다.

### Q-Learning

importance sampling을 사용하지 않으면서, action-value $$Q(s, a)$$를 이용해 Off-Policy learning을 하려고 한다.

1. 현재 state 에서 behavior policy로 다음 action 을 선택한다.   
 $$A_{t+1} \sim \mu(\cdot|S_t)$$

2. 다음 state 에서 action을 선택할 때에는 behavior policy 대신 target policy \(alternative policy\) 를 이용해 action을 선택한다.   
  $$A' \sim \pi(\cdot|S_t)$$

3. target policy로 action을 선택하고, 그 value값을 사용해 update 한다.  $$Q(S_t, A_t) \leftarrow Q(S_t, A_t) + \alpha[R_{t+1} + \gamma Q(S_{t+1}, A') - Q(S_t, A_t)]$$

Importance Sampling을 이용한 Off-Policy에서는 value function을 사용했지만 Q-Learning에서는 action-value function을 사용한다.

behavior policy로 이동을 하고, 다음 state에서의 추측치 값을 사용해 $$Q$$를 update 해줘야 하는데, 그 추측치값을 behavior policy 대신 target policy로 구하는 것이다. 어차피 추측치이기 때문에 behavior policy 대신 target policy를 사용해도 문제가 되지 않는다. 

#### Off-Policy Control with Q-Learning

target policy 뿐만 아니라 behavior policy도 함께 improve 되면 당연히 더 좋다. 그래서 일반적으로 Q-Learning에서는 아래와 같이 policy를 improvement 한다.

* target policy $$\pi$$        :  $$Q(s, a)$$에 대해 greedy improvement
* behavior policy $$\mu$$   :  $$Q(s, a)$$에 대해 $$\epsilon$$-greedy improvement

행동을 하는 policy는 $$\epsilon$$하기 때문에 다양하게 경험을 쌓을 수 있고\(exploration\) 실제 target policy는 greedy하게 선택하기 때문에 exploitation도 만족시킬 수 있다. 

$$
\begin{matrix}
R_{t+1} + \gamma Q(S_{t+1}, A')  
&=& R_{t+1} + \gamma Q(S_{t+1} , \underset{a'}{argmax} Q(S_{t+1}, a')) \\
&=& R_{t+1} + \underset{a'}{max} \gamma Q(S_{t+1}, a')
\end{matrix}
$$

Q-Learning 을 수식으로 나타내면 위와 같다.

![Q-Learning Control Algorithm](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-55-02.png)

Sarsa 는 $$Q$$ policy에서 하나의 action만 선택하는 것이였는데, Q-Learning에서는 action 중에서 max 값을 선택해서 업데이트하기 때문에 Sarsa max 라고 부르기도 한다.

#### Q-Learning Algorithm

![Q-Learning Algorithm for Off-Policy Control](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-55-30.png)

## DP vs TD

![Relationship Between DP and TD](/assets/images/20-05-31-rl-5-model-control-2022-01-20-21-55-34.png)
