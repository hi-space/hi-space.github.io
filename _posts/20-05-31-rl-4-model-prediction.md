---
title: "[RL] 4. Model Free Prediction"
category: AI
tags: ai reinforcement-learning
mathjax_autoNumber: false
---

<!--more-->

# 4. Model Free Prediction

## Introduction

MDP를 모르는 환경에 agent가 던져졌을 때 어떤 방식으로 prediction 하고 control 해야할까? 이것이 environment를 모르고 policy가 정해져있을 때 value를 찾는 문제이다. 즉, episode가 끝날 때 return 값을 알아내야 한다. 

이런 경우 경험으로부터 직접 배워가야 한다. Monte Carlo Learning, TD Learning 을 통해 위와 같은 문제를 풀려고 한다. value function을 추정하고 optimal policy를 찾는 방법들이다. 

DP처럼 모든 state transition probability를 알 필요 없다. DP는 MDP에 대한 정보로부터 value function을 계산\(compute\)했지만, 여기에서는 MDP의 return 표본들을 통해 value function을 학습\(learn\)한다.

## Monte-Carlo Learning

Monte-Carlo는 random하게 무언가를 취해보고 추정하는 방법이다. 

> MC는 sample return의 평균값을 통해 RL 문제를 푸는 방법이다. 경험이 여러개의 episode로 분할되고 언젠가는 종료된다고 가정했을 때, 하나의 episode가 완료된 후에만 value estimation과 policy가 변화한다.

직접 구하기 어려운 값들을 계속 시행하면서 나오는 값들을 통해 추정하는 방법이다. 어떤 state 에서 value의 기댓값이 어떻게 되는지 측정한다. 매 episode가 끝날 때 나오는 return 값들이 다를텐데 그 return 값들을 평균내어 사용한다. 

> 어떤 state의 value는 그 state를 시작점으로해서 계산된 expected return 값\(미래 discounted reward의 누적 기댓값\)이다. 그렇기 때문에 경험으로부터 state의 value를 추정하는 방법은, 그 state 이후에 방문한 모든 state의 return 값들의 평균을 구하는 것이다. 얻어지는 return 값이 많아질 수록 그 평균값은 기댓값 수렴하게 된다. \(MC의 기본 idea\)

* episode의 경험으로부터 직접적으로 학습한다
* MDP transition과 reward를 모르는 Model-free 방식이다
* episode가 끝나야 return 값이 나오고, 학습할 수 있다 \(no bootstrapping\)
* value = mean return
* episodic MDP에만 MC를 적용할 수 있다 \(모든 episode는 terminate 되어야 한다\)

### Monte-Carlo Policy Evaluation

> **\[Goal\]** policy가 $\pi$일 때, episode의 경험로부터 $v_{\pi}$를 학습시킨다.
> $$
> S_1, A_1, R_2, ..., S_k \sim \pi
> $$

Policy Evaluation 이니 Prediction 문제이다.

Monte-Carlo policy evaluation은 expected return 값 대신에 empirical mean return 값을 이용한다.  episode를 끝까지 가본 후에 얻어지는 return 값들로 각 state의 value function을 거꾸로 계산해보는 방식이기 때문에 episode가 끝나지 않으면 사용할 수 없는 방법이다.

initial state 부터 terminal state 까지 policy $\pi$를 따라서 가다보면 time step 마다 discounted reward를 받을텐데, 그 reward 값을 기억해뒀다가 $S_t$가 되면 뒤돌아보며 각 state의 value function을 계산한다. 

Recall the `return` \(total discounted reward\)

 $$ G_t = R_{t+1} + \gamma R_{r+2} + ... + \gamma^{T-1} R_T $$

Recall the `value function` \(expected return\)

 $$ v_{\pi}(s) = \mathbb{E}_{\pi} [G_t | S_t = s] $$

#### First-Visit / Every-Visit

한 episode에서 state $s$를 방문했을 때 그것을 `visit` 이라고 한다. $s$의 visit 이후에 발생하는 return 의 평균을 구함으로써 $v_{\pi}(s)$를 추정한다.

`First-visit MC method` : 해당 state의 첫 방문때에만 count 해주고, 그 이후는 무시하고 return값 에 더해주기만 한다.

`Every-visit MC Method` : 해당 state에 방문할 때마다 count 해준다.

1. 방문한 횟수 증가 

   $$ N(s) \leftarrow N(s) + 1 $$

2. total return 값을 더해줌

   $$ S(s) \leftarrow S(s) + G_t $$

