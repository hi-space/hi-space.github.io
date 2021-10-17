---
title: Unsupervised Domain Adaptation for Semantic Segmentation via Class-Balanced Self-Training
category: AI
tags: ai paper 🔥
cover: /assets/images/21-10-15-uda-for-ss-class-balance-self-training-2021-10-16-09-38-11.png
---

- ECCV 2018
- [Paper](https://arxiv.org/pdf/1810.07911.pdf)
- [Github](https://github.com/yzou2/CBST)

<!--more-->

# Abstract

Recent deep networks achieved state of the art performance on a variety of semantic segmentation tasks. Despite such progress, these models often face challenges in real world “wild tasks” where large difference between labeled training/source data and unseen test/target data exists. In particular, such difference is often referred to as “domain gap”, and could cause significantly decreased performance which cannot be easily remedied by further increasing the representation power. Unsupervised domain adaptation (UDA) seeks to overcome such problem without target domain labels. In this paper, we propose a novel UDA framework based on an iterative self-training (ST) procedure, where the problem is formulated as latent variable loss minimization, and can be solved by alternatively generating pseudo labels on target data and re-training the model with these labels. On top of ST, we also propose a novel classbalanced self-training (CBST) framework to avoid the gradual dominance of large classes on pseudo-label generation, and introduce spatial priors to refine generated labels. Comprehensive experiments show that the proposed methods achieve state of the art semantic segmentation performance under multiple major UDA settings.

# 1 Introduction

Semantic segmentation is a core computer vision task where one aims to densely assign labels to each pixel in the input image. In the past decade, significant amount of effort has been devoted to this area [1, 5, 6, 9, 10, 13, 20, 38, 39, 44, 45], leading to considerable progress with the recent advance of deep representation learning [15, 19, 31]. The competition on major open benchmark datasets [10] have resulted in a number of more powerful models that tend to overfit to the benchmark data. While the boundaries of benchmark performance have been pushed to new limits, these models often encounter challenges in practical applications such as autonomous driving, where one needs ubiquitous good performance of the perception module. This is because benchmark datasets are usually biased to specific environments, while the testing scenario may encounter large domain differences caused by a number of factors, including change of geological position, illumination, camera, weather condition, etc. In this case, even the performance of a powerful model often drops dramatically, and such issue can not be easily remediated by further building up the model power [9, 16, 17].

![](/assets/images/21-10-15-uda-for-ss-class-balance-self-training-2021-10-16-09-38-11.png)

A natural idea to improve network’s generalization ability is to collect and annotate data covering more diverse scenes. However, densely annotating image is time-consuming and labor-intensive. For example, each Cityscapes image on average takes about 90 minutes to annotate [10]. To overcome the limitation, efforts were made to efficiently generate densely annotated images from rendered scenes, such as the Grand Theft Auto V (GTA5) [24] and SYNTHIA [26]. However, the large appearance gap across simulated/real domains significantly degrades the performance of synthetically trained models.

In light of the above issues, in this paper we focus on the challenging problem of unsupervised domain adaptation for semantic segmentation, aiming to unsupervisedly adapt a segmentation model trained on a labeled source domain to a target domain without knowing target labels. Recently, unsupervised domain adaptation has been widely explored for classification and detection tasks. There is a predominant trend to use adversarial training based methods for matching the distributions of both source and target features [3,9,12,17,29]. In particular, these methods aim to minimize a domain adversarial loss to reduce both the global and class-wise discrepancy between source and target feature distributions, while retaining good performance on source domain task by minimizing the task-specific loss. 

Adversarial training based domain adaptation methods have recently achieved great success. However, in this work we show that similar or even better adaptation performance can be achieved by taking an alternative way without using adversarial training. Rather than trying to adapt by confusing the domain discriminator, our method kind of unifies the feature space alignment and the task itself together under a single, unified loss, which is given in section 4. Under the single unified loss, we incorporate both the global and class-wise feature alignment as parts of our unified task, instead of considering feature matching and classification task separately.

Traditional self-training methods with handcrafted features is a common semi-supervised learning method that can learn better decision boundary for source and target data. Usually these approaches do not consider feature distribution matching. But combined with CNN, self-training becomes a powerful domain adaptation method that can not only learn better decision boundary, but also find a feature space of matched source and target distribution. In essence, the feature learning in self-training guided by softmax cross-entropy loss not only encourages global closeness of source and target features but also the classwise feature alignment. The CNN based self-training methods share the same goal of adversarial training based global and class-wise feature alignment methods [9, 17], but it try to solve domain adaption by a simpler and more elegant way.

The area of self-training based domain adaptation for semantic segmentation is underdevelopment. We propose a typical CNN based self-training (ST) framework for domain adaptation in semantic segmentation of which workflow is shown in figure refflow, taking adapting from GTA5 → Cityscapes as an example. ST is carried out by alternately generating a set of pseudo-labels corresponding to large selection scores (i.e., softmax probability) in target domain, and then fine tuning network based on these pseudo-labels and labeled source data. It should be mentioned that ST assumes that target samples with larger prediction probability have better prediction accuracy.

The visual (e.g., appearance, scale, etc.) domain gap between source and target domains are usually different between classes. This can result in different difficulty degree for the network to learn transferable knowledge for each class. For instance, different countries may have different construction views and plants, but traffic lights and vehicles are similar. So it’s harder for the source pre-trained models to learn transferable knowledge for construction and plants than for traffic lights and vehicles. Moreover, the imbalanced class distribution of source domain, and difference between source distribution and target distribution can also cause different degree of difficulty in transferring knowledge among different classes. This causes different prediction confidence levels for various classes in target domain. Since ST selects pseudo-labels with large confidence, it tends to be biased towards easy-to-transfer classes ignoring other classes and have inferior adaptation performance.

In summary, we focus on self-training based adaptation methods for semantic segmentation in this work. Our contributions are as follows. 

– Building on deep nets, we introduce a self-training (ST) with self-paced learning adaptation framework for segmentation. We formulate it as a loss minimization problem in the form of mixed integer nonlinear program, which can be solved in an end-to-end way. Both domain-invariant features and classifier are expected be learned.

– To solve the class imbalance problem of pseudo-labels in ST, we propose a novel class-balanced self-training (CBST) adaptation for semantic segmentation. The proposed CBST utilizes confidence scores normalized classwise to select and generate pseudo-labels with balanced class distribution. – Moreover, we observe that a traffic scene has its own spatial structure and introduce the concept of spatial priors (SP). We incorporate spatial priors into proposed self-training leading to class-balanced self-training with spatial priors (CBST-SP). The probability scores weighted by spatial priors are used for pseudo-label generation metric.

– We comprehensively evaluate our approaches in adapting large-scale rendered image dataset SYNTHIA/GTA5, to real image dataset, Cityscapes, and achieve state-of-the-art performance, outperforming other methods by a large margin. Also we test our methods in cross city adaptation settings, Cityscapes to NTHU dataset, and achieve state-of-the-art performance.

# 2 Related works

The revolution of deep learning inspired broad interest in deep neural network based semantic segmentation. Long et al. [20] proposed a fully convolutional network for pixel-level classification. Recently several researchers proposed powerful segmentation nets, such as ResNet-38, PSPNet, etc. [38, 39, 44].

Unsupervised domain adaptation has been widely investigated in computer vision primarily for classification and detection tasks. In the era of deep neural network, the main adaption idea is to learn domain invariant features by minimizing difference between source and target feature distributions in an end-toend way [11,12,14,21,32,35,37]. Among them, several methods utilize Maximum Mean Discrepancy (MMD) and its kernel variants to achieve the goal of feature distribution difference minimization. Recently there is an increasing interest in utilizing adversarial learning based methods to reduce the gap between source and target domains [14, 21, 36, 37].

Another important strategy for unsupervised domain adaptation is based on self-training [4,47], which has many applications in vision and natural language processing [22, 25, 40, 47]. Tang et al. [33] proposed a self-paced adaptation to shift object detection model from images to videos by learning labeled source samples and target data with pseudo-labels in an easy-to-hard way. Chen et al. [7] proposed a adaptation framework by slowly adapting its training set from the source to the target domain, using ideas from co-training. Bekker [2] et al. tackle the noisy labels problem.

As pointed out in [43], approaches addressing classification do not translate well to the semantic segmentation problem. So recently domain adaptation for semantic segmentation has emerged as a hot topic. Several researchers have focused on utilizing adversarial learning to minimize the domain gap of feature spaces. [9, 17] proposed pixel level adversarial domain adaptation methods to reduce domain gap in feature spaces. Based on the domain adversarial training, [28] introduced a critic network detecting samples near the boundary and a generator that can generate discriminative features for target domain. [43] proposed a curriculum adaption method to regularize the predicted label distribution in the target domain to follow label distributions in source domain. Another possible direction to solve the domain adaptation problem is to utilize style transfer technique to stylize annotated source domain images as target domain images. Following this idea, based on the style transfer network CycleGAN [46], [16] proposed a cycle-consistent adaptation framework combining the cycle-consistent loss with adversarial loss to minimize both pixel level and feature level domain gap.

# 3 Preliminaries

## 3.1 Fine-tuning for supervised domain adaptation

If the labels for the same task in both source and target are available, possibly the most direct way to perform domain adaptation is supervised fine-tuning the model on both domains. For semantic segmentation nets with softmax output, the adaptation problem can be formulated as minimizing the following loss function: min w LS(w) = − X S s=1 X N n=1 y ⊤ s,n log(pn(w, Is)) − X T t=1 X N n=1 y ⊤ t,n log(pn(w, It)) (1) where Is denotes the image in source domain indexed by s = 1, 2, ..., S, ys,n the ground truth label for the n-th pixel (n = 1, 2, ..., N) in Is, and w contains the network weights. pn(w, Is) is the softmax output containing the class probabilities at pixel n. Similar definitions apply for It, yt,n and pn(w, It).

## 3.2 Self-training for unsupervised domain adaptation

In the case of unsupervised domain adaptation, the target ground truth labels are not available. An alternate way to fine-tune the segmentation model is to consider the target labels as hidden variables that can be learned. Accordingly, the problem can be formulated as follows:


# 4 Proposed methods

## 4.1 Self-training (ST) with self-paced learning 

Jointly learning the model and optimizing pseudo-labels on unlabeled data is naturally difficult as it is not possible to completely guarantee the correctness of the generated pseudo-labels. A better strategy is to follow an “easy-to-hard” scheme via self-paced curriculum learning, where one seeks to generate pseudolabels from the most confident predictions and hope they are mostly correct. Once the model is updated and better adapted to the target domain, the scheme then explores the remaining pseudo-labels with less confidence. To incorporate curriculum learning, we consider the following revised self-training formulation:

![](/assets/images/21-10-15-proda%20copy-2021-10-16-07-46-28.png)

where assigning ys,n as 0 leads to ignoring this pseudo-label in model training, and the L1 regularization serves as a negative sparse promoting term to prevent the trivial solution of ignoring all pseudo-labels. k is a hyperparameter controlling the amount of ignored pseudo-labels. A larger k encourages the selection of more pseudo-labels for model training. To minimize the loss in Eq. (3), we take the following alternative block coordinate descent algorithm:

– a) Fix (initialize) w and minimize the loss in Eq. 3 with respect to yˆt,n.

– b) Fix yˆt,n and optimize the objective in Eq. 3 with respect to w.

