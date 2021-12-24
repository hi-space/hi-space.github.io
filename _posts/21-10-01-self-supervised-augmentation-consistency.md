---
title: Self-supervised Augmentation Consistency for Adapting Semantic Segmentation (da-sac)
category: AI
tags: ai paper
article_header:
    type: overlay
    theme: dark
    background_color: "#123"
    background_image: false
cover: /assets/images/21-10-01-self-supervised-augmentation-consistency-2021-10-01-19-39-57.png
---

- CVPR 2021
- [Paper](https://arxiv.org/pdf/2105.00097v1.pdf)
- [Github](https://github.com/visinf/da-sac)
- Self supervised learning 을 이용해 end-to-end로 공동 진화하는 pseudo labeling을 사용해 모델을 training 시키는 방법을 제안

<!--more-->

# Introduction

Self supervised learning 을 이용해 end-to-end로 공동 진화하는 pseudo labeling을 사용해 모델을 training 시키는 방법을 제안한다.

# Related Works

GAN을 이용해 input-level에서 source 와 target domain 간의 gap을 줄이는 방법이 있다.(like style-transfer) 그리고 Discriminator를 사용해 source와 target 데이터 간의 confusion 을 유도할 수도 있다. 

Pseudo labeling 은 offline 이라는 label을 미리 계산하고 이후에 모델을 업데이트 하는데 사용한다. 여러 라운드 동안 이 프로세스를 반복하는 형식이다. 이 방법은 adversarial training, style transfer 모델 등에 의존적이고, labeling 시에 불안정할 수 있기 때문에 추가 정규화가 필요하다. 이에 따라 entropy minimization을 추가 적용하거나 dorpout 기반 샘플링을 하기도 한다.

Semantic segmentation은 classification 문제와 달리 spatial 한 정보를 사용해야 한다. 

# Methods 

본 framework는 adversarial learning을 사용하지 않고, Pseudo labeling 모델이 함께 결합되어 training 한다. Noisy Teacher와 유사하고 consistancy regularization과 self ensemble을 결합한 형태이다. 

![](/assets/images/21-10-01-self-supervised-augmentation-consistency-2021-10-01-19-39-57.png)

target domain에 적용하기 위한 semantic segmentation 모델과 momentum network로 구성된다.

1. self supervised domain adaptation을 수행하기 위해, target domain의 샘플 이미지를 Crop, flip 한다.
2. 각 픽셀에 대해 semantic mask를 평균화 하고
3. confident가 높은 픽셀을 선택하여 pseudo labeling을 만든다
4. semantic segmentation은 stochastic gradient 를 이용해 pseudo labeling에 대한 parameter를 업데이트 한다.

---

# Abstract

We propose an approach to domain adaptation for semantic segmentation that is both practical and highly accurate. In contrast to previous work, we abandon the use of computationally involved adversarial objectives, network ensembles and style transfer. Instead, we employ standard data augmentation techniques – photometric noise, flipping and scaling – and ensure consistency of the semantic predictions across these image transformations. We develop this principle in a lightweight self-supervised framework trained on co-evolving pseudo labels without the need for cumbersome extra training rounds. Simple in training from a practitioner’s standpoint, our approach is remarkably effective. We achieve significant improvements of the state-ofthe-art segmentation accuracy after adaptation, consistent both across different choices of the backbone architecture and adaptation scenarios.

# 1. Introduction

Unsupervised domain adaptation (UDA) is a variant of semi-supervised learning [6], where the available unlabelled data comes from a different distribution than the annotated dataset [4]. A case in point is to exploit synthetic data, where annotation is more accessible compared to the costly labelling of real-world images [59, 60]. Along with some success in addressing UDA for semantic segmentation [67, 69, 80, 91], the developed methods are growing increasingly sophisticated and often combine style transfer networks, adversarial training or network ensembles [39, 46, 68, 77]. This increase in model complexity impedes reproducibility, potentially slowing further progress.

In this work, we propose a UDA framework reaching state-of-the-art segmentation accuracy (measured by the Intersection-over-Union, IoU) without incurring substantial training efforts. Toward this goal, we adopt a simple semisupervised approach, self-training [12, 42, 91], used in recent works only in conjunction with adversarial training or Code is available at https://github.com/visinf/da-sac.

![](/assets/images/21-10-01-self-supervised-augmentation-consistency-2021-10-01-19-36-31.png)

> Results preview. Unlike much recent work that combines multiple training paradigms, such as adversarial training and style transfer, our approach retains the modest single-round training complexity of self-training, yet improves the state of the art for adapting semantic segmentation by a significant margin.

network ensembles [17, 39, 54, 70, 80, 87, 86]. By contrast, we use self-training standalone. Compared to previous selftraining methods [9, 43, 65, 91, 92], our approach also sidesteps the inconvenience of multiple training rounds, as they often require expert intervention between consecutive rounds. We train our model using co-evolving pseudo labels end-to-end without such need.

Our method leverages the ubiquitous data augmentation techniques from fully supervised learning [11, 85]: photometric jitter, flipping and multi-scale cropping. We enforce consistency of the semantic maps produced by the model across these image perturbations. The following assumption formalises the key premise: {}

Next, we introduce a training framework using a momentum network – a slowly advancing copy of the original model.

The momentum network provides stable, yet recent targets for model updates, as opposed to the fixed supervision in model distillation [15, 87, 86]. We also re-visit the problem of long-tail recognition in the context of generating pseudo labels for self-supervision. In particular, we maintain an exponentially moving class prior used to discount the confidence thresholds for those classes with few samples and increase their relative contribution to the training loss. Our framework is simple to train, adds moderate computational overhead compared to a fully supervised setup, yet sets a new state of the art on established benchmarks (cf. Fig. 1).

# 2. Related Work

Most of the work on scene adaptation for semantic segmentation has been influenced by a parallel stream of work on domain adaptation (DA) and semi-supervised learning for image classification [23, 24, 27, 45, 50]. The main idea behind these methods is to formulate an upper bound on the target risk using the so-called H∆H-divergence [3]. In a nutshell, it defines the discrepancy between the marginals of the source and target data by means of a binary classifier.

In the following, we briefly review implementation variants of this idea in the context of semantic segmentation.

## Learning domain-invariant representations.

Adversarial feature alignment follows the GAN framework [24, 26] and minimises the gap between the source and target feature representations in terms of some distance (e.g., Wasserstein in [41]). The discriminator can be employed at multiple scales [15, 67, 77] and use local spatial priors [83]; it can be conditional [33] and class-specific [22, 52], or align the features of ‘hard’ and ‘easy’ target samples [56]. Often, self-supervised losses, such as entropy minimisation [69], or a ‘conservative loss’ [90] assist in this alignment.

The alternative to adversarial feature alignment are more interpretable constraints, such as feature priors [51], bijective source-target association [37] or aligning the domains directly in the image space with style transfer [89] used either alone [74] or, most commonly, jointly with adversarial feature alignment [8, 16, 25, 55, 78, 79, 82]. One issue with style translation is to ensure semantic consistency despite the changes in appearance. To address this, Hoffman et al. [32] use semantic and cycle-consistency losses, while Yang et al. [77] reconstruct the original image from its label-space representation.

These methods tend to be computationally costly and challenging to train, since they require concurrent training of one or more independent networks, e.g. discriminators or style transfer networks. Although Yang and Soatto [80] obviate the need for style networks by incorporating the phase of a Fourier-transformed target image into a source sample, multiple networks have to be trained, each with its own predefined phase band.

## Self-training on pseudo labels.

As a more computationally lightweight approach, self-training seeks high-quality pseudo supervision coming in the form of class predictions with high confidence. Our work belongs to this category.

Most of such previous methods pre-compute the labels ‘offline’, used subsequently to update the model, and repeat this process for several rounds [43, 65, 91, 92]. More recent frameworks following this strategy have a composite nature: they rely on adversarial (pre-)training [14, 20, 86], style translation [17, 80] or both [54, 46, 39, 70, 73].

Training on co-evolving pseudo labels can be computationally unstable, hence requires additional regularisation.

Chen et al. [13] minimise the entropy with improved behaviour of the gradient near the saturation points. Using fixed representations, be it from a ‘frozen’ network [15, 86], a fixed set of global [53] or self-generated local labels [47, 68, 81], further improves training robustness.

Overconfident predictions [28] have direct consequences for the quality of pseudo labels. Zou et al. [92] attain some degree of confidence calibration via regularising the loss with prediction smoothing akin to temperature scaling [28].

Averaging the predictions of two classifiers [87], or using Dropout-based sampling [7, 88], achieves the same goal.

## Spatial priors.

Different from DA for classification, the characteristic feature of adaptation methods for segmentation is the use of spatial priors. Local priors have been enforced patch-wise [15, 47, 68] and in the form of precomputed super-pixels [81, 83]. Although global spatial priors have also been used [91], their success hinges on the similarity of the semantic layout in the current benchmarks.

## Relation to our approach.

As shown in Table 1, our work streamlines the training process. First, we do not use adversarial training, as feature invariance alone does not guarantee label invariance [36, 84]. Second, we train our model with co-evolving pseudo labels in one round. Our framework bears resemblance to the noisy mean teacher [76] and combines consistency regularisation [2, 61, 64, 75] with self-ensembling [40, 66]. Similar approaches have been explored in medical imaging [44, 58] and concurrent UDA work [71], albeit limited in the scope of admissible augmentations. We leverage photometric invariance, scale and flip equivariance [72] to extract high-fidelity pseudo supervision instead of more computationally expensive sampling techniques [38]. Contrary to [65], we find that scale alone is not predictive of the label quality, hence we average the predictions produced at multiple scales and flips. This parallels uncertainty estimation using test-time augmentation [1], but at training time [5].

# 3. Self-Supervised Augmentation Consistency

## 3.1. Framework overview

![](/assets/images/21-10-01-self-supervised-augmentation-consistency-2021-10-01-19-39-57.png)

> Figure 2. Overview. The segmentation network in our framework (a) maintains a slow copy of itself, the momentum network, which provides stable targets for self-supervision. In addition to encouraging semantic invariance w.r.t. the photometric noise, we facilitate consistent predictions across multiple scales and flips by first (b) feeding random multi-scale crops and flips to the momentum network and then (c) fusing the predictions by simple averaging to produce the pseudo-supervision targets.

Shown in Fig. 2a, our framework comprises a segmentation network, which we intend to adapt to a target domain, and its slowly changing copy updated with a momentum, a momentum network. To perform self-supervised scene adaptation, we first supply a batch of random crops and horizontal flips from a sample image of the target domain to both networks. For each pixel we average the predictions (i.e. semantic masks) from the momentum network after the appropriate inverse spatial transformation. We then create a pseudo ground truth by selecting confident pixels from the averaged map using thresholds based on running statistics, which are capable of adapting to individual samples.
Finally, the segmentation network uses stochastic gradient descent to update its parameters w.r.t. these pseudo labels.

Our approach closely resembles the mean teacher framework [23, 66] and temporal ensembling [35, 40]. However, as we will show empirically, the ensembling property itself plays only an auxiliary role. More importantly, akin to the critic network in reinforcement learning [48] and the momentum encoder in unsupervised learning [30], our momentum network provides stable targets for self-supervised training of the segmentation network. This view allows us to focus on the target-generating process, detailed next.

## 3.2. Batch construction

For each sampled target image, we generate N crops with random scales, flips and locations, but preserving the aspect ratio. We re-scale the crops as well as the original image to a fixed input resolution h×w and pass them as the input to the networks. Fig. 2b demonstrates this process. Following the noisy student model in image classification [76], the input to the segmentation network additionally undergoes a photometric augmentation: we add random colour jitter and smooth the images with a Gaussian filter at random. The momentum network, on the other hand, receives a ‘clean’ input, i.e. without such augmentations. This is to encourage model invariance to photometric perturbations.

## 3.3. Self-supervision

### Multi-scale fusion.

We re-project the output masks from the momentum network back to the original image canvas of size h × w, as illustrated in Fig. 2c. For each pixel, the overlapping areas average their predictions. Note that some pixels may lie outside the crops, hence contain the result of a single forward pass with the original image. We keep these predictions intact. The merged maps are then used to extract the pseudo masks for self-supervision.

### A short long-tail interlude.

Handling rare classes (i.e. classes with only a few training samples) is notoriously difficult in recognition [29]. For semantic segmentation, we here distinguish between the classes with low image-level (e.g., “truck”, “bus”) and pixel-level (e.g., “traffic light”, “pole”) frequency. While generating self-supervision, we take special care of these cases and encourage (i) lower thresholds for selecting their pseudo labels, (ii) increased contributions to the gradient with a focal loss, and (iii) employ importance sampling. We describe these in detail next.

### Sample-based moving threshold.

Most previous work with self-training employs multi-round training that requires interrupting the training process and re-generating the pseudo labels [43, 46, 54, 65, 91]. One of the reasons is the need to re-compute the thresholds for filtering the pseudo labels for supervision, which requires traversing the predictions for the complete target dataset with the model parameters fixed. In pursuit of our goal of enabling end-to-end training without expert intervention, we take a different approach and compute the thresholds on-the-go.

As the main ingredient, we maintain an exponentially moving class prior. In detail, for each softmax prediction of the momentum network, we first compute a prior estimate of 3 the probability that a pixel in sample n belongs to class c as χc,n = 1 hw X i,j mc,n,i,j , (1) where mc,n,:,: is the mask prediction for class c (with resolution h × w). We keep an exponentially moving average after each training iteration t with a momentum γχ ∈ [0, 1]: χ t+1 c = γχχ t c + (1 − γχ)χc,n. (2) Our sample-based moving threshold θc,n takes lower values when the moving prior χc ≈ 0 (i.e. for long-tail classes), but is bounded from above as χc → 1. We define it as θc,n = ζ 
1 − e −χc/β m∗ c,n, (3) where β and ζ are hyperparameters and m∗ c,n is the predicted peak confidence score for class c, i.e.
m∗ c,n = max i,j mc,n,i,j . (4) Fig. 3 plots Eq. (3) as a function of the moving class prior χc for a selection of β. For predominant classes (e.g., “road”), the exponential term has nearly no effect; the threshold is static w.r.t. the peak class confidence, i.e. θc,n ≈ ζm∗ c,n.
However, for long-tail classes such that χc ≈ β, the threshold is lower than this upper bound, hence more pixels for these classes are selected for supervision. To obtain the pseudo labels, we apply the threshold θc,n to the peak predictions of the merged output from the momentum network: mˆ n,i,j = ( c ∗ mc ∗,n,i,j > θc,n ignore otherwise, (5) where c ∗ = arg maxc mc,n,i,j is the dominant class for that pixel. Note that the pixels with confidence values lower than the threshold, as well as non-dominant predictions, will be ignored in the self-supervised loss.

### Focal loss with confidence regularisation.

Our loss function incorporates a focal multiplier [49] to further increase the contribution of the long-tail classes in the gradient signal. Unlike previous work [49, 65], however, our moving class prior χc regulates the focal term: L t n ( ¯m, m | φ) = −mc ∗,n(1 − χc ∗ ) λ log( ¯mc ∗,n), (6) where m¯ is the prediction of the segmentation network with parameters φ, the pseudo label c ∗ derives from mˆ in Eq. (5) and λ is a hyperparameter of the focal term. Recall that low values of χc signify a long-tail category, hence should have a higher weight. High values of λ (i.e. > 1) increase the relative weighting on the long-tail classes, while setting λ = 0 disables the focal term. Note that we also regularise our loss with the confidence value of the momentum network, mc ∗,n (Eq. 4). In case of an incorrect pseudo label, we expect this confidence to be low and to regularise the training owing to its calibration with the multi-scale fusion. We minimise the loss in Eq. (6), applied for each pixel, w.r.t. φ.
0 0.2 0.4 0.6 0.8 1 θc,n 0 0.01 0.02 0.03 0.04 χc ζ m∗ c,n β = 0.01 β = 0.005 β = 0.02

## 3.4. Training

### Pre-training with source-only loss.

Following [47, 83], we use Adaptive Batch Normalisation (ABN) [45] to jumpstart our model on the segmentation task by minimising the cross-entropy loss on the source data only. In our experiments, we found it unnecessary to re-compute the mean and the standard deviation only at the end of the training.

Instead, in pre-training we alternate batches of source and target images, but ignore the loss for the latter. For a target batch, this implies updating the running mean and the standard deviation in the Batch Normalisation (BN) [34] layers and leaving the remaining model parameters untouched.

### Importance sampling.

Our loss function in Eq. (6) accounts for long-tail classes with a high image frequency (e.g., “traffic light”, “pole”), and may not be effective for the classes appearing in only few samples (e.g., “bus”, “train”). To alleviate this imbalance, we use importance sampling [21] and increase the sample frequency of these long-tail classes. We minimise the expected target loss by re-sampling the target images using the density pt: min φ En∼pt L t n (φ) . (7) To obtain pt, we use our pre-trained segmentation network and pre-compute χc,n, the class prior estimate, for each image n using Eq. (1). At training time, we (i) sample a semantic class c uniformly, and then (ii) obtain a target sample l with probability χˆc,l = P χc,l n χc,n . (8) This two-step sampling process ensures that all images have non-zero sample probability owing to the prevalent classes for which χˆc,l > 0 for all l (e.g., “road” in urban scenes).

### Joint target-source training.

![](/assets/images/21-10-01-self-supervised-augmentation-consistency-2021-10-01-19-43-24.png)

> Self-supervision example. In this image sample (a) and its crops, the segmentation network (b) tends to mistake the “motorcycle”
for a “bicycle”. The momentum network (c) improves on this prediction, but may still produce an inconsistent labelling. Averaging the
predictions over multiple scales (d) corrects this inconsistency, allowing to produce high-precision pseudo labels (e) for self-supervision.

We train the segmentation network with stochastic gradient descent using the crossentropy loss for the source and our focal loss for the target data sampled from pt, as defined by Eqs. (6) and (7). Fig. 4 illustrates the synthesis of pseudo labels. We periodically update the parameters ψ of the momentum network as ψt+1 = γψψt + (1 − γψ)φ, (9) where φ are the parameters of the segmentation network. γψ regulates the pace of the updates: low values result in faster, but unstable training, while high γψ leads to a premature and suboptimal convergence. We keep γψ moderate, but update the momentum network only every T iterations.

# 4. Experiments

### Datasets.

In our experiments we use three datasets. The Cityscapes dataset [18] contains 2048 × 1024 images from real-world traffic scenes, split into 2975 images for training and 500 for validation. The GTA5 dataset [59] contains 24 966 synthetic scenes with resolution 1914 × 1052 and pixelwise annotation aided by the GTA5 game engine.

We also use the SYNTHIA-RAND-CITYSCAPES subset of the SYNTHIA dataset [60], which contains 9400 synthetic images with resolution 1280 × 760 and a semantic annotation compatible with Cityscapes.

### Setup.

We adopt the established evaluation protocol from previous work [47, 67, 69]. The synthetic traffic scenes from GTA5 [59] and SYNTHIA [60] serve as the source data, and the real images from the Cityscapes dataset as the target (obviously ignoring the available semantic labels).

This results in two domain adaptation scenarios depending on the choice of the source data: GTA5 → Cityscapes and SYNTHIA → Cityscapes. As in previous work, at training time we only use the training split of the Cityscapes dataset and report the results on the validation split. We measure the segmentation accuracy with per-class Intersection-overUnion (IoU) and its average, the mean IoU (mIoU).

## 4.1. Implementation details

We implement our framework in PyTorch [57]. We adopt DeepLabv2 [10] as the segmentation architecture, and evaluate our method with two backbones, ResNet-101 [31] and VGG16 [63], following recent work [39, 67, 68, 69, 73].
Both backbones initialise from the models pre-trained on ImageNet [19]. We first train the models with ABN [45] (cf. Sec. 3.4), implemented via SyncBN [57], on multiscale crops resized to 640 × 640 and a batch size of 16.

Next, training proceeds with the self-supervised target loss (cf. Sec. 3.3) and the BatchNorm layers [34] frozen. The batch size of 16 comprises 8 source images and 8 target images at resolution 1024 × 512, which is a common practice [70, 80]. The target batch contains only two image samples along with 3 random crops each (i.e. N = 3 in Sec. 3.2), downscaled up to a factor of 0.5. As the photometric noise, we use colour jitter, random blur and greyscaling (see Appendix B for details). The optimisation uses SGD with a constant learning rate of 2.5 × 10−4 , momentum 0.9 and weight decay of 5×10−4 . We accumulate the gradient in alternating source-target forward passes to keep the memory footprint in check. Since the focal term in Eq. (6) reduces the target loss magnitude w.r.t. the source loss, we scale it up by a factor of 5 (2 for VGG-16). We train our VGGbased framework on two TITAN X GPUs (12GB), while the ResNet-based variant requires four. This is a substantially reduced requirement compared to recent work (e.g., FADA [70] requires 4 Tesla P40 GPUs with 24GB memory). Note that the momentum network is always in evaluation mode, has gradient tracking disabled, hence adds only around 35% memory overhead. For the momentum network, we fix γψ = 0.99 and T = 100 in all our experiments. For the other hyperparameters, we use γχ = 0.99, ζ = 0.75, β = 10−3 and λ = 3. Appendix C.2 provides further detail on hyperparameter selection, as well as a sensitivity analysis of our framework w.r.t. ζ and β. The inference follows the usual procedure of a single forward pass through the segmentation network at the original image resolution without any post-processing.

## 4.2. Comparison to state of the art

We compare our approach to the current state of the art on the two domain adaptation scenarios: GTA5 → Cityscapes in Table 2 and SYNTHIA → Cityscapes in Table 3. For a fair comparison, all numbers originate from single-scale inference. In both cases, our approach, denoted as SAC (“Self-supervised Augmentation Consistency”), substantially outperforms our baseline (i.e. the source-only loss model with ABN, see Sec. 3.4), and, in fact, sets a new state of the art in terms of mIoU. 

Importantly, while the ranking of previous works depends on the backbone choice and the source data, we reach the top rank consistently in all settings.

![](/assets/images/21-10-01-self-supervised-augmentation-consistency-2021-10-01-19-46-32.png)

### GTA5 → Cityscapes (Table 2).

Our method achieves a clear improvement over the best published results [55, 83] of +3.4% and +1.2% using the VGG-16 and ResNet-101 backbones, respectively. Note that RPT [83] and SA-I2I [55] have a substantially higher model complexity. RPT [83] uses PSPNet [85], which has a higher upper bound than DeepLabv2 in a fully supervised setup (e.g., +5.7% IoU on PASCAL VOC [85]); it requires extracting superpixels and training an encoder-decoder LSTM, thus increasing the model capacity and the computational overhead.

SA-I2I [55] initialises from a stronger baseline, BDL [46], and relies on a style transfer network and adversarial training. While both RPT [83] and SA-I2I [55] require multiple rounds of training, 3 and 6 (from BDL [46]), respectively, we train with the target loss in a single pass. Notably, compared to the previous best approach for VGG with a ResNet evaluation, SA-I2I [55], our improvement with ResNet-101 is substantial, +3.4%, and is comparable to the respective margin on VGG-16.

### SYNTHIA → Cityscapes (Table 3).

Here, the result is consistent with the previous scenario. Our approach attains state-of-the-art accuracy for both backbones, improving by 7.6% and 1.4% with VGG-16 and ResNet-101 backbones over the best results previously published [55, 83]. Again, our method with ResNet-101 outperforms the previous best method with full evaluation, PyCDA [47], by 5.9% IoU.

Remarkably, in both settings our approach is more accurate or competitive with many recent works [65, 70, 77] even when using a weaker backbone, i.e. VGG-16 instead of ResNet-101. This is significant, as these improvements are not due to increased training complexity or model capacity, in contrast to these previous works. Additional results, including the evaluation on Cityscapes test, are shown in Appendices C and D.

# 5. Conclusion
   
We presented a simple and accurate approach for domain adaptation of semantic segmentation. With ordinary augmentation techniques and momentum updates, we achieve state-of-the-art accuracy, yet make no sacrifice of the modest training or model complexity. No components of our framework are strictly specialised; they build on a relatively weak and broadly applicable assumption (cf. Sec. 1). Although this work focuses on semantic segmentation, we are keen to explore the potential of the proposed techniques for adaptation of other dense prediction tasks, such as optical flow, monocular depth, panoptic and instance segmentation, or even compositions of these multiple tasks.

### Acknowledgements.

This work has been co-funded by the LOEWE initiative (Hesse, Germany) within the emergenCITY center. Calculations for this research were partly conducted on the Lichtenberg high performance computer of TU Darmstadt.