3. return 의 평균값 \(total return 의 합 / 방문한 횟수\) 으로 value를 estimate 한다

   $$ V(s) = S(s) / N(s) $$

4. 방문 횟수가 무한대로 갈 수록 return 평균값은  value 값에 수렴한다.

  $$ V(s) \rightarrow v_{\pi}(s)  \quad  as  \quad N(s) \rightarrow \infty $$

First-visit, every-visit 둘 중 무엇을 써도 상관은 없다. visit의 갯수\($N(s)$\) 가 무한으로 갈 수록 수렴하게 된다. 다만 모든 state를 다 방문해야 한다는 것이 가정되어야 한다. 모든 state를 evaluate 하는 것이 목적이기 때문에, 어떤 state에 방문하지 않으면 해당 state는 평가하지 못한다.

### Incremental Monte-Carlo Updates

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-34-45.png)

MC는 여러개 시도해보고 episode가 끝난 후 그것을 평균내는 방식이다. 나중에 평균내기 위해서는 episode마다 이전의 state 값들을 전부 저장해놓고 값을 계산해줘야 하지만, Incremental Mean을 사용하면 그때그때 평균을 구하며 새로운 값이 있을 경우 그 값을 통해 교정해주기 때문에 이전에 계산된 모든 값들을 저장할 필요가 없다. Incremental 하게 평균값을 구해주는 것이다.

$x_1, x_2, ..$의 연속된 입력값이 주어지고 이들의 평균 $\mu_1, \mu_2, ..$을 구할 때 위와 같이 전개할 수 있다.

$$
\mu_k = \mu_{k-1} + \frac{1}{k}(x_k - \mu_{k-1})
$$

이를 이용해 MC에서 적용해보자. state $$ S_t $$ 에서 return 값이 $G_t$라고 하면,

$$
N(S_t) \leftarrow N(S_t) + 1 \\
V(S_t) \leftarrow V(S_t) +  \frac{1}{N(S_t)} G_t - V(S_t)
$$

이와 같이 나타낼 수 있다. $G_t - V(S_t)$는 $s$에서의 error term으로, error 만큼 더해주는 것으로 볼 수 있다. 방문하는 state 의 수가 증가함에 따라 $\frac{1}{N(S_t)}$는 점점 작아지게 될텐데, 이 값을 작은 값 $\alpha$로 고정할 수 있다. 그럼 오래된 기억은 잊게 된다.

$$
V(S_t) \leftarrow V(S_t) + \alpha(G_t = V(S_t))
$$

non-stationary 문제에서 과거의 기억은 잊고 최신의 경험만 기억하고 싶을 때 사용한다.

## Temporal-Difference Learning

TD는 MC 와 DP 방법을 결합한 것이다. MC 처럼 경험으로부터 직접적으로 학습하고, DP 처럼 episode가 끝나지 않더라도 학습할 수 있다. 

DP, MC, TD 모두 GPI \(Generalized policy iteration\)를 일부 변형한 것으로, prediction 문제에 대한 접근법의 차이다. 

* episode의 경험으로부터 직접적으로 학습한다
* MDP transition과 reward를 모르는 Model-free 방식이다
* episode가 끝나지 않더라도 학습한다 \(bootstrapping\)
* 예측을 통해 예측값을 업데이트 한다

> **\[Goal\]** policy가 $$ \pi $$일 때, episode의 경험로부터 online $$ v_{\pi} $$를 학습시킨다

Monte-Carlo 에서는 actual return인 $$G_t$$ 방향으로 $$V(S_t)$$를 업데이트 했다.

$$ V(S_t) \leftarrow V(S_t) + \alpha ({\color{RED} G_t} - V(S_t)) $$

TD에서는 \(TD\(0\)이라고 가정\) estimated return인 $$R_{t+1} + \gamma V(S_{t+1})$$방향으로 $$V(S_t)$$를 업데이트 한다. \(estimated return을 TD target이라고 부른다\) 즉, 현재 value function을 계산하는데 이전의 주변 state들의 value function을 사용하는 것이.

$$V(S_t) \leftarrow V(S_t) + \alpha( {\color{RED} R_{t+1} + \gamma V(S_{t+1})} - V(S_t))$$

* `TD target` : $R_{t+1} + \gamma V(S_{t+1})$
* `TD error` : $R_{t+1} + \gamma V(S_{t+1}) - V(S_t)$

#### Driving Home Example

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-36-32.png)

한 스텝을 가보고 거기서 예측하는 예측치를 보고 그방향으로 v 를 업데이트 이전에 예측한 것보다 더 정확할거아냐? 현실이 더 반영되어 있으니까? MC는 정확한 값으로 업데이트하는 건데 TD는 예측치로 예측하니까 에러가 더 있지 않을까?