We call one step of a) followed by one step of b) as one round. In this work, we propose a self-training algorithm where step a) and step b) are alternately repeated for multiple rounds. Intuitively, step a) selects a certain portion of most confident pseudo-labels from the target domain, while step b) trains the network model given the pseudo-labels selected in step a). Fig. 1 illustrates the proposed algorithm flow in the domain adaptation example of GTA5 → Cityscapes. Solving step b) leads to network learning with stochastic gradient descent. However, solving step a) requires a nonlinear integer programming given the optimization over discrete variables. Given k > 0, step a) can be rewritten as: 

![](/assets/images/21-10-15-proda%20copy-2021-10-16-07-48-16.png)

Since yˆt,n is required to be either a discrete one-hot vector or a zero vector, the pseudo-label configuration can be optimized via the following solver: 

![](/assets/images/21-10-15-proda%20copy-2021-10-16-07-48-38.png)

Unlike traditional self-training adaptation with handcrafted features that learn a domain-invariant classifier, CNN based self-training can learn not only domaininvariant classifier but also domain-invariant features. The softmax loss implicitly tries to reduce the domain difference in feature space. In addition, the selftraining also has the missing value (pseudo-label) problem, similar to EM algorithm. The proposed alternate optimization method can learn the weights of models without prior observation of target domain labels.

