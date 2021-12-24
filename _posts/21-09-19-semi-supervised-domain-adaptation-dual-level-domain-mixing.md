---
title: Semi-supervised Domain Adaptation based on Dual-level Domain Mixing for Semantic Segmentation
category: AI
tags: ai paper
article_header:
    type: overlay
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/21-09-04-domain-adaptation-dual-level-domain-mixing-SSDA%20framework.png
---

- CVPR2021
- [Paper](https://arxiv.org/pdf/2103.04705.pdf)
- Multi teacher (Region-level teacher와 Sample-level teacher) 를 이용해 student model에게 각기 다른 도메인에 대한 정보를 학습시켜준다. (Domain Mixing)

<!--more-->

<details>
<summary>Original</summary>
<div markdown="1">

# Abstract

Data-driven based approaches, in spite of great success in many tasks, have poor generalization when applied to unseen image domains, and require expensive cost of annotation especially for dense pixel prediction tasks such as semantic segmentation. Recently, both unsupervised domain adaptation (UDA) from large amounts of synthetic data and semi-supervised learning (SSL) with small set of labeled data have been studied to alleviate this issue. However, there is still a large gap on performance compared to their supervised counterparts. We focus on a more practical setting of semi-supervised domain adaptation (SSDA) where both a small set of labeled target data and large amounts of labeled source data are available. To address the task of SSDA, a novel framework based on dual-level domain mixing is proposed. The proposed framework consists of three stages. First, two kinds of data mixing methods are proposed to reduce domain gap in both region-level and sample-level respectively. We can obtain two complementary domain-mixed teachers based on dual-level mixed data from holistic and partial views respectively. Then, a student model is learned by distilling knowledge from these two teachers. Finally, pseudo labels of unlabeled data are generated in a self-training manner for another few rounds of teachers training. Extensive experimental results have demonstrated the effectiveness of our proposed framework on synthetic-to-real semantic segmentation benchmarks.

# 1. Introduction

Semantic segmentation with the goal of assigning semantic-level labels to every pixel in an image is one of the fundamental topics in computer vision due to its widely critical real-world applications, such as autonomous †Equal contribution ‡Part of this work was done while he was in Noah’s Ark Lab driving [11] and robotic navigation [28, 39]. Over the past few years, deep convolutional neural networks(CNNs) have achieved dramatic improvements in semantic segmentation [1, 25, 17, 26, 2, 49]. The success of CNN-based methods benefits from large volume of manually labeled data [24, 8], and the assumption of independent and identical data distribution between training and testing samples.
However, performance drops significantly when the model trained on training set (source domain) is directly applied to unseen test scenarios (target domain). In addition, densely annotating pixel-wise labels of many samples in target domain is time-consuming and uneconomical.
To reduce the heavy demand for pixel-wise annotation, one way is to employ large amounts of easy-to-get simulation data which can be collected from game engines such as GTA5 [33] and SYNTHIA [34]. In addition, unsupervised domain adaptation (UDA) strategy, which aims at transferring knowledge from a synthetic label-rich source domain to a real-world label-scarce target domain, is required to bridge domain gap between synthetic and realworld domains. Impressive results have been achieved by UDA methods that extract domain-invariant representations via entropy minimization [31, 43], generative modelling [16, 12] and adversarial learning [42, 41]. However, domain shift cannot be completely eliminated by these methods due to weak supervision on target examples. There is still a big gap in performance compared with supervised methods. Another way in addressing the issue of heavy annotation is to annotate only a small set of images from target domain and make full use of plenty of unlabeled data with semi-supervised learning (SSL) techniques [10, 30, 9, 29].
Due to the shortage of labeled data in SSL setting, the obtained model has the risk of overfitting to the small amount of labeled data. How to effectively utilize available unlabeled and limited labeled data from different domains is the key in improving model’s accuracy and generalization for pixel-wise prediction tasks.
1 arXiv:2103.04705v1 [cs.CV] 8 Mar 2021 Hence, a more practical task of semi-supervised domain adaptation (SSDA) is recently introduced by combining the small set of labeled target data images in SSL with the large amounts of labeled source domain data and unlabeled target domain data. In order to address the SSDA problem, one naive way is to equip UDA methods with additional supervision on the extra labeled target images (see Baseline in Table 1.). For example, Alleviating Semantic-level Shift (ASS) model [44] is proposed for better promoting the distribution consistency of features by using adversarial learning on outputs from two labeled domains. However, these methods cannot fully explore rich information within available labeled and unlabeled data in two domains.
Semantic segmentation is a dense pixel-wise prediction task, and classification of one pixel depends not only on its own value but also on its neighbourhood’s context. We focus on how to effectively utilize labeled data in different domains to extract domain-invariant representations in both region-level and sample-level. The proposed framework consists of three steps. First, two kinds of data mixing methods are proposed to reduce domain gap in both regionlevel and sample-level. The region-level data mixing is achieved by applying two masks to labeled images from two domains and combining the two masked regions, which encourages a model to extract domain-invariant features about semantic structure from partial view. On the other hand, the image-level data mixing directly mixes labeled images from two domains from holistic view. Such two mixing ways help train two complementary teacher models that work on both two kinds of data distribution. In the second step, we employ knowledge distillation technique to extract “dark knowledge” from these two complementary teachers, which works as guidance in the learning process of a student model for target domain. By integrating knowledge from two views and making full use of unlabeled data, the student model of the same network architecture can give even better performance than any of its teachers. Once a good student model for target domain is obtained, pseudo labels could be generated with self-training strategy to expand the set of labeled target domain data for iterative update. Compared with traditional self-training methods, which directly use pseudo labels to train a final model, we instead leverage these pseudo labels to obtain two stronger domain-mixed teachers, which also leads to stronger student by another round of knowledge distillation. Overall, in our framework, teachers and student are progressively growing, and we can obtain a final well-trained student model.
Our contributions of this paper are three-fold
- Two kinds of data mixing methods are proposed to train domain-mixed teachers across domains in both region-level and sample-level to alleviate data distribution mismatch between different domains.
- A stronger student model on target domain can be obtained by distilling knowledge from complementary domain-mixed teachers. It can be further strengthened by employing pseudo labels which are generated for unlabeled target data in a self-training manner.
- Extensive experiments demonstrate that the proposed method can achieve superior performance on two common synthetic-to-real semantic segmentation benchmarks with small amounts of labeled data.

## 2. Related Works

### Unsupervised domain adaptation for semantic segmentation.

Unsupervised domain adaptation (UDA) methods for semantic segmentation has been extensively studied to address domain discrepancy between photo-realistic synthetic dataset and unlabeled real dataset. One mainstream approach is by adversarial learning [42, 41, 6, 5, 17, 37, 19], which aims to employ a discriminator to measure the divergence across two domains. Another approach to solving UDA problem is to utilize generative networks [38, 16, 50] to generate target-style images by applying style transfer technique on annotated source image. Some methods based on self-training [21, 51, 23, 14] have been employed to generate pseudo labels of unlabeled data and use them to finetune the model. [21] firstly generate different stylized annotated images to learn texture-invariant representation and then use self-training to generate pseudo labels of unlabeled data to fine-tune the model on target domain.
Although impressive results have been achieved in UDA for semantic segmentation, the domain gap cannot be fully alleviated due to the lack of strong supervision in the target domain, and there is still an observed performance gap compared with their supervised counterparts.

### Semi-supervised learning for semantic segmentation
One way to reduce the heavy demand for manual pixel-wise labeling is to only label a small amount of data from target distribution and adopt semi-supervised learning (SSL) strategy to learn a great generalized model among ample unlabeled and limited labeled data. Numerous methods have since been developed to improve model generalization [30, 20, 9, 29, 18, 4, 13]. Consistency regularization is one of the most popular methods and the key idea is to encourage the network to give consistent predictions for perturbed unlabeled inputs. One most related work is [10], which enforces a consistency between mixed output of teacher network and the prediction of student over the mixed inputs by a region-level data augmentation CutMix [47] with a teacher-student architecture [40]. Our method also shares similar philosophy as theirs, however, we propose to train two domain-mixed teachers with two kinds of domain-mixing methods to fully exploit two sets of data from two different domains.

### Semi-supervised domain adaptation

Also aims to reduce the data distribution mismatch, compared with UDA, semi2 HUAWEI TECHNOLOGIES CO., LTD. Huawei Confidential 5 ℒ𝑘𝑙 Region-level teacher (b) Multi-teacher Knowledge Distillation Fixed 𝓜𝑆𝐿 Fixed 𝓜𝑅𝐿 𝓜𝑆 𝑬 x𝑖 𝑡 x𝑖 𝑢 𝑦𝑖 𝑡 𝑦𝑖 𝑡 x𝑖 𝑡 Region-level mixing ℒ𝑅𝐿 ℒ𝑆𝐿 Sample-level teacher (a) Domain-mixed Teachers based on Dual-level Domain Mixing 𝓜𝑅𝐿 𝓜𝑆𝐿 x𝑖 𝑟𝑙 x𝑖 𝑠 x𝑖 𝑡 𝑦𝑖 𝑡 𝑦𝑖 Mask 𝑀 𝑠 x𝑖 𝑠 𝑦𝑖 𝑟𝑙 Sample-level mixing Region-level teacher Figure 1. The first two stages of the proposed SSDA framework, training of domain-mixing teachers and multi-teacher knowledge distillation. The domain-mixed teachers are trained based on the dual-level mixed data. Then these two domain-mixed teachers are used to train a good student. The student will generate pseudo labels for next round of teachers training. E means the ensemble operation of two domain-mixed teachers. The black arrows represent the training data flow, blue arrows represent the data flow of inference, which do not require gradient backward. The red arrows represent the computation of losses.
supervised domain adaptation (SSDA) bridges domain discrepancy via introducing partially labeled target samples.
Recently, a few methods have been proposed based on deep learning [46, 32, 22, 35] for image classification. [46] decomposes SSDA into two sub-problems: UDA and SSL, and employ co-training [3] to exchange the expertise between two classifiers, which are trained on MixUp-ed [48] data between labeled and unlabeled data of each view.
Due to the complex densely pixel-wise prediction and no explicit decision boundaries between examples in semantic segmentation, SSDA methods based on discriminative class boundaries for image classification cannot be directly applied to semantic segmentation. Just one previous work have been developed to study SSDA for semantic segmentation. Wang et al. [44] propose Alleviating Semantic-level Shift (ASS) framework to realize feature alignment across domain from global and semantic level. ASS introduces an extra semantic-level adaptation module through adversarial training on the corresponding outputs of source and target labeled inputs besides the additional supervision on extra small amount of labeled target data upon the classical AdaptSeg framework [41]. However, the naive supervision of labeled target samples cannot fully take advantage of labeled two domains, and the adversarial loss makes training unstable due to the weak supervision. To solve this issue, we propose a novel iterative framework based on dual-level domain mixing methods without any adversarial training.

# 3. Method

![](/assets/images/21-09-19-semi-supervised-domain-adaptation-dual-level-domain-mixing-ssda-framework.png)

> The first two stages of the proposed SSDA framework, training of domain-mixing teachers and multi-teacher knowledge distillation. The domain-mixed teachers are trained based on the dual-level mixed data. Then these two domain-mixed teachers are used to train a good student. The student will generate pseudo labels for next round of teachers training. E means the ensemble operation of two domain-mixed teachers. The black arrows represent the training data flow, blue arrows represent the data flow of inference, which do not require gradient backward. The red arrows represent the computation of losses.

## 3.1. Problem Statement

In the setting of semi-supervised domain adaptation (SSDA), we are provided with a small set of labeled target domain images upon the large amounts of labeled source and unlabeled target domain images. Let DS = {(x s i , ys i )} NS i=1 represents the NS labeled source domain samples, and DT = {(x t i , yt i )} NT i=1 represents the NT labeled target domain samples, and DU = {x u i } NU i=1 represents the NU unlabeled target domain samples. With the SSDA setting, we aim at developing a way to efficiently utilize the available DS, DT and DU and obtain a segmentation model which has great performance on unseen test data sampled from target data distribution.

## 3.2. Domain-mixed Teachers

Performance degradation comes from inconsistent data distribution in different domains. We propose two data mixing methods for domain adaptation, one is region-level data mixing and the other is sample-level data mixing, to reduce the data distribution gap from two views. As we all know, data with labeled ground truth provides much information for training one model in deep learning-based methods. In SSDA, two types of labeled data, i.e., DS, DT , are provided. Our region-level and sample-level data mixing methods are implemented on these two kinds of labeled data, and two domain-mixed teacher models can be trained on the mixed data. Because of different views of data mixing, these two domain-mixed teachers are complementary.

### Region-level data mixing

Semantic segmentation is a dense pixel-wise prediction task, and the classification of one pixel depends not only on its own value but also on its regional neighbourhood’s context. Thus, if one image contains both source domain and target domain content, the model can learn domain-invariant representation because different regions with different feature distribution can be seen at the same time during model training.
3 Inspired by CutMix [47] where patches from an image are cut and pasted to another one to augment data for improving model’s generalization ability, here we propose to conduct region-level data mixing on set DS and DT to reduce domain gap. Given two labeled images {x t , yt}, {x s , ys}, the region-level mixing operation can be described as below.
x rl = M  x t + (1 − M)  x s , y rl = M  y t + (1 − M)  y s , (1) where M denotes a binary mask indicating where the region needs to fusion, and  is element-wise multiplication.
As shown in Fig. 1, the mixed image x rl contains both contents of x s and x t , and the corresponding mixed labels y rl are obtained for each pixel according to which domain the region containing that pixel comes from. In detail, a rectangular region is cropped from x s according to randomly chosen coordinates, and then pasted on the same location of x t . The region-level data mixing is able to produce intermediate samples between different domains, which works as a bridge, filling in the gap between domains. This helps explore essential semantic contexts across different domains from partial view. Additionally, this operation can destroy the inherent structure of the original target picture, and regularize the training process of region-level teacher. Once the mixed images and their labels are ready, we can train a semantic segmentation model through supervised training on the mixed data. The training objective function can be written as follows.
LRL = Lce(MRL(x rl), yrl), (2) where MRL represents teacher model trained on regionlevel mixed data, Lce denotes the cross entropy loss.

### Sample-level data mixing

Sample-level data mixing aims to mix the data from different domains from holistic view.
The source and target examples are sampled from inconsistent distribution with a big gap. We find that direct mixing of these data can already help reduce the gap between different domains to some extent. There are two advantages with sample-level data mixing method. On the one hand, the introduction of large amounts of source images alleviates the model overfitting to the small amount of target images. On the other hand, the sample-level mixing helps explore intermediate decision boundary between different domains from holistic view. In our experiments, we randomly sample two examples from source set DS and target set DT , then directly feed both of them into model during one iteration.
Given two images from DS and DT , the training objective function of sample-level teacher is defined as follows.
LSL = Lce(MSL(x s ), ys ) + Lce(MSL(x t ), yt ), (3) where MSL represents teacher model trained on samplelevel mixed data.
Algorithm 1 Training process of our proposed framework.
Require: labeled source dataset DS = {(x s i , ys i )} NS i=1, unlabeled target dataset DU = {x u i } NU i=1, labeled target dataset DT = {(x t i , yt i )} NT i=1, initialized weights of teachers model M0 RL, M0 SL and student model M0 S , iterative rounds R.
Procedure: 1: for r ← 1 to R do 2: Dual-level domain mixing 3: Optimize Mr RL and Mr SL by Eq. (2) and (3) .
Training two teachers 4: Optimize Mr S by Eq. (4) . Training student model 5: Generate pseudo labels yˆ u i following [23] by Mr S 6: Update DU = {x u i , yˆ u i } NU i=1 to labeled target dataset DT 7: end for 8: return student model MR S

## 3.3. Multi-teacher Knowledge

Distillation After obtaining two pre-trained domain-mixed teachers, we employ knowledge distillation (KD), a technique to distilling knowledge by minimizing the KL-divergence between outputs of these two models. Here we adapt it to extract “dark knowledge” from these two complementary teachers. The pipeline of multi-teacher KD is shown in Fig. 1 (b), including two pre-trained domain-mixed teachers and one student with the same network architecture as teacher. The outputs of two teachers are ensembled as a stronger guidance to supervise the training of the student model on unlabeled target data. Besides, the student model is also supervised by the labels on the small amount of labeled target data. The objective function of learning student MS is defined as below.
LS =λklLkl(E(MRL(x u ),MSL(x u )),MS(x u )) + λceLce(MS(x t ), yt ), (4) where λkl and λce are the weights of KL-divergence loss and cross entropy loss respectively, E denotes the ensemble operation of two models. In experiments, the ensemble operation is implemented by averaging the outputs of two complementary teachers.
By integrating knowledge from two views and making full use of unlabeled data, we can obtain one student with even superior performance than any one of its teachers.

## 3.4. Progressive Improving Scheme

Normally, a teacher network usually has stronger ability than student network. However, here a good student model is obtained by distilling knowledge from the ensembled outputs of two complementary domain-mixed models on large amount of unlabeled data. We focus on how to use a student 4 to further improve teachers’ performance for next step.
Recently, self-training as a simple but effective technique to address the scarceness of labeled training data, and are widely applied in SSL and UDA for image classification task. In our task, the teachers we obtain are trained based just on labeled source data and a small amount of labeled target data. Motivated by the success of self-training, we believe the teachers can be further improved with this strategy. In detail, following [23], pseudo labels of DU are generated via the learned student model to update the labeled set of images in dataset of DT for next round training of domain-mixed teachers. Once stronger domain-mixed teachers are obtained, a stronger student can be obtained by another round of multi-teacher KD.
Overall, the whole training process of our framework goes iteratively. Both the domain-mixed teachers and student are progressively growing, i.e., they can help the learning of each other through knowledge distillation and selftraining strategies. We summarize our proposed algorithm in Algorithm 1.

# 4. Experiments

## 4.1. Experimental

Setup Following the setting of unsupervised domain adaptation methods in semantic segmentation, we also conduct extensive experiments and report the mean intersection-overunion (mIoU) score on two commonly used synthetic-toreal benchmarks, which are GTA5 [33] and SYNTHIA [34] to Cityscapes [7] respectively.

Cityscapes is an autonomous driving dataset captured from 50 cities in real world. It contains densely annotated 2,975 and 500 images with a fixed resolution of 2048×1024 for training and validation respectively. All images are manually labeled by 19 semantic categories. For SSDA setting, we randomly select different numbers of images, such as (100, 200, 500, 1000), from the whole training set to demonstrate the effectiveness of our method across different settings. The validation set is used to evaluate the performance of our method.

GTA5 is a synthetic dataset in which the images are collected from game video and the corresponding semantic labels are automatically generated by computer graphics techniques. It includes 24,966 synthesized images with pixel-wise labels of 33 classes. In experiments, we consider the 19 common classes with Cityscapes dataset to train our models.

SYNTHIA is also a synthetic dataset and we use SYNTHIA-RAND-CITYSCAPES as another labeled source domain, which contains 9,400 fully annotated synthetic images with resolution of 1280×960. It has 16 common categories with Cityscapes dataset. We train our models with the common classes and report the 13-class mIoU on validation set.

## 4.2. Implementation Details

For all the following experiments, similar to [42], a DeeplabV2 [1] model, which contains Atrous Spatial Pyramid Pooling (ASPP) module to extract multi-scale representations and utilizes a pre-trained ResNet-101 [15] on ImageNet as backbone, are employed as our semantic segmentation architecture. To train our proposed framework, we implement it using Pytorch deep learning toolbox. All the experiments are conducted on a single Tesla V100 GPU with 32GB memory to accelerate computing.
An optional operation before training the model is to apply a simple image translation method to source domain images to reduce the visual difference between source and target domain. Here images are converted into LAB color space and are matched to the statistics of target domain. Image translation is applied at the beginning in most experiments except as otherwise noted. Then sample-level and region-level data mixing are conducted on labeled source data with target style and the target data. We then train domain-mixed teachers with cross entropy loss on supervised data. Student model is obtained on both labeled and unlabeled target data with cross entropy and KL loss.
The weight λkl, λce in Eq. 4 are set to 0.5 and 1 respectively. For self-training, the portion of selected pseudo labels and the confidence threshold are separately, similar to [23], set to 0.5 and 0.9. Iterative rounds R are set to 4 and 3 for GTA5→Cityscapes and SYNTHIA→Cityscapes respectively. All the models are trained by the Stochastic Gradient Descent (SGD) optimizer with initial learning rate 2.5×10−4 , the momentum 0.9 and weight decay 10−4 as mentioned in [42]. The learning rate is decreased with the 5 Ours Baseline Baseline.

## 4.3. Performance Comparison

Our proposed method is conducted on two common synthetic-to-real GTA5 to Cityscapes and SYNTHIA to Cityscapes benchmarks to demonstrate the effectiveness of proposed framework. The performance is compared with the baseline method and existing state-of-the-art methods on UDA, SSL and SSDA settings. More extensive experiments can be seen in supplementary materials.
Baseline. SSDA aims to alleviate the domain shift problem by introducing extra a small amount of labeled target data compared with UDA setting. As mentioned in Section 1, one naive way to address SSDA problem is by adding additional supervision upon UDA methods. Therefore, here we employ the classical UDA method named AdaptSeg [41], one multi-level adaptation method by adversarial learning on multi-level outputs, with extra supervised cross entropy loss on limited labeled target images as our baseline model.

### GTA5 to Cityscapes

![](/assets/images/21-09-19-semi-supervised-domain-adaptation-dual-level-domain-mixing-comparision-with-sota.png)

> Semantic segmentation performance comparison with the state-of-the-art UDA, SSL and SSDA methods on GTA5→Cityscapes. 19-class mIoU (%) score are reported on Cityscapes validation set across 0, 100, 200, 500, 1000, 2975 numbers of labeled target images. “∗” denotes our reimplementation on corresponding numbers of labeled Cityscapes images. GTA5 images are not introduced for implementing SSL methods. Best results are highlighted.

The performance comparisons with several state-of-the-art methods on GTA5 to Cityscapes are shown in Table 1. In experiment, iterative round R is set to 4. After the iterative training, our method achieves the best performance on different ratios of labeled target domain images compared with existing methods in UDA, SSL and SSDA settings. Compared with UDA methods such as AdaptSeg, Advent [42], LTI [21], and PIT [27], our method can obtain above 10% performance improvement by labeling just 100 target images and significantly reduce the performance gap compared with the oracle model. Particularly, our method outperforms the SSL methods CutMix [10] and DST-CBC [9], which use related CutMix and selftraining techniques respectively, by a large performance gain. ASS [44], to be our known, which is the first work on SSDA for semantic segmentation, employs additional semantic-level adaptation on the outputs of both labeled source and target images to alleviate semantic-level shift except the additional supervision. We modify MME [36], which is used to address image classification in SSDA setting, for semantic segmentation task, and obtain inferior results. We think the reason is that SSDA methods for classification without taking into account the semantic contexts in an image and cannot be directly applied to segmentation task. The proposed approach obtains superior results on all ratios of labeled data. The reason is that the supervision of adversarial learning is weak and we can fully take advantage of available labeled data to reduce domain gap by dual-level data mixing. In addition, our method also performs well on fully 2975 images with the performance of 69.8%.
In Fig. 2, we further display some qualitative segmentation results of both our method and baseline method on 100, 200, 500 and 1000 labeled target images. Overall, our method achieves more complete segmentation results than baseline model in the same ratio of labeled images. As the number of labeled images increases, more refined segmentation results we can obtain by our proposed approach.

### SYNTHIA to Cityscapes

![](/assets/images/21-09-19-semi-supervised-domain-adaptation-dual-level-domain-mixing-performance-comparision-sota.png)

> Semantic segmentation performance comparison with the state-of-the-art UDA, SSL and SSDA methods on SYNTHIA→Cityscapes. Here we train the DeeplabV2 model with 16 classes and report 13-class mIoU (%) score following the previous works on UDA. Other settings are kept same as in
Table 1. Best results are highlighted.

In order to further measure the performance of our approach, we also compare the results with several state-of-the-art methods on the SYNTHIA to Cityscapes. Since there are only 16 common categories between the SYNTHIA and Cityscapes, we just train a segmentation model with the common categories. As shown in Table 2, following previous UDA works [42, 41], we also report 13-class mIoU score to compare with existing other methods. From the results, it is clear that our method outperforms the UDA, SSL and SSDA methods with a large performance gain. And the similar discussions we can draw as in “GTA5 to Cityscapes”.

# 5. Ablation Study

## 5.1. Complementarity

![](/assets/images/21-09-19-semi-supervised-domain-adaptation-dual-level-domain-mixing-performance-comparison.png)

To examine the complementarity of models trained from different views, we select 100 labeled target images and train the domain-mixed teachers twice separately from sample-level and region-level mixed data. Then ensemble of different models including two region-level teachers and two sample-level teachers, are conducted and results are shown in Table 3. Overall, from Table 3, we can draw a conclusion that model ensemble is effective for improving the performance, and the ensembled models from dual-level data mixing views can achieve better results than that from single-level data mixing view. In detail, regionlevel teachers perform better in categories that can be predicted without strictly relying on the structural information, such as road, sidewalk, vegetation and sky. However, they have poor predictions on the fence, light and bus classes whose shape is distinctive. We explain that the regionlevel data mixing operation could destroy the structure of these classes. Although model ensemble from one single view can realize impressive results on its own advantageous categories, the categories with poor performance are still poor. For example, the ensembled model from two region-level models achieves the best IoU score on road and sidewalk classes, and the worst results on rider and fence classes. Such best-worst phenomenon also occurs in ensembled model of sample-level teachers, but on different categories compared with ones in region-level. So we can fuse the models with different complementary levels and achieve a good result in all categories.
We also visualize some segmentation results of ensemble of different-level models in Fig. 3. From Fig. 3, the pixels what is wrongly classified in one view will be corrected in another view.

## 5.2. Number of Iterative Rounds

We discuss our results reported in Table 1 during different rounds in the whole training process on GTA5 to Cityscapes, and the detailed results of three models, domain-mixed teachers and student, are shown in Table 4.
All three models can be improved with obvious performance gain compared with first round in training process.
During different rounds, the student model will outperform both of two teachers, and the stronger student will correct the learning of teachers through generating more accurate pseudo labels, thus the teachers and student are progressive growing. This demonstrates the effectiveness of our proposed iteratively framework. We notice that the best models are achieved in different rounds on different numbers of labeled images.

## 5.3. Image Translation

In the above experiments, a simple image translation method in LAB color space is firstly taken to further reduce the visual difference between different domain images.
Additionally, the experiments without style transfer are also conducted to demonstrate the effectiveness of our approach.
We just compare the results of two-domain mixed teachers and student model in the first round of our framework.
From Table 5, we can draw the following three observations. First, the student model using style transfer achieves better performance than ones without it. Therefore, duallevel data mixing with style transfer can further reduce distribution mismatch across domains. Secondly, the teacher model trained on region-level mixed data becomes insensitive as the number of images increases. Superior performance without style transfer is obtained on 500 labeled target images than using style transfer. We argue that the reason is that region-level data mixing is relatively robust to whether style transfer is conducted in the one patch cropped from source image. Because of significant improvement in sample-level data mixing, we can also obtain better results with style transfer. Finally, our proposed framework can obtain better results than ASS even if without style transfer on 100, 200, 500, 1000 labeled images.

# 6. Conclusion

In this paper, we propose a novel framework based on dual-level domain mixing to address semi-supervised domain adaptation problem. Two complementary domainmixed teachers can be obtained based on proposed two kinds of data mixing methods in both region-level and sample-level. Then a stronger student model on target domain can be by distilling knowledge from these two domain-mixed teachers. Finally, pseudo labels can be generated by self-training manner for next round training of domain-mixed teachers. Extensive experiments demonstrate the proposed framework can fully take advantage of available data, and achieve superior performance on two commonly used synthetic-to-real benchmarks.

</div>
</details>

---

# Method

![](/assets/images/21-09-04-domain-adaptation-dual-level-domain-mixing-SSDA%20framework.png)

Domin을 mixing 한 multi teache 를 이용해 student model에게 각기 다른 도메인에 대한 정보를 학습시켜준다.

- Region-level teacher
- Sample-level teacher

두개의 teacher model을 ensemble한 모델이 domain-mixed teachers 이다.  
Domain gap을 줄이기 위해 Region-level data mixing, sample-level data mixing 두가지 view를 이용한다.

### Region-level data mixing

Semantic segmentation은 dense pixel-wise prediction task로, 각 픽셀의 prediction 값 외에 주변 pixel의 regional 한 prediction 값도 필요하다. 한 이미지에 source, target 도메인 이미지가 함께 포함되어 있으면 모델은 domain-invariant representation을 배울 수 있다. 다른 feature distribution와 다른 region을 가진 값은 두가지를 동시에 배울 수 있기 때문이다. 이 아이디어는 CutMix 에서 영감을 받았다. (모델의 generalization 특성을 향상시키기 위해 각기 다른 이미지를 붙여서 augment 함) 그래서 본 논문에는 비슷한 형태로 source domain 이미지와 target domain 이미지를 섞어서 teacher model의 학습 데이터로 사용함으로써 domain gap을 줄인다. 

### Sample-level data mixing

Holistic view에서 다른 도메인의 데이터를 섞는다. Source와 target 데이터는 큰 distribution gap이 있을거다. Source 데이터 만으로만 학습을 하면 overfitting 모델이 만들어질 수 있기 때문에 source, target 데이터를 랜덤으로 샘플링해서 teacher model의 학습 데이터로 사용한다.

## Multi-teacher Knowledge Distillation

이제 pretrain된 domain-mixed teacher 모델을 두개 얻었다. 그 다음은 Knowledge Distillation(KD)를 이용해 두 모델간 KL-divergence를 최소화 해야한다. 

두개의 teacher 모델이 있고 비슷한 형태의 student 모델이 하나 있다. 두개의 teacher model을 앙상블해서 unlabeled data에 labeling 하고 그 데이터로 student 모델을 학습시킨다. 그리고 student model은 적은 양의 labeled data로 supervised learning  한다.

## Progressive Improving Scheme

일반적으로 teacher model의 성능이 더 좋은데 distilling knowledge를 이용해 studnet model도 teacher model의 성능으로 끌어올려보자. 

Labeled source data와 소량의 labeled target data를 통해 teacher model을 얻었다. 

이 framework는 iteratively 학습이 진행될거고 domain-mixed teacher와 student는 모두 knowledge distillation와 self-training 기법을 통해 모두 성능이 향상된다.
