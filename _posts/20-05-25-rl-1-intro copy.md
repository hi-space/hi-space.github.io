---
title: "[RL] 1. Introduction"
category: AI
tags: ai reinforcement-learning
mathjax_autoNumber: false
---

<!--more-->

# 1. Introduction

![](/assets/images/20-05-25-rl-1-intro-intro.png)

강화학습 : 주어진 상황에서 어떠한 행동을 취할지를 학습하는 것

강화학습은 Supervised Learning, Unsupervised Learning 과 함께 Machine Learning의 한 분야이다.

* Supervised Learning은 label이 포함되어 있는 데이터들을 통해서 학습하는 것으로, unseen data에 대해서도 예측할 수 있도록 하는 것이 목표이다. 이 방식은 상호작용이 필요한 문제에는 적합하지 않다. 상호작용 문제에서는 경험의 의거하여 바람직한 행동을 선택해야하기 때문에, 모델은 새로운 환경이 주어졌을 때 미지의 영역에서의 자신의 경험에 의거하여 배울 수 있어야 한다. 
* Unsupervised Learning은 label이 없는 데이터의 집합 안에서 숨겨진 구조를 찾아낸다. 강화학습은 보상을 최대로 하기 위해 노력하지만 숨겨진 구조를 찾으려고 하지 않는다. 구조를 찾아내는 것이 어느정도 강화학습에 도움이 될 수는 있지만, 구조를 파악한다고 항상 최대의 보상을 얻는 선택을 할 수 있는 것은 아니다.

다른 learning 과 달리 강화학습에서는 exploration과 exploitation 사이의 trade-off 를 하는 것이 중요한 문제 중 하나이다. 이미 경험한 행동들을 활용\(exploitation\) 해서 큰 보상을 주는 행동을 취해야 하지만 동시에 더 좋은 행동을 선택하기 위해 탐험\(exploration\) 해야 한다. 

강화학습의 또 하나의 key feature는, 미지의 환경과 interaction 하는 agent 를 학습할 때, 필요한 모든 sub-problem들을 전체적으로 고려한다는 것이다. 하나의 큰 문제를 풀기 위해선 여러가지 풀어야 하는 작은 문제들이 있을 수 있는데 이것들을 포괄하여 학습하는 것이다. goal-seeking agent를 명확하게 정의하고 환경과의 interaction을  통해 planning과 control이 실시간으로 작용되도록 해야한다.

예를 들어 실내 주행하는 로봇을 만든다고 했을 때, 카메라를 통해 전방의 물체를 인식하고 판단하거나 원하는 목적지까지 주행하기 위해 바퀴의 토크값을 얼마나 줄 것인지 등의 하위 문제들이 있을 수 있다. 여기에서 가장 큰 문제는 목적지까지 로봇이 정상적으로 주행하는 것이지만, 그 문제를 해결하기 이전에 인식의 정확도를 높이거나 정밀한 컨트롤을 하기 위해 연구가 필요할 수 있다. 강화학습은 위와 다른 접근법으로, 이러한 하위 문제들을 완벽하게 planning 하지 않고도 환경과 상호작용하며 적절히 주행할 수 있는 방법을 학습하려고 노력할 것이다.

강화학습으로 푸는 문제들은 학습하려고 하는 학습자와 그를 둘러싼 주변 환경 사이의 interaction 이 있다. 주변 환경에는 uncertainty 요소들이 있지만, 학습자는 goal을 이루기 위한 방법들을 모색한다. 당장 주어진 상황에서 어떻게 선택을 해야 하는지 판단하고, 그 판단으로 인해 미래의 환경이 어떻게 달라져서 학습자에게 어떻게 영향이 오는지 까지 고려해서 선택을 계획해야 한다.

* Trial and Error
* Optimal Control

## Characteristics of Reinforcement Learning

* supervisor가 없고 reward signal 만 존재한다. 
* Feedback이 즉각적이지 않다. 여러가지의 행동을 취한 뒤 지연된 피드백을 받게 될 수 있다.
* 시간 독립적이지 않고 sequential 하다.
* Agent의 action 이후의 상태 데이터에 영향을 미친다.

## Elements of Reinforcement Learning

학습자와 환경을 제외하고도 네가지 주요한 구성요소가 있다. `policy`, `reward signal`, `value function`, 환경에 대한 `model` \(optional\)