One may note that the proposed framework is similar to [33] and several other related works. However, the proposed method presents a more generalized model for self-training and self-paced learning, in the sense that pseudo-label generation is unified with curriculum learning under a single learning framework. More importantly, in terms of the specific application, the above self-training framework sheds light on a relatively new direction for adapting semantic segmentation models. We will show that self-training based methods lead to considerably better or competitive performance compared to many current state of the art methods that are predominantly based on adversarial training.

## 4.2 Class-balanced self-training (CBST)

As mentioned in section 1, the difference in visual domain gap and class distribution can cause different domain-transfer difficulty among classes, resulting in relatively higher prediction confidence scores for easy-to-transfer classes in target domain. Since ST generates pseudo-labels corresponding to large confidence, an issue comes out that model tends to be biased towards these initially well-transferred classes and ignore other hard classes along the training process. Thus it is difficult for ST to perform well in multi-class segmentation adaptation problem. To overcome this issue, we propose the following class-balanced self-training framework where class-wise confidence levels are normalized:

where each kc is a separate parameter determining the proportion of selected pseudo-labels in class c. As one may observe, it is the difference between kc that introduces different levels of class-wise bias for pseudo-label selection, and addresses the issue of inter-class balance. The optimization flow of class-balanced self-training is the same as in Eq. (3) except for pseudo-label generation. Again, we can rewrite the step of pseudolabel optimization as: 