### TD vs MC

<table>
  <thead>
    <tr>
      <th style="text-align:center"><b>TD</b>
      </th>
      <th style="text-align:center"><b>MC</b>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:center">
        <ul>
          <li>&#xB9E4; step &#xB9C8;&#xB2E4; online &#xD559;&#xC2B5;&#xD560; &#xC218;
            &#xC788;&#xB2E4;.</li>
          <li>incomplete sequences &#xC5D0;&#xC11C; &#xD559;&#xC2B5;&#xD560; &#xC218;
            &#xC788;&#xB2E4;.</li>
          <li>continuing (non-terminating) &#xD658;&#xACBD;&#xC5D0;&#xC11C; &#xC0AC;&#xC6A9;
            &#xAC00;&#xB2A5;&#xD558;&#xB2E4;.</li>
        </ul>
      </td>
      <td style="text-align:center">
        <ul>
          <li>episode&#xAC00; &#xB05D;&#xB098;&#xACE0; return &#xAC12;&#xC774; &#xB098;&#xC640;&#xC57C;
            &#xACC4;&#xC0B0;&#xD560; &#xC218; &#xC788;&#xB2E4;.</li>
          <li>complete sequences &#xC5D0;&#xC11C; &#xD559;&#xC2B5;&#xD560; &#xC218;
            &#xC788;&#xB2E4;.</li>
          <li>episodic (terminating) &#xD658;&#xACBD;&#xC5D0;&#xC11C;&#xB9CC; &#xC0AC;&#xC6A9;
            &#xAC00;&#xB2A5;&#xD558;&#xB2E4;.</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

* Return $G_t = R_{t+1} + \gamma R_{t+2} + ... + \gamma ^ {T-1}R_t$는 $v_{\pi}(S_t)$의 unbiased estimate 이다. 즉 편향되지 않았다는 것이다. $G_t$를 계속 평균내다 보면 $v_{\pi}$로 결국 수렴하게 되기 때문에 unbiased한 estimate 이 가능하다.
* True TD target $$R_{t+1} + \gamma v_{\pi}(S_{t+1})$$는 $$v_{\pi}(S_t)$$ 의 unbiased estimate 이다. 모든 것을 알고 있는 oracle이 $$v_{\pi}(S_t)$$의 실제값을 알려주게 되면 bellman equation이 저 값을 보장해주기 때문에 unbiased한 estimate 이 된다. 
* TD target $$R_{t+1} + \gamma v_{\pi}(S_{t+1})$$는 $$v_{\pi}(S_t)$$ 의  biased estimate 이다. 추측치로 업데이트하기 때문에 biased 되어 있을 수 있다.
* TD target은 return 보다 variance가 낮다. Return은 많은 random actions, transitions, rewards에 종속되지만, TD target은 하나의 random action, transition, reward에 종속되기 때문이다.

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-37-11.png)

<table>
  <thead>
    <tr>
      <th style="text-align:center"><b>TD</b>
      </th>
      <th style="text-align:center"><b>MC</b>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:center">
        <ul>
          <li>variance&#xAC00; &#xC791;&#xACE0; bias&#xAC00; &#xD06C;</li>
          <li>&#xC77C;&#xBC18;&#xC801;&#xC73C;&#xB85C; MC &#xBCF4;&#xB2E4; &#xD6A8;&#xC728;&#xC801;&#xC774;&#xB2E4;.</li>
          <li>TD(0)&#xC740; &#xC5D0; &#xC218;&#xB834;&#xD55C;&#xB2E4;. &#xD558;&#xC9C0;&#xB9CC;
            function approximation&#xC744; &#xC37C;&#xC744; &#xB54C; &#xD56D;&#xC0C1;
            &#xC218;&#xB834;&#xD558;&#xB294; &#xAC83;&#xC740; &#xC544;&#xB2C8;&#xB2E4;.</li>
          <li>initial value&#xC5D0; &#xBBFC;&#xAC10;&#xD558;&#xB2E4;. &#xCD94;&#xCE21;&#xCE58;&#xAC00;
            &#xCC98;&#xC74C;&#xC5D0; &#xC798; &#xC815;&#xD574;&#xC838;&#xC788;&#xC5B4;&#xC57C;
            &#xC798; &#xC218;&#xB834;&#xC744; &#xD55C;&#xB2E4;.</li>
        </ul>
      </td>
      <td style="text-align:center">
        <ul>
          <li>variance&#xAC00; &#xD06C;&#xACE0; bias&#xAC00; &#xC791;&#xB2E4;.</li>
          <li>function approximation (deep learning)&#xC744; &#xC0AC;&#xC6A9;&#xD574;&#xB3C4;
            &#xC218;&#xB834;&#xC774; &#xC798; &#xB41C;&#xB2E4;.</li>
          <li>&#xC2E4;&#xC81C;&#xAC12;&#xC744; &#xC5C5;&#xB370;&#xC774;&#xD2B8; &#xD558;&#xAE30;
            &#xB54C;&#xBB38;&#xC5D0; initial value&#xAC00; &#xC911;&#xC694;&#xD558;&#xC9C0;
            &#xC54A;&#xB2E4;.</li>
          <li>&#xC2EC;&#xD50C;&#xD558;&#xAC8C; &#xC774;&#xD574;&#xD558;&#xACE0; &#xC0AC;&#xC6A9;&#xD558;&#xAE30;
            &#xC88B;&#xB2E4;.</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