### 정책 \(Policy\)

특정 시점에 학습자가 취하는 행동을 정의. 학습자가 환경을 보고 어떤 행동을 취해야 하는지 알려주는 것으로, 정책을 통해 학습자의 행동을 결정할 수 있다. 

정책은 간단한 함수, look up table 이 될 수도 있고, 각 행동이 선택될 확률을 부여하고 그 확률에 따라 행동을 선택할 수도 있다\(확률론적으로 선택\)

### 보상 신호 \(Reward signal\)

강화학습이 성취해야 할 목표를 정의. 매 시간마다 환경은 학습자에게 reward라고 하는 하나의 숫자 signal을 전달한다. 학습자는 reward signal을 통해 자신의 취한 행동이 좋은 것인지 나쁜 것인지 판단할 수 있다.

정책이 선택한 어떤 행동을 해서 적은 reward signal을 받게되면, 다음번 유사한 환경에서는 정책이 바뀌어 다른 선택을 하도록 유도할 수 있다. 

일반적으로 reward signal은 환경의 상태와 취해진 행동에 대해 확률적으로 결정되는 stochastic 함수가 될 수 있다.

### 가치 함수 \(value function\)

reward signal 은 어떤 행동에 대해 즉각적으로 알려주는 반면, value function은 장기적인 관점에서 무엇이 좋은 선택인지 알려준다.

특정 상태의 value는 특정 시점 이후부터 일정 시간 동안 학습자가 기대할 수 있는 reward의 총량이다. 일정 시간 동안의 상태를 고려해 long-term으로 평가한 지표이다.

value는 reward에 대한 예측으로, value가 필요한 것은 더 많은 reward를 얻기 위함이다. 행동을 선택함에 있어서 가장 중요한 것은 reward 지만, reward가 최대인 행동을 하기 위해선 당장의 reward가 높은 행동을 취하기 보다는 value가 최대인 값을 선택해야만 장기적으로 최대한 많은 reward를 얻을 수 있다. 

reward는 당장 환경에서 나오는 데이터들을 토대로 쉽게 얻을 수 있지만 value를 정의하는 것은 그보다 어려운 일이다. 학습자의 관측값을 통해 반복적으로 추정되어야 한다. 이 부분이 강화학습에서 가장 핵심적인 역할을 한다.

### 환경 모델 \(Model\)

Environment Model은 환경의 변화를 모사한다. 현재 상태와 그에 따라 취해지는 행동으로부터 다음 상태와 보상을 예측한다. 환경이 어떻게 학습자의 행동에 반응할 것인지를 예측하기 위해 만든 것으로 imagination\(simulation\) 하기 위함이다.

실제 상황을 경험하기 이전에 일련의 행동을 결정\(planning\)하기 위해 사용된다.  Planning은 Model을 통해 imagination\(simulation\) 하여 어떠한 policy를 만들고 향상시키는 과정을 뜻한다.

* `model-based` : model과 planning을 사용하여 강화학습 문제를 해결하는 방법. agent가 model을 가지고 있다.  \(ex, Q-Planning, Rollout Algorithms, Monte-Carlo Tree Search, SARSA, Actor-Critic\) 
* `model-free` : 시행착오를 통해 환경 모델을 학습하고 동시에 그 모델을 사용하여 계획하는 방법 \(ex, Q-Learning, DQN, A3C\)

Model-based는 planning에, Model-free는 learning에 중점을 두고 있다.

## Multi-Armed bandit problem

### A K-armed Bandit Problem

다음과 같은 문제가 있다.

![](/assets/images/20-05-25-rl-1-intro-2021-11-02-14-22-57.png)

외팔이 강도\(one-armed bandit\)는 앞의 여러대\(`k`\)의 슬롯머신 레버\(`arm`\)를 당겨, 그에 해당하는 보상\(`reward`\)을 얻을 수 있다. 단, 한번에 한개의 레버만 당길 수 있고 각 슬롯머신의 보상은 다르다. 이 보상은 선택된 행동에 따라 결정되는 고정 확률 분포\(`stationary probability distribution`\)로부터 얻어진다. 이 문제에서 주어진 시간 \(`time step`\) 동안 보상을 최대화 할 수 있는 정책\(`policy`\)을 배우고자 한다.