## 4.3 Self-paced learning policy design 

### Determination of k in ST

From previous sections, we know that k plays a key role in filtering out pseudo-labels with probabilities smaller than k. To control the proportion of selected pseudo-labels in each round, we set k based on the following strategy: We take the maximum output probability at each pixel, and sort such probabilities across all pixel locations and all target images in descending order. We then set k such that exp(−k) equals the probability ranked at round(p ∗ T ∗ N), where p is a proportion number between [0, 1]. In this case, pseudo-label optimization produces p × 100% most confident pseudo-labels for network training. The above policy can be summarized in Algorithm 1.

We design the self-paced learning policy such that more pseudo-labels are incorporated for each additional round. In particular, we start p from 20%, and empirically add 5% to p in each additional round of pseudo-label generation. The maximum portion is set to be 50%.

### Determination of kc in CBST

The policy of kc in CBST is similarly defined. Although CBST seemingly introduce much more parameters than ST, we propose a strategy to easily determine kc, and effectively encode the class-wise confidence levels. Note that Algorithm 2 determines kc by ranking the class c probabilities on all pixels predicted as class c, and setting kc such that exp(−kc) equals to the probability ranked at round(p ∗ Nc), where Nc indicates the number of pixels predicted as class c. Such a strategy basically takes the probability ranked at p × 100% separately from each class as a reference for both thresholding and confidence normalization. The proportion variable p and its increasing policy is defined exactly the same to ST.

## 4.4 Incorporating spatial priors