> - Bias : 데이터 내의 모든 정보를 고려하지 않아, 지속적으로 잘못된 것들을 학습하는 경향 \(underfitting에 연관있음\)
> - Variance : 데이터 내의 에러나 노이즈까지 잘 잡아내는 model에 데이터를 fitting 시킴으로, 실제 현상과 관계없는 random한 것들까지 학습하는 알고리즘의 경향 \(overfitting에 연관있음\)
> 
> 보통 이 두개의 값은 trade-off 관계에 있다.

Return은 환경의 랜덤성을 제공하는 것으로 볼 수 있다. 랜덤성과 랜덤성을 가지고 게임을 하면 variance가 크다. TD는 매 스텝마다 return이 나오기 때문에 랜덤성이 작아서 bias가 높고 variance가 작다.

TD는 한 episode 안에서 매 time-step 마다 업데이트를 하는데 보통 그 이전의 상태가 그 후의 상태에 영향을 많이 주기 때문에 학습이 한 쪽으로 치우칠 수 있다. \(bias가 높다\)  MC는 episode마다 학습하기 때문에 episode 가 전개됨에 따라 전혀 다른 경험을 가질 수가 있다. 하나의 state에서 다음 state로 넘어갈 때도 확률적으로 움직이기 때문에 random 성이 크다. \(variance가 높다\)

### Batch TD vs MC

경험이 무한번 일어난다고 했을 때 \(experience $$ \rightarrow \infty$$\) MC나 TD나 optimal value function에 수렴하게 될 거다. \($$ V(s) \rightarrow v_{\pi}(s) $$\) 하지만 k개의 제한된 episode가 있다고 했을 때 TD가 더 잘 수렴할까? MC나 TD가 과연 같은 값에 수렴할까?

#### AB Example

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-38-49.png)

위와 같이 8개의 episode batch가 주어졌을 때 optimal value V\(A\), V\(B\)는 어떻게 estimate 될까?

* V\(B\) : 8번중 6번 reward를 1 받고 종료되었기 때문에 0.75 이다.
* V\(A\) : \(MC 관점\) state A 에 있었던 유일한 경험이 reward 0을 받았기 때문에 0이다.          : \(TD 관점\) A 에서 B로 100% transition이 발생했고, B가 0.75를 받는다고 결정했기 때문에 V\(A\) 또한 0.75가 된다. V\(B\)로 A를 업데이트 하기 때문이다.

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-39-01.png)

MC는 주어진 training data에 대해 mean squared error를 minimize 하는 추정값을 찾는다. training data에 있어서는 오차가 0 이지만,  이후 데이터에 대해서는 TD보다 큰 오차가 발생할 수 있다. TD는 Markov Property를 이용해 value를 추측하기 때문에 Markov 환경에서는 더 효과적이다. 

정리하면, 

* TD는 Markov property를 사용해서, Markov 환경에서 잘 동작
* MC는 Markov property를 사용하지 않아서, return 값 관측이 가능한 non-Markov 환경에서 잘 동작

### Unified View

#### Monte-Carlo Backup

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-39-10.png)

MC는 episode의 끝까지 가보고 $$S_t$$를 업데이트 한다. \(Monte-Carlo Backup\) 

#### Temporal-Difference Backup

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-39-18.png)

TD는 한 step만 가고 추측해서 그 값으로 업데이트 \(bootstrapping\). 

#### Dynamic Programming Backup

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-39-30.png)

DP는 sampling 하지 않고 할 수 있는 모든 action에 대해 update. 모든 범위에 대해 업데이트하고 episode의 끝까지 가지는 않는다.

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-39-37.png)

