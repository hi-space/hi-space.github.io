---
title: Semi-supervised Models are Strong Unsupervised Domain Adaptation Learners
category: AI
tags: ai paper 🔥
cover: /assets/images/21-11-04-ss-model-strong-udp-2021-11-04-16-43-55.png
---

- 2021
- [Paper](https://arxiv.org/pdf/2106.00417.pdf)
- [Github](https://github.com/YBZh/Bridging_UDA_SSL)

<!--more--> 

# Abstract 

Unsupervised domain adaptation (UDA) and semi-supervised learning (SSL) are two typical strategies to reduce expensive manual annotations in machine learning. In order to learn effective models for a target task, UDA utilizes the available labeled source data, which may have different distributions from unlabeled samples in the target domain, while SSL employs few manually annotated target samples. Although UDA and SSL are seemingly very different strategies, we find that they are closely related in terms of task objectives and solutions, and SSL is a special case of UDA problems. Based on this finding, we further investigate whether SSL methods work on UDA tasks. By adapting eight representative SSL algorithms on UDA benchmarks, we show that SSL methods are strong UDA learners. Especially, state-of-the-art SSL methods significantly outperform existing UDA methods on the challenging UDA benchmark of DomainNet, and state-of-the-art UDA methods could be further enhanced with SSL techniques. We thus promote that SSL methods should be employed as baselines in future UDA studies and expect that the revealed relationship between UDA and SSL could shed light on future UDA development. Codes are available at https://github.com/YBZh.

# 1 Introduction 

Although deep models have achieved great successes on various tasks [24, 16], they require a large amount of labeled training data and could not generalize to data of shifted distributions, impeding their applications in practical tasks. Given a new target task, the model learned from labeled source data of a previous task typically achieves degenerated performance on target data. It is time and labor consuming to collect a large amount of labeled training data for the new task. Fortunately, collecting unlabeled data is usually much easier; thus, there is a huge demand to learn efficient target models by utilizing labeled source data and unlabeled target data, falling in the realm of unsupervised domain adaptation (UDA) [15, 34]. Motivated by seminal UDA theories (e.g., Theorem 1) [2, 58, 56], which bound the target error with terms including domain discrepancy, several popular UDA methods [15, 29, 38, 30, 57] have been proposed to minimize the target risk by simultaneously learning domain-invariant representations and minimizing the source risk. Besides, the target risk is minimized by re-weighting labeled source samples in [39, 8]; pixel-level alignment is pursued in [5, 28] with image style transfer techniques. A comprehensive survey on UDA methods can be found in [48, 47] .
With similar motivations, semi-supervised learning (SSL) explores a different way from UDA to learn target models with minor manual efforts. Considering that manually annotating a few amount of target data is easy to achieve, SSL aims to learn target models from few labeled target data and a large amount of unlabeled target data [45, 61, 6]. SSL methods are typically motivated by basic assumptions on the data structure, such as the smoothness assumption, low-density separation assumption, and manifold assumption [45, 61, 6]. The smoothness assumption promotes that two samples close in the input space should present the same label, inspiring the consistency regularization [26, 49] and graph-based methods [60, 20]. The low-density separation assumption states that the decision boundaries should lie in areas where few samples are observed, which motivates methods of entropy minimization [18] and self-training [27]. The manifold assumption suggests that the input space could be decomposed into multiple low-dimensional manifolds and samples on the same manifold should share the same label, whose representative method is the graph-based one [60].
Some methods simultaneously adopt multiple assumptions [60, 41, 4].

![](/assets/images/21-11-04-ss-model-strong-udp-2021-11-04-16-43-55.png)

We illustrate the task settings of UDA and SSL in Figure 1. Although UDA and SSL are seemingly very different, we argue that they are closely related in the following aspects:
- UDA and SSL share a similar objective of utilizing unlabeled (target) data to improve the performance of the model trained with labeled (source) data only.
- Methods of UDA and SSL similarly utilize the underlying data structure.
- SSL is a special case of UDA problems when the target support covers source support.

Based on the above relationships between UDA and SSL, we further investigate an important question: whether SSL methods work on UDA tasks? To answer this question, we make in-depth analyses by applying eight representative SSL algorithms on four UDA benchmarks. Results clearly demonstrate that SSL methods are strong UDA learners. Especially, state-of-the-art SSL methods significantly outperform existing UDA methods on the challenging UDA benchmark of DomainNet, and state-ofthe-art UDA methods could be further enhanced with SSL techniques. Therefore, we argue that SSL methods, especially the state-of-the-art ones, should be employed as baselines in future UDA studies.

## 4.2 Ablation study and analyses

### Combining UDA and SSL methods for UDA tasks.

We find that state-of-the-art UDA methods could be boosted with SSL techniques. Specifically, we combine the popular consistency regularization in SSL [49, 41] with UDA methods of MCC [21] and MDD [58], leading to boosted results and the new state of the art, as illustrated in Tables 2, 3, 4, and 5. The boosted results also justify the efficacy of SSL techniques on UDA tasks. The combining strategies are detailed in the appendices.

### Employing UDA methods for SSL tasks.

Given the close relationship between UDA and SSL and the superior results of SSL methods on UDA tasks, one may consider employing UDA methods on SSL tasks. As illustrated in Figures 4(a) and 4(b), UDA methods of DANN [15], CDAN [30] and MCC [21] marginally outperform the ‘Labeled only’ baseline, presenting limited efficacy on SSL tasks. We also attempt to combine the state-of-the-art SSL method of FixMatch [41] with classical UDA techniques (e.g., adversarial distribution minimization in [15, 30]) for SSL tasks, but we observe no improvement over vanilla FixMatch.

We note that a technique termed distribution alignment is adopted in the recent SSL method [3]; however, it is different from the popular distribution alignment in UDA. Berthelot et al. [3] aim to align the marginal class distribution across labeled and unlabeled data, which is motivated by the input-output mutual information maximization objective. On the contrary, the popular distribution alignment in UDA aligns the marginal feature distribution across labeled and unlabeled data

### Applying SSL losses on the feature extractor g only for UDA tasks.

Considering the domain shift, researchers typically apply the entropy minimization loss on the feature extractor g only for UDA tasks [54, 57]. We analyze whether this benefit could generalize to other SSL regularizations (7). As illustrated in Figure 4(c), better results of SSL methods on UDA benchmarks are typically achieved by applying SSL losses on the feature extractor g only (cf. the appendices for details), which is adopted as the default implementation of SSL methods for UDA tasks in this paper.

### Convergence performance.

We illustrate the convergence performance on the skt→clp task of the DomainNet dataset in Figure 4(d). UDA and SSL methods both converge smoothly.

### Domain divergence.

DANN [15] and CDAN [30], which are designed for the divergence minimization, consistently and significantly minimize the distribution divergence on various tasks, as illustrated in Figures 5(a) and 5(b). On the contrary, most SSL methods and their variants marginally reduce the domain divergence; although FixMatch considerably reduces the domain divergence on the A→W task, it hardly reduces the divergence on the VisDA-2017 dataset, a task with larger domain divergence, which distinguishes it from the seminal UDA methods (e.g., DANN and CDAN). In practice, SSL methods and their variants currently achieve the state of the art, as shown in Tables 2, 3, 4, and 5. Similar to [7, 59], this empirical evidence challenges the dominant position of divergence minimization in UDA studies and suggests another promising UDA solution of SSL methods.

### Data pre-processing.

As illustrated in Figures 5(c) and 5(d), results on UDA tasks are significantly influenced by the data pre-processing strategies; for example, different data pre-processing strategies lead to more than 2% accuracy divergence for the MCC method on the DomainNet dataset, which is even larger than its improvement over most compared methods. Thus, we promote that researchers should compare different methods on UDA tasks with the same data pre-processing, which is less concerned in previous UDA studies. We detail the data pre-processing strategies in the appendices.

# 5 Conclusions and discussions

In this work, we bridged UDA and SSL by revealing that SSL is a special case of UDA problems. By adapting eight representative SSL methods on UDA benchmarks, we showed that SSL methods were strong UDA learners. Specifically, the state-of-the-art SSL method of FixMatch outperformed existing UDA methods by more than 2.0% on the challenging DomainNet benchmark, and the new state of the art could be achieved by empowering UDA methods with SSL techniques. We thus promoted that SSL methods should be employed as baselines in future UDA studies. Our work bridged existing tasks of UDA and SSL; thus, it inherited their negative impacts (e.g., abused models). However, in experiments, we conducted the model selection (e.g., choosing training checkpoints) according to results on labeled target data, which is actually a common problem in the UDA community; although the upper bound performance of SSL and UDA methods [22] were fairly compared, we expect to compare them on UDA tasks with appropriate model selection strategies in the future.