k-armed bandit problem에서 k개의 행동 각각에는 그 행동이 선택됐을 때 평균 보상값\(mean reward\)이 얻어진다. 이를 k action에 대한 `value` 라고 한다. 

$$ q_*(a) \doteq \mathbb{E}[R_t | A_t = a] $$

* $$A_t $$        :  시간 단계 $$t$$에서 선택되는 action
* $$R_t$$        :  위의 action을 취했을 때 얻어지는 reward
* $$ q_*(a)$$  :  행동 $$a$$가 선택됐을 때 얻어지는 reward의 expectation
* $$Q_t(a)$$ :  시간 $$t$$에서 추정한 행동 $$a$$의 reward

모든 action에 대한 reward를 알고 있다면 항상 높은 reward를 주는 action만을 선택하면 되기 때문에 k-armed problem 문제는 쉽다. 하지만 보통 action에 대한 reward를 추정할 수는 있으나 정확하게 알 수는 없다. 그래서$$Q_t(a)$$ 가 기대값 $$ q_*(a)$$와 가까워질 수록 정확한 추정이 될 수 있다. 이렇게 value를 추정할 수 있다면 각 시간 $$t$$마다 value가 최대인 action을 결정 할 수 있다. 

최대의 value 값을 선택하는 행동을 greedy 한 행동이라고 할 수 있다. greedy action을 선택하는 것은 현재까지 갖고 있는 지식을 exploiting 하는 것이다. 그와 반대로 현재까지 가지고 있는 지식을 활용하지 않고 non-greedy 한 action을 선택하는 것을 exploring 이라고 한다. 당장의 action에 대해 최대의 reward를 얻고자 하면 greedy한 action을 선택하는 것이 맞지만, 장기적으로 봤을 때 reward의 총합을 최대화 하기 위해선 exploration이 더 좋은 선택일 수 있다. exploration을 통해 더 좋은 action을 찾아내고 그를 더 많이 활용할 수 있는 기회가 생길 수 있기 때문이다. 

이는 위의 k-armed problem에 빗대어 생각해볼 수 있다. 슬롯머신마다 reward가 다른데 한번에 한 슬롯머신의 reward만 확인할 수 있기 때문에, 최대의 reward를 주는 슬롯머신을 탐색해야 한다. 랜덤하게 몇개의 슬롯을 당겨본 후 그 지식을 토대로 greedy한 선택을 할 수도 있고 \(exploitation\) 더 많은 정보를 얻기 위해 새로운 arm을 선택할 수도 있다.\(exploration\) 

* exploitation이 크면, 다른 더 좋은 reward를 가진 슬롯을 선택할 기회가 없어진다.
* exploration이 크면, 충분한 정보를 가지고 있더라도 정보를 더 얻기 위한 불필요한 비용이 발생한다.

결국 시간은 제한되어 있기 때문에 적절히 exploitation과 exploration간 적절한 균형을 유지해야 한다. 이 균형을 찾기 위한 것이 multi-armed problem의 핵심이다.

## The RL Problem

### Reward

$ R_t $: $t$ 번째 시간에 agent가 얼마나 잘했는지 알려주는 scalar 피드백 시그널 

* reward를 최대화 하는 것이 agent의 목표이다.
* RL은 reward hypothesis를 기반으로 한다.
  * **Reward Hypothesis :** All goals can be described by the maximisation of expected cumulative reward \(축적된 reward를 극대화\)

Sequential Decision Making 해야 한다. 연속적으로 선택을 해가면서 reward의 총합을 최대화 하는 것이 목표이기 때문에 long-term으로 생각해야 한다. 

### Agent and Environment

![](/assets/images/20-05-25-rl-1-intro-agent-and-env.png)

Agent가 어떤 Action을 하게 되면 Environment는 그 행동에 대해 달라진  observation과 reward를 반환해준다.

매 time step 마다 agent는 

* $$A_t$$: Executes action
* $$O_t$$: Receives observation
* $$R_t$$: Receives scalar reward

Environment는 

* $$A_t$$: Receives
* $$O_{t+1}$$: Emits observation
* $$R_{t+1}$$: Emits scalar reward

### History and State

$$ H_t = O_1, R_1, A_1, ... , O_t, R_t $$

$$t$$시간까지의 observation, reward, action 의 history를 나타낸다. 이 history를 보고 agent는 action을 선택하고, environment는 observation과 reward를 선택한다.