<table>
  <thead>
    <tr>
      <th style="text-align:center"></th>
      <th style="text-align:center">
        <p>Bootstrapping</p>
        <p>: update involves an estimate</p>
      </th>
      <th style="text-align:center">
        <p>Sampling</p>
        <p>: update samples an expectation</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:center"><b>MC</b>
      </td>
      <td style="text-align:center">X</td>
      <td style="text-align:center">O</td>
    </tr>
    <tr>
      <td style="text-align:center"><b>TD</b>
      </td>
      <td style="text-align:center">O</td>
      <td style="text-align:center">O</td>
    </tr>
    <tr>
      <td style="text-align:center"><b>DP</b>
      </td>
      <td style="text-align:center">O</td>
      <td style="text-align:center">X</td>
    </tr>
  </tbody>
</table>

Bootstrapping은 추측치로 업데이트 하기 때문에 예측치에 추측치가 포함된다. Sampling은 full sweep을 안하고 sample 데이터로 업데이트 하는것을 말한다.

모델을 알 때는 DP가 가능하지만, 모델을 모를 때에는 sample backup을 해야 한다. \(agent가 policy를 따라 가는 것이 sampling\)

## n-Step TD

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-39-47.png)

위에서 봤던 TD는 TD\(0\)으로 바로 다음 단계만 보고 value function을 update 했었다. 몇 단계 까지 보고 추정값을 갱신할 지에 따라 n-step TD로 표현할 수 있다. 매 time step 마다 학습을 하게되면 정보가 한정적이여서 학습하는데 오래 걸리기 때문에 MC와 TD의 절충안으로 각각의 장점을 살리는 방법이다.

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-39-58.png)

n-step 에서 n 만큼은 return 값을 그대로 사용하고, 그 이후에는 추측치를 넣어서 update 하는 형식이다. 여기에서 n을 구하는 것도 하나의 문제이다. n 값을 경험적으로 알아낼 수도 있겠지만 몇 가지의 n-step을 구하고 그를 평균내는 방법도 있다.

### Averaging n-Step Returns

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-40-05.png)

n-step 에서 n의 값을 다르게 해서 return 값을 얻어내고, 각 return 값들의 평균을 이용 \($$\frac { G^{(i)} +  G^{(j)}} {2}$$\)

### $$ \lambda $$-return

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-40-12.png)

TD\(0\) 부터 MC 까지 모든 것을 평균내서 쓸 수도 있다. 이 때 단순 평균을 사용하지 않고 $$\lambda$$라는 weight를 이용해 geometric mean을 이용한다.\(Forward-view TD\($$\lambda$$\)\) MC에 가까워질 수록 \(n 값이 커질 수록\) 가중치가 적게 들어가도록 한다.  

> geometric mean을 사용하는 이유는? 
>
> * computational efficient를 위해서. TD\(0\)과 같은 비용으로 TD\($$\lambda$$\)를 계산할 수 있다.
>
> Forward View? 
>
> * 미래를 보고 $$ G^{\lambda}_t $$를 계산하는거다.

#### Forward View of TD\($$\lambda$$\)

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-40-20.png)

하지만 이 방법도 episode의 끝까지 가봐야 update 할 수 있다는 문제가 있다. TD 의 장점이 사라진다. 이런 문제를 해결하기 위해서 나온 것이 eligibility trace를 이용한 Backward View of TD \($$\lambda$$\) 다.

#### Backward View of TD\($$\lambda$$\)

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-40-26.png)

현재 내가 받은 reward 값에 많이 기여한 값들에 업데이트를 많이 해주는 방식이다. 얼마나 빈번하게 일어났는지\(Frequency heuristic\), 얼마나 최근에 일어났는 지\(Recency heuristic\)를 기준으로 과거를 기억해뒀다가 현재의 reward를 과거의 state들로 분배해준다.

Eligibility trace는 Frequency, Recency heuristic 두가지를 결합하여 만들어진다. 

![](/assets/images/20-05-25-rl-4-model-prediction-2022-01-20-21-40-33.png)

현재의 value function을 update 함과 동시에 과거 지나왔던 state의 eligibility trace를 기억해뒀다가 과거의 모든 state들의 value function 또한 update 한다. 현재의 경험이 과거의 value function에 얼마나 영향을 줄 지는 $$\lambda$$를 통해 조절한다.

* $$\delta_t$$: 현재 update 할 값. TD-error
* $$E_t(s)$$: eligibility trace
* $$V(s)$$: update 할 과거 state의 value function