For adapting models in the case of street scenes, we could take advantage of the spatial prior knowledge. Traffic scenes have common structures. For example, sky is not likely to appear at the bottom and road is not likely to appear at the top. If the image views in source domain and target domain are similar, we believe this knowledge can help to adapt source model. Thus we introduce spatial priors, similar to [30], by counting the class frequencies in the source domain, followed by smoothing with a 70×70 Gaussian kernel. In particular, we use qn(c) to indicate the frequency of class c at pixel n. Upon obtaining the class

![](/assets/images/21-10-15-proda%20copy-2021-10-16-07-52-05.png)

# 5 Numerical experiments

In this section, we provide a comprehensive evaluation of proposed methods by performing experiments on three benchmark datasets. We firstly consider a cross-city adaptation case of shifting from Cityscapes to NTHU dataset [9]. Following [9], we choose the training set of Cityscapes as source. The NTHU dataset contains 400 1, 024 × 2, 048 from 4 different cities: Rome, Rio, Tokyo and Taipei. Also we consider two challenging problems: from SYNTHIA [26] to Cityscapes [10] and from GTA5 [24] to Cityscapes. We use SYNTHIA-RANDCITYSCAPES subset including labeled 9,400 760 × 1280 images. GTA5 dataset includes annotated 24,966 1, 052 × 1, 914 images captured from the GTA5. The validation set of Cityscapes is treated as target domain.

### Implementation Details

We use FCN8s-VGG16 [20] as our base network in SYNTHIA to Cityscapes and GTA5 to Cityscapes to give fair comparison with other methods utilizing the same base net. Further we boost our methods’ performance via a better model ResNet-38 [39]. In the cross-city setting, we show state-of-the-art performance via CBST with ResNet-38. The networks were pre-trained on ImageNet [27]. SGD has been used to train all the models by MXNET [8]. We use NVIDIA Titan Xp. In the CBST and CBST-SP experiments of GTA5 to Cityscapes and Cityscapes to NTHU, we use a hard sample mining strategy which mines the least prediction classes according to target prediction portions. The mining classes are the worst 5 classes and top priority are given to classes whose portions are smaller than 0.1%. Other more details are provided in supplementary document.

## 5.1 Small shift: cross city adaptation

NTHU dataset contains 13 classes shared with Cityscapes. We follow the same protocol as [9] to use a 10-fold cross validation. The IoU (Intersection-overUnion) of each class and the mIoU (mean IoU) are reported. Table 1 shows the results. Our CBST achieves superior or competitive performance compared with state-of-the-art.

![](/assets/images/21-10-15-proda%20copy-2021-10-16-07-54-21.png)

From GTA5 to Cityscapes Table 3 gives experimental results of the shared 19 classes. For the results with FCN8s-VGG16 as base model, the performance of ST demonstrates that the adapted model can be easily biased towards initial easy-to-transfer classes. However, the CBST not only achieves better mIoU than ST, but also better IoU for these initial hard-to-transfer classes. Moreover, since images from GTA5 and Cityscapes have similar view structure, we evaluate our proposed CBST-SP, achieving mIoU 36.1, which is better than the results using powerful base models, ResNet-50 [28] and DenseNet [23]. Equipped with a powerful model ResNet-38, our method get a much better score 46.2, outperforming other methods by a large margin. The multi-scale testing (0.5,0.75,1.0)  boosts the mIoU to 47.0. Figure 4 gives the visualization segmentation results in Cityscapes.

![](/assets/images/21-10-15-proda%20copy-2021-10-16-07-55-33.png)

# 6 Conclusions

In this paper, we proposed a deep neural network based self-training (ST) framework for unsupervised domain adaptation in the context of semantic segmentation. The ST is formulated as a loss minimization problem allowing learning of domain-invariant features and classifier in an end-to-end way. A class-balanced self-training (CBST) is introduced to overcome the imbalance issue of transferring difficulty among classes via generating pseudo-labels with balanced class distribution. Moreover, if there is a small domain difference in image view, we could incorporate spatial priors (SP) into CBST, resulting in CBST-SP. We experimentally demonstrate that our proposed methods achieve superior results outperforming other state-of-the-art methods by a large margin.We empirically show our proposed methods is compatible with adversarial domain adaptation methods.