$$S_t = f(H_t)$$

State는 다음에 무엇을 할 지 결정할 때 사용되는 정보로, history의 function으로 나타낼 수 있다. history의 정보들을 가공해서 State를 표현한 것이다.

### Environment State

Environment State\($$ S_t^e$$\)는 다음 observation과 reward를 결정할 때 사용되는 environment의 internal한 정보들이다. visual 하게 표현되는 어떤 게임을 예로 들면, 현재 화면에 렌더링 되는 픽셀값들이 environment state가 될 수 있다. agent가 이 정보를 봤을 때는 그저 raw한 숫자들의 나열이겠지만, environment는 이 state들을 바탕으로 observation과 reward 값을 만들어낼 수 있다.  

### Agent State

Agent State \($$ S_t^a$$\)는 다음 action을 선택할 때 필요한 정보들이다. history의 정보들을 보고 필요한 정보들을 택해서 agent state로 사용할 수 있다. \($$S_t^a = f(H_t)$$\)

### Information State \(Markov state\)

어떤 State가 Markov 하다는 것은 바로 이전 state 에만 의존해서 결정을 하는 것을 말한다. 과거의 정보들과는 독립적이다. 이를 수식으로 나타내면 아래와 같다. 아래의 수식이 만족될 경우 state가 Markov 하다고 할 수 있다.

$$
\mathbb{P}[S_{t+1} | S_t] = \mathbb{P}[S_{t+1} | S_1, ..., S_t]
$$

$$S_t$$가 주어졌을 때 $$S_{t+1}$$ 로 갈 확률은 $$S_1$$부터 $$S_t$$까지 주어졌을 때 $$S_{t+1}$$로 갈 확률과 동일하다. 즉, $$S_1$$부터 $$S_t$$까지 주어져 있더라도어도 $$S_{t+1}$$까지 갈 확률은 $$S_t$$에 의해서만 결정된다는 의미이다.

$$
H_{1:t} \longrightarrow S_t \longrightarrow H_{t+1:\infty}
$$

위와 같이도 표현할 수 있다. history가 1에서 t까지 있을 때, 이것이 $$S_t$$를 결정하고, 이 $$S_t$$는 그 이전 history 값들을 사용하지 않고 미래를 결정한다는 의미이다. 

environment state는 Markov 하다고 할 수 있다. environment state를 통해 observation, reward 값을 만들어냈다면 이제 그 이전의 값들은 필요하지 않다. history 도 Markov 하다고 할 수 있다.

### Observable Environments

#### Fully Observability

$$ O_t = S_t^a = S_t^e $$

Agent state가 environment state와 동일한 경우 Fully observability 하다고 표현할 수 있다. 이를 Markov Decision Process \(`MDP`\) 라고 한다.

#### Partial Observability

Agent state와 Environment state가 동일하지 않을 경우 partial Observability 하다고 한다.

* 로봇이 카메라를 통해 어딘가를 향해 보고 있을 때 절대적인 위치는 알 수가 없다
* 포커를 칠 때 player는 상대방의 카드는 알 수가 없다

이를 Partially Observable Markov Decision Process\(`POMDP`\) 라고 한다.

이 때 Agent는 state를 어떻게 표현할 것인지를 정해야  한다. history를 그대로 사용할 수도 있고, 어떤 함수를 통해 표현할 수도 있다. 

## Major Components of an RL Agent

RL Agent는 아래와 같은 component를 하나 이상 가지고 있다.

* Policy : Agent의 행동을 결정하는 function
* Value function : 각 state 와 action이 얼마나 좋은지 평가하는 지표
* Model : 환경에서 나오는 agent의 representation

### Policy

Agent의 행동을 결정하는 것으로, state를 통해 action을 받을 수 있다.

#### Deterministic Policy

$$ a = \pi(s) $$

state 를 넣었을 때 action 하나가 결정적으로 정해진다.

#### Stochastic Policy

$$ \pi(a|s) = \mathbb{P}[A_t = a |S_t=s] $$

여러가지 action이 나올 수 있는데 그 action의 확률을 넘겨준. 확률에 따라 하나의 action을 선택한다.

### Value Function

미래 reward의 합산을 예측해 주는 것으로, 현재 state가 좋은지 안좋은지 평가하는데 사용된다.

$$
v_{\pi}(s) = \mathbb{E}_{\pi}[R_{t+1} + \gamma R_{t+2} + \gamma^2 R_{t+3} + ... | S_t = s]
$$

value function은 시간 $$t$$에 state가 주어졌을 때 policy $$\pi$$를 통해 나온 reward들의 총합의 기댓값이다. policy가 stochastic 할 수도 있고 environment가 stochastic 할 수도 있다. 확률에 따라 선택할 수 있는 값들이 달라질 수 있기 때문에 기대값을 취해주는 것이다. $$\gamma$$는 discount factor로, 미래로 갈 수록 값을 작게 해서 더해주기 위함이다.

value function은 optimal policy를 따랐을 때 value가 어떻게 되는 지를 표현하는 것이다.

### Model

model은 환경이 어떻게 될 지 예측한다. 다음 state와 reward를 예측할 수 있다.

$$ P_{ss'}^a = \mathbb{P}[S_{t+1} = s' | S_t = s, A_t = a]$$

$$R_{s}^a = \mathbb{E}[R_{t+1} |S_t = s, A_t = a]$$

## Categorizing RL agents

![](/assets/images/20-05-25-rl-1-intro-category.png)

### Value Based vs Policy Based vs Actor Critic

|  | Value Based | Policy Based | Actor Critic |
| :---: | :---: | :---: | :---: |
| **Policy** | X | O | O |
| **Value Function** | O | X | O |

### Model Free vs Model Based

|  | Model Free | Model Based |
| :---: | :---: | :---: |
| **Policy / Value Function** | O | O |
| **Model** | X | O |

Model Free Agent는 내부적으로 Model을 만들지 않고, Model Based Agent는 환경에 대한 Model을 만들고 그에 맞게 행동하는 Agent 이다.

### Learning and Planning

sequential decision making 에는 Learning과 Planning 문제가 있다. 우리가 일반적으로 말하는 RL 에는 두가지 컨셉으로 나뉜다고 볼 수 있다.

![](/assets/images/20-05-25-rl-1-intro-learning.png)

#### \(Reinforcement\) Learning

* 환경을 모른다.
* agent는 환경과 interact 한다.
* agent는 policy를 향상시킨다.
* agent는 환경을 모르는 상태에서 환경과 interaction 하며 policy를 개선시켜 나간다.

#### Planning

* 환경을 안다. \(reward와 transition \(state를 통해 어떻게 움직일 것인지\) 이 어떻게 되는 지 안다\)
* 환경과 함께 작용하지 않더라도 이미 정보가 다 있기 때문에 computation 만으로 policy를 개선시켜나간다. \(ex, monte carlo\)
* 다음 state에 대해 query를 날려서 computation 결과를 얻을 수 있고, 그 값들을 통해 optimal policy를 찾아나간다. \(ex, tree search\)
* a.k.a\) deliberation, reasoning, introspection, pondering, thought, search

## Exploration and Exploitation

RL은 trial-and-error 를 통해 좋은 policy를 찾아나가는 과정이다. 이 과정에 크게 두가지가 필요하다.

* Exploration : 환경으로부터 정보를 얻어나가는 과정
* Exploitation : 알고있는 정보들을 통해 최선의 선택을  하는 과정

이 둘은 trade-off 관계에 있다. exploitation 만 하면 새로운 정보를 얻을 기회를 놓치는 것이고, exploration만 하면 새로운 선택을 통해 장기적으로 더 좋은 선택을 할 수도 있으나 당장은 좋은 선택이 아닐 수 있다. 

## Prediction and Control

Prediction 문제가 있고 Control 문제가 있다.

#### Prediction 

미래를 평가하는 문제이다. Value function을 학습시키는 것이다. 

Uniform Random policy로 계속 랜덤하게 돌아다니 reward를 받을텐데, 각 state에서 출발해서 충분히 오래 했을 때 이 future reward 의 합이 얼마나 될까, 이게 value function을 학습시키는 방법이다.

#### Control

미래를 최적화하는 문제이다. best policy를 찾는다. state에서 어떻에 움직여야 하는지 찾는 것이 Control 문제이다. 

## Reference

- [https://www.davidsilver.uk/wp-content/uploads/2020/03/intro\_RL.pdf](https://www.davidsilver.uk/wp-content/uploads/2020/03/intro\_RL.pdf)

