---
title: A Review of Single-Source Deep Unsupervised Visual Domain Adaptation
category: AI
tags: ai paper
---

A Review of Single-Source Deep Unsupervised Visual Domain Adaptation
  
<!--more-->
  
- 2020
- [Paper](https://arxiv.org/pdf/2009.00155.pdf)
- [Related Post](https://hi-space.github.io/ai/2021/09/04/source-free-domain-adaptation-brief.html)

# Abstract

Large-scale labeled training datasets have enabled deep neural networks to excel across a wide range of benchmark vision tasks. However, in many applications, it is prohibitively expensive and time-consuming to obtain large quantities of labeled data. To cope with limited labeled training data, many have attempted to directly apply models trained on a large-scale labeled source domain to another sparsely labeled or unlabeled target domain. Unfortunately, direct transfer across domains often performs poorly due to the presence of domain shift or dataset bias. Domain adaptation is a machine learning paradigm that aims to learn a model from a source domain that can perform well on a different (but related) target domain.
In this paper, we review the latest single-source deep unsupervised domain adaptation methods focused on visual tasks and discuss new perspectives for future research. We begin with the definitions of different domain adaptation strategies and the descriptions of existing benchmark datasets. We then summarize and compare different categories of single-source unsupervised domain adaptation methods, including discrepancy-based methods, adversarial discriminative methods, adversarial generative methods, and selfsupervision-based methods. Finally, we discuss future research directions with challenges and possible solutions.

# I. INTRODUCTION

In the last decade, deep neural networks (DNNs) have achieved significant progress in various computer vision tasks where large-scale labeled training data are available. For example, the classification error of the “Classification + localization with provided training data” task in the Large Scale Visual Recognition Challenge was reduced from 0.28 in 2010 to 0.022 in 20171 , even outperforming humans. However, in many applications, it is difficult to obtain a large amount of labels, as manual annotation is expensive and time-consuming.

An alternative solution is to train a model on another related large-scale source domain with labels (e.g. a simulation domain) and apply it to the unlabeled target domain (e.g. a real-world domain). However, due to the presence of domain shift or dataset bias [1], such a direct transfer might not perform well, as shown in Figure 1.

One may argue that the pre-trained source models can be fine-tuned in the target domain. However, fine-tuning still requires considerable quantities of labeled training data, which may be not available for many applications. For example, in fine-grained recognition, only experts are able to provide reliable labeled data [2]; in semantic segmentation, it takes about 90 minutes to label each image in the Cityscapes dataset [3]; in autonomous driving, the substantial traffic data obtained with different sensors, such as 3D LiDAR point clouds, are difficult to label [4, 5]; in affective image content analysis, the perceived emotions are subjective and personalized across viewers [6, 7].

## a) Domain Adaptation in context of other sample efficient learning methods

Domain adaptation techniques were introduced to addresses the domain shift between source and target domains [8] and for this reason, they have recently attracted significant interest in both academia and industry.

Domain adaptation (DA), also known as domain transfer, is a specialized form of transfer learning [9] that aims to learn a model from a labeled source domain that can generalize well to a different (but related) unlabeled or sparsely labeled target domain. It belongs to the sample-efficient learning class [10], together with zero-shot learning, few-shot learning, and selfsupervised learning. We briefly compare domain adaptation with other methods in this category. While the unsupervised domain adaptation (UDA) does not require the annotations of target data, it usually needs a sufficient number of unlabeled target samples to train the model [11]. Compared to UDA, zero-shot learning does not need either the target data annotations or the unlabeled target samples [12, 13]. However, existing methods often require some auxiliary information, such as the attributes of the images, or the description of the classes [14, 15]. Further, zero-shot learning is trained on known/seen classes and tested on unknown/unseen classes, which demands the model to generalize from known/seen classes to unknown/unseen classes. Since the known/seen classes and the unknown/unseen classes are from different distributions, there is no concept of domain shift in zeroshot learning. Different from zero-shot learning, DA deals with the same learning tasks on different domains. Taking image classification as an example, both source data and target data have the same classes. Few-shot learning shares similar setting with zero-shot learning. The difference is that fewshot learning has a few (e.g. 5 or 10) annotated samples for the unknown/unseen classes [16–18]. Few-shot learning and zero-shot learning can also be grouped as low-shot learning.

Self-supervised learning (SSL) is a learning paradigm that captures the intrinsic patterns and properties of input data without using human-provided labels [19]. The basic idea of SSL is to construct some auxiliary tasks solely based on the data itself without using human-annotated labels and force the network to learn meaningful representations by performing the auxiliary tasks well. Typical self-supervised learning approaches generally involve two aspects: constructing auxiliary tasks and defining loss functions [20]. The auxiliary tasks are designed to encourage the model to learn meaningful representations of input data. The loss functions are defined to measure the difference between a modelâA˘ Zs prediction and a fixed ´ target, the similarities of sample pairs in a representation space (e.g., contrastive loss), or the difference between probability distributions (e.g., adversarial loss). Compared with domain adaptation, SSL does not specifically address the domain shift problem between different domains.

## b) Domain Adaptation Challenges

Albeit DA is a very effective method, it is not without blemish. The main challenge for single-source UDA is domain shift [1], i.e., the difference between the source and target distributions that leads to unreliable predictions on the target domain. Typically, three types of domain shift are considered: covariate shift, label shift, and concept drift (see Section II for details).
As Figure 1 shows, the presence of domain shift causes the direct transfer of models trained on the source domain to perform poorly on the target domain. Figure 1 (a) shows an example of image-level object classification on the OfficeHome dataset [21]. When training a ResNet-50 model [22] on the target Clipart domain, we can obtain a promising 96.0% object classification accuracy. However, if we train the model on the source Art domain and directly test it on the target domain, the accuracy significantly drops to 34.9%.
Figure 1 (b) shows an example of simulation-to-real adaptation, which is a more realistic application with unlimited synthetic labeled data created by graphics and simulation infrastructure. For example, CARLA2 , GTA-V3 , and Autoware.AI4 are three popular simulators for autonomous driving research. While there are ongoing efforts to make simulations more realistic, it is very difficult to model all the characteristics of real data [23]. Using the FCN model [24] to conduct pixelwise segmentation on a real target dataset Cityscapes [3], direct transfer from synthetic GTA [25] only obtains a mean intersection-over-union (mIoU) of 21.7%, which is much lower than the mIoU 62.6% of the model trained on real Cityscapes.

## c) Focus of this Survey and Comparison with Other Surveys

There are many different domain adaptation settings (see Section II for details). Our survey focuses on the most prevalent one: single-source, single-target, homogeneous, and closed set adaptation without target labels. In this setting, there is one fully labeled source domain and one unlabeled target domain within the same modality, and the source and target domains share the same label set.
The early unsupervised domain adaptation (UDA) methods were mainly non-deep approaches, which aimed to match the feature distributions between the source domain and the target domain. Single-source UDA methods can be divided into two categories [26, 27]: sample re-weighting [28, 29] and intermediate subspace transformation [30–34].
With the advent of deep learning, the emphasis has shifted to end-to-end learning domain-invariant features. Typically, for single-source deep UDA (DUDA) [8, 35], a conjoined architecture with two streams is employed to represent the models for the source and target domains, respectively [36].

Besides the traditional task loss, such as cross-entropy loss for classification, based on the labeled source data, DUDA models are usually trained jointly with another loss to deal with domain shift, such as a discrepancy loss, adversarial loss, or self-supervision loss. The single-source DUDA methods are divided into four categories based on domain shift loss and generative/discriminative settings, as shown in Figure 2.
There have been several surveys on domain adaptation and transfer learning. In particular: [9] covers different transfer learning paradigms, such as self-taught learning and multi-task learning, but the domain adaptation part is not comprehensive; [26] deals for the most part with early methods with little discussion devoted to recent deep learning-based methods; [27] covers almost all categories of domain adaptation methods briefly but comparisons are scant; [37, 38] focus on shallow methods and only a few deep methods are reviewed; [39, 40] analyze multi-source domain adaptation respectively focusing on shallow and deep methods. Similar to [41–43], we focus on deep domain adaptation, but use a different taxonomy that provides different insights. There are also some blogs summarizing recent papers on different transfer learning methods5 and domain adaptation6 strategies. Compared to existing surveys and blogs, our survey has the following advantages/contributions: (1) we cover and compare the latest methods on deep unsupervised domain adaptation; (2) we provide an analysis that includes advantages/disadvantages of different categories and differences/connections among different methods in each category with summarizing tables; (3) we give suggestions and prospects for future directions to explore; (4) we systematically compare the results of existing methods on popular benchmark datasets; and (5) we discuss some important aspects that are overlooked in previous surveys, such as label shift and theory.

## d) Organization of This Survey

In this survey, we review recent progress on visual domain adaptation, comparing advantages and disadvantages of different approaches in this class, and discussing future directions.
In particular: First, we define different DA strategies in Section II. Second, we summarize the available datasets for performing DA evaluation focusing on computer vision tasks in Section III. And then, we briefly introduce the theoretical view in Section IV-A, summarize and compare the representative approaches on different single-source DUDA categories, including discrepancy-based methods (Section IV-B), adversarial discriminative methods (Section IV-C), adversarial generative methods (Section IV-D), self-supervision-based methods (Section IV-E), and combinations and others (Section IV-F), followed by both qualitative and quantitative comparisons of different categories in Section IV-G and Section IV-H. Finally, we discuss potential future research directions in Section V.

# II. DOMAIN ADAPTATION TAXONOMY

We introduce a standard definition of the variables and models to enable effective comparisons and classification. Let x and y 7 respectively denote the input data and output label, drawn from a specific domain probability distribution P(x, y).
In typical domain adaptation, there is one source domain and one target domain. Suppose the source data and corresponding labels drawn from the source distribution PS(x, y) are XS and YS respectively, and the target data and corresponding labels drawn from the target distribution PT (x, y) are XT and YT respectively. The corresponding marginal distributions include PS(x), PS(y), PT (x), PT (y), and conditional distributions include PS(x|y), PS(y|x), PT (x|y), PT (y|x). Three typical sources of variation between the two domains considered in the literature include: 1) covariate shift, where PS(y | x) = PT (y | x) for all x, but PS(x) 6= PT (x); 2) label shift, where PS(x | y) = PT (x | y) for all y, but PS(y) 6= PT (y); 3) concept drift, where PS(x, y) 6= PT (x, y).
In addition, the source dataset is DS = {XS, YS} = {(x i S , yi S )} NS i=1, the target dataset is DT = {XT , YT } = {(x j T , y j T )} NT j=1, where NS and NT are the number of source samples and target samples respectively, x i S ∈ RdS and x j T ∈ RdT are referred as observations in the source domain and the target domain, and y i S and y j T are the corresponding class labels.
Suppose the number of labeled target samples is NT L; then, the DA problem can be classified into different categories: • unsupervised DA, when NT L = 0; • fully supervised DA, when NT L = NT ; • semi-supervised DA, otherwise.
Suppose the number of labeled source samples is NSL; then, DA can be classified into: • strongly supervised DA, when NSL = NS; • weakly supervised DA, otherwise.
Based on the representations, dS and dT , of the source and target domains (e.g. images vs. text), we can classify DA into: 7 In this paper we assume x is an image and y could be any label type (e.g. object classes, bounding boxes, semantic segmentation, etc).

when no prior knowledge of the label sets is available; where ∩ and ⊂ indicate intersection and proper subset.
Although without labels, the target data is usually available during training in typical DA. If the target data is also unavailable, we often denote this task as domain generalization or zero-shot DA. Therefore, we have: • domain adaptation, when XT is available during training; • domain generalization or zero-shot DA, when XT is unavailable during training.
The classification of different DA categories are summarized in Table I. We focus on the review of recent unsupervised domain adaptation (UDA) methods under homogeneous, single-source, single-target, strongly-supervised, and closedset settings, i.e. NT L = 0, dS = dT , NS = 1, NT = 1, NSL = NS, CS = CT . The goal is to learn a model f that can correctly predict a sample from the target domain based on labeled {XS, YS} and unlabeled {XT }.

# III. DATASETS

In the early years, the datasets for DA were mainly collected from real world scenarios. Increasingly, large-scale synthetic datasets are being generated from simulation engines with labels automatically obtained, which induces large domain shift from the real world data. The released datasets are summarized in Table II.

## Semantic Segmentation

Semantic segmentation. Cityscapes [3] contains vehicle- centric urban street images collected from a moving vehicle in 50 cities from Germany and neighboring countries. There are 5,000 images with pixel-wise annotations, including a training set with 2,975 images, a validation set with 500 images, and a test set with 1,595 images. It is widely used in segmentation. BDDS [167] contains thousands of real-world dashcam video frames with accurate pixel-wise annotations. It has a label space that is compatible with Cityscapes. GTA [25] is collected in the high-fidelity rendered computer game GTA-V with pixel-wise semantic labels. It contains 24,966 images (video frames). There are 19 classes that are compatible with Cityscapes. SYNTHIA [168] is a large synthetic dataset. To pair with Cityscapes, a subset, named SYNTHIA-RANDCITYSCAPES, was designed with 9,400 images which are automatically annotated with 16 compatible classes, one void class, and some unnamed classes.

# IV. SINGLE-SOURCE DUDA

In this section, we first introduce a theoretical view for domain adaptation. Second, we summarize different categories UNDER REVIEW 6 of single-source DUDA. Finally, we compare the advantages and disadvantages of these methods.

## A. Theory Brief

Many methods in the domain of machine learning are based on empirical evidence rather than well-founded theory. The ones that have solid theory background use statistics profusely.
Domain adaptation is no exception. However, upper bounds on the generalization target error by learning from the source data have been obtained. In a seminal paper, (author?) [173] provided a bound for domain adaptation on the target risk that generalizes the standard bound on the source risk. Informally, the theory says that if there exists a common hypothesis (classifier) that generalizes well on both the source and the target domains, the performance difference of any classifier on these two domains could be bounded by the distance between the data distributions of the two domains. The authors proposed H-divergence, a parametric pseudo-metric to measure the distance between two domains. Using this this pseudometric, two domains are considered close if there exists a binary classifier (a discriminator) that, upon seeing data from the two domains, can distinguish which domain the data comes from. This work formalizes the intuitive notion that reducing the two distributions while ensuring a low error on the source domain, yields accurate results in the target domain. Further, the theory justifies the basis of many recent DA algorithms that learn domain-invariant representations, using either domain adversarial classifier or discrepancy-based approaches.
(author?) [174] introduced a new divergence measure: the discrepancy distance, which was used to provide a generalization guarantee for the target domain. Compared with the H-divergence that can only be used in the setting of binary classification, the discrepancy distance could be used to achieve a generalization bound for target domain in regression setting as well. In a later work, (author?) [175] derived generalization bounds on the target error by making use of the robustness properties introduced in [176]. Extensions of the above theory to multi-source domain adaptation for both classification and regression problems also exist [146, 177].

## B. Discrepancy-based Methods (Table III)
Discrepancy-based methods explicitly measure the discrepancy between the source and target domains on corresponding activation layers of the two network streams. Long et al. [46] designed a Deep Adaptation Network, where the discrepancy is defined as the sum of the multiple kernel variant of maximum mean discrepancies (MK-MMD) between the fully connected (FC) layers. Sun et al. [47] proposed correlation alignment (CORAL) to minimize domain shift by aligning the second-order statistics of the source and target features of the last FL layer. Apart from the CORAL loss on the last FL layer, Zhuo et al. [36] also incorporated the CORAL loss on the last convolutional (conv) layer. To deal with the high dimension of convolutional layer activations, activationbased attention mapping is employed to distill it into low dimensional representations. The CORAL losses on both the last convolutional layer and the last FC layer are combined.

Wu et al. [44] studied the UDA problem for 3D LiDAR point cloud segmentation from synthetic data to real world data.
At every batch of training, in addition to the focal loss to learn semantics from the point cloud on the synthetic batch, they employed the geodesic distance to penalize discrepancies between batch statistics from two domains. In recent papers, Zellinger et al. [49] proposed to match the higher order central moments of probability distributions by means of order-wise moment differences. They utilized the equivalent representation of probability distributions by moment sequences to define a new distance function, called Central Moment Discrepancy (CMD). Chen et al. [50] explored the benefits of using higherorder statistics (in this case mainly third-order and fourth-order statistics) for domain matching. They proposed a Higher-order Moment Matching (HoMM) method, and further extended the HoMM into reproducing kernel Hilbert spaces (RKHS).
Some other types of divergence are also designed to align the source and target domains. Lee et al. [55] designed sliced Wasserstein discrepancy (SWD) to capture the natural notion of dissimilarity between the outputs of task-specific classifiers.
It provides a geometrically meaningful guidance to detect target samples that are far from the support of the source and enables efficient distribution alignment in an end-to-end trainable fashion. Roy et al. [56] proposed domain alignment layers which implement feature whitening for the purpose of matching source and target feature distributions. Additionally, they leveraged the unlabeled target data by proposing the Min-Entropy Consensus loss, which regularizes training while avoiding the adoption of many user-defined hyper-parameters.

Instead of explicitly modeling the discrepancy between the source and the target domains, some papers implicitly minimize domain discrepancy by aligning the Batch Normalization (BN) statistics. Li et al. [53] proposed to adopt domain specific normalization for different domains. The proposed Adaptive BN (AdaBN) replaces the moving average mean and variance of all BN layers in the task network trained on the source domain with the mean and variance estimated from the target mini-batches. AdaBN [53] and other DUDA methods define a prior on which layers should be adapted. Instead, Cariucci et al. [54] proposed to learn automatically which layers of the network should be aligned and the corresponding alignment degree. The Auto-DomaIn Alignment Layer (AutoDIAL) is embedded multiple times to align the learned feature representations of the source and target domains at different levels.
These BN-based methods have fewer parameters to tune, higher computational efficiency, and competitive performance.

The methods described above measure the domain discrepancy at the domain level, which neglects the information concerning the classes from which the samples are drawn and thus may lead to misalignment and poor performance.
Kang et al. [51] proposed Contrastive Adaptation Network, which optimizes a new metric, Contrastive Domain Discrepancy (CDD), by explicitly minimizing the intra-class discrepancy and maximizing the inter-class domain discrepancy. The source and target samples of the same underlying class are drawn closer, while the samples from different classes are pushed apart. Pan et al. [69] recently proposed Transferrable Prototypical Networks, which perform domain alignment such that prototypes for each class in the source and target domains are close in the embedding space and the predictions from prototypes separately on source and target data are similar.
Most of the papers mentioned above consider aligning the marginal distributions in the feature space. When confronted with complex tasks, these approaches would fail when the label distributions are drastically different between source and target domains. The joint alignment of distributions DS = (XS, YS) and DT = (XT , YT ) is considered in [52] under the assumptions that PS(x) 6= PT (x) and PS(y|x) 6= PT (y|x).
The joint distributions across domains is projected to a Reproducing Kernel Hilbert Space (RKHS) H and MMD is used as the distance metric. During the joint distribution alignment, the distribution shift PS(x) and PT (x), PS(y|x) and PT (y|x) are significantly reduced.
The above methods all adopt weight-sharing between the two streams of the Siamese architecture [178, 179] that attempts to reduce the impact of domain shift by learning domain-invariant features. However, domain invariance may be detrimental to discriminative power. On the contrary, Rozantsev et al. [48] proposed to explicitly model the domain shift and relaxed the weight-sharing constraint to a linear correlation. They jointly optimized a weight regularizer, representing the loss between corresponding layers of the two streams, and an unsupervised regularizer, encoding the MMD measure and favoring similar distributions of the source and target representations.

## C. Adversarial Discriminative Models (Table IV)

Adversarial discriminative models usually employ an adversarial objective with respect to a domain discriminator to encourage domain confusion (see Table IV). In the early-stage of adversarial discriminative models, the domain adversarial training of neural networks is proposed to learn domain invariant and task discriminative representations [58]. It is directly derived from the seminal theoretical works of Ben et al. [173] and directly optimizes the H-divergence between source and target. By deriving the generalization bound on the target risk and obtaining an empirical formulation of the objective, Ganin et al. [58] proposed the Domain-Adversarial Neural Networks (DANN) algorithm. From this point of view, the adversarial discriminative models are originally similar to the discrepancy-based models. Recently, a couple of adversarial discriminative models were proposed with different algorithms and network architectures, thus differing from the discrepancybased methods.

Suppose mS and mT are the representation mappings of the source and target domains, respectively, and d is a domain discriminator, which classifies whether a data point is drawn from the source or the target domain. The adversarial discriminator is trained typically based on an adversarial loss Lad . The loss Lam used to train representation mapping is different in existing methods. The Domain-Adversarial Neural Network [58] optimizes the mapping to minimize the discriminator loss directly Lam = −Lad , which might be problematic, since the UNDER REVIEW 8 discriminator converges quickly during training, causing the gradients to vanish. A gradient reversal layer was proposed to achieve domain adversarial training with a single feed-forward network with standard backpropagation and stochastic gradient descent. Tzeng et al. [11] proposed Adversarial Discriminative Domain Adaptation (ADDA), using an inverted label GAN loss to split the optimization process into two independent objectives for the generator and discriminator.
Besides aligning marginal distributions, several methods also align conditional or joint distributions. Long et al. [62] considered aligning conditional distribution across domains, and proposed Conditional Domain Adversarial Network (CDAN). Based on DANN [58], they used conditional discriminator D(f ×g) with improved discriminability, where f is feature extractor and g is classifier, to capture the cross-covariance between feature representations and classifier predictions. To extend joint distribution alignment, Du et al. [67] used dual adversarial strategy to train a dual-discriminator to pit against each other. Cicek et al. [63] also aimed for joint distribution P(d, y) alignment over domain d and label y by a joint predictor and aligned its output with classifier’s prediction.After analyzing the drawbacks of feature-level alignment methods, Liu et al. [65] proposed Transferable Adversarial Training (TAT), not only adapting feature representations from difference domains, but also generating transferable examples to make the classifier learn a more robust decision boundary.

Xu et al. [66] explored two common limitations in current adversarial-based methods. Sampling from source and target domains separately is insufficient to ensure domain-invariance at the whole latent space, and does not give the discriminator a hard label to judge real and fake samples. They proposed a mixed version of the discriminator to guarantee domaininvariance in a more continuous latent space, thus improving the robustness of models performance. Chen et al. [68] adopted the concept of self-training. They analyzed the noise of pseudo-labels in the confusion matrix and proposed correspondingly an adversarial-learned loss to accurately estimate the confusion matrix. In this way, their proposed method inherits the strength of both adversarial learning and selftraining paradigm.

Hoffman et al. [57] made the very first effort for domain adaptation in semantic segmentation. They employed a pixellevel adversarial loss to enforce the network to extract domaininvariant features for semantic segmentation and further applied category-specific constraints, e.g. pixel percentage histograms. Instead of only performing domain adversarial globally, Chen et al. [59] proposed to perform feature alignment jointly at the global and class-wise levels by leveraging soft labels from source and target-domain data. Hong et al. [60] proposed to learn a conditional generator to transform features of synthetic images to real-image like features, and perform domain adversarial training on the learned features. However, the proposed method is network-specific and only applied to the FCN model structure. While previous works mostly perform feature alignment in the middle of a network, Tsai et al. [61] adopted adversarial learning in the output space. To further enhance the adapted model, they constructed a multilevel adversarial network to effectively perform output space domain adaptation at different feature levels. To address DA in object detection, [126, 129] applied multi-level domain alignment with adversarial training, and Chen et al. [45] performed domain alignment on both image level and instance level. Weak alignment model was introduce in [127] which focused the adversarial alignment loss on images that are globally similar, putting less emphasis on aligning images which are globally dissimilar. Zhu et al. [125] instead proposed to perform adversarial learning on region level for domain alignment. Recently, Zhent et al. [131] proposed a coaraseto-fine feature adaptation approach for object detection. Different from image level or instance level feature alignment, foreground regions are extracted by attention mechanism, and aligned through multi-layer adversarial learning. Based on prototypical representations, Hu et al. [64] recently proposed a Prototypical Adversarial Learning scheme to align both feature representations and intermediate prototypes across domains.

## D. Adversarial Generative Models (Table V)

Adversarial generative models combine the domain discriminative model with a generative component generally based on generative adversarial nets (GANs) [181], which include a generator g and a discriminator d. g takes random noise z as input to generate a virtual image, while d takes the output of g and real images x as input to classify whether an image is real or generated. The learning process is that d tries to maximize the probability of correctly classifying real and generated images, while g tries to generate images to maximize the probability of d making a mistake. The Coupled Generative Adversarial Networks (CoGAN) [180] is composed of a tuple of GANs, and each is responsible for synthesizing images in one domain. CoGAN corresponds to a constrained min-max game of two teams, each with two players.

Instead of taking random noise as input, the generator of more recent GAN based methods is usually conditioned on the source data. Shrivastava et al. [23] proposed simulated and unsupervised learning (SimGAN) to improve the realism of a simulator’s output using unlabeled real data. The discriminator’s loss in SimGAN is the same as is used in a traditional GAN, while a self-regularization loss is added in the refiner (generator) loss to ensure that the refined data do not change much, which aims to preserve the annotation information. The generator in pixel-level DA [70] is conditioned on both a noise vector and an image from the source domain. To penalize large low-level differences between the source and generated images for foreground pixels only, the model learns to minimize a masked Pairwise Mean Squared Error (PMSE) which only calculates the masked pixels (foreground) of the source and the generated images. Sankaranarayanan et al. [73] proposed to learn a mutual feature embedding for source and target images, and to generate intermediate domain images from source and target embeddings. They also designed a multiclass discriminator to encourage the model to extract more class-discriminative features.

To overcome the under-constrained nature of GAN, [182] proposed CycleGAN with a cycle-consistency constraint. Based on the CycleGAN loss, some effective adaptation methods were introduced. Hoffman et al. [71] proposed discriminatively-trained Cycle-Consistent Adversarial Domain Adaptation (CyCADA), which adapts representations at both the pixel-level and feature-level, enforces cycle-consistency, and leverages a task loss to perform semantic segmentation adaptation. Similarly, Russo et al. [78] introduced bidirectional image translation mapping and proposed classconsistency loss. While CycleGAN [182] can only translate low-level appearance, e.g. texture, [74] realized multiple view-point transformation combining with key-point detection network. Similarly, Tzeng et al. [87] performed domain adaptation on object detection using pixel-level alignment and feature-level alignment. Extending previous CycleGANbased works [71, 87, 182], Li et al. [75] proposed cycleconsistent conditional adversarial transfer networks (3CATN) to improve adversarial training and feature generation process by conditioning on the classifier prediction. Instead of using a discriminator, Wu et al. [76] explored channel-wise statistics alignment of CNN features to guide the generation process.
Liu et al. [77] combined CoGAN [180] with Variational Autoencoder (VAE) [183] to perform unsupervised image-toimage translation. A shared latent space between source and target domains is inferred to align the joint distributions of different domains. And then training data closer to the target domain can be sampled from the shared latent space. Besides the CycleGAN loss, Kang et al. [72] proposed to impose the attention alignment penalty to reduce the discrepancy of attention maps across domains. To make the attention mechanism invariant to domain shift, the target network is trained with a mixture of real and synthetic data from both source and target domains. Hsu et al. [130] leveraged CycleGAN together with feature-level alignment for object detection adaptation. Kim et al. [128] further proposed to generate diversified intermediate domains to help domain-invariant representation learning for object detection. A multi-domain discriminator is leveraged to encourage the feature to be indistinguishable among the domains.

## E. Self-supervision-based Methods (Table VI)

Self-supervision based methods incorporate auxiliary selfsupervised learning task(s) into the original task network.
Training the self-supervision task jointly with the original task network is helpful to bring the source and target domains closer. Ghifary et al. [184] designed a three-layer Multi-task Autoencoder (MTAE) architecture to transform the original image into analogs in multiple related domains. The hiddeninput and hidden-output weights represent shared and domainspecific parameters, respectively. The learned features are then used as input to a classifier. The category-level correspondence across domains is required. Self-domain and between-domain reconstruction tasks are introduced as the self-supervision task and are performed during training. Deep reconstruction classification network (DRCN) [79] combines a convolutional supervised network for source label prediction with a deconvolutional unsupervised network for target data reconstruction. The feature mapping parameters of the two streams are shared, while the labeling parameters of the supervised network and the decoding parameters of the unsupervised network are learned individually.
MTAE requires that the number of samples of corresponding categories in the two domains should be the same. After the sample selection procedure, some important information may be missing. Further, the output of the algorithm is learned features, based on which a classifier (multi-class Support Vector Machine with a linear kernel in this paper) needs to be trained. DRCN employs an end-to-end strategy, without the requirement of aligned pairs. The above two methods use the same encoder to extract domain-invariant features, ignoring the individual characteristics of each domain. Bousmalis et al. [80] explicitly learned to extract image representations that are partitioned into two subspaces. One component is private to each domain, which aims to capture domain-specific properties, such as background. The other is shared across domains with the goal of capturing shared representations by using autoencoders and explicit loss functions, i.e. scaleinvariant mean-square error (SIMSE).

Except for the reconstruction task, more recent selfsupervision tasks (e.g. image rotation prediction and jigsaw prediction) have been used for DA [81–83]. Xu et al. [83] suggested using self-supervision pretext tasks (e.g. image rotation, patch location prediction) over a feature extractor. Feng et al. [84] proposed to use self-supervision pretext tasks as part of their framework for domain generalization. Carlucci et al.
[82] proposed to solve domain adaptation/generalization by introducing a jigsaw puzzle as a self-supervision task. Images are decomposed into 9 patches which are then randomly shuffled and used to form images of the same dimension of the original ones. The Maximal Hamming distance algorithm is used to define a set of patch permutations and assign an index to each of them. The convolutional network is optimized to satisfy two objectives: object recognition on the ordered images and jigsaw classification, namely the permutation index recognition, on the shuffled images. Sun et al. [81] further proposed to perform domain adaptation by jointly learning multiple self-supervision tasks. Source and target images share the same convolutional feature encoder, and the extracted features are then fed into different self-supervision task heads: image rotation prediction, patch location prediction, and flip prediction. Since images from different domains normally have many low-level visual differences, e.g. brightness, texture, etc., self-supervision tasks aiming to predict pixel values of the original images are usually not quite helpful. Because of this, self-supervision tasks that predict high-level structural labels are more favorable for domain adaptation. Kim et al. [85] proposed a cross-domain self-supervised learning approach for DA. It captures apparent visual similarities with both in-domain and across-domain self-supervision. Consequently, they could perform DA with only few source labels. Selfsupervised learning has also been introduced into point-cloud adaptation [86], in which region reconstruction is introduced as a new pretext task.

## F. Combinations and Others

Some techniques combine several of the above-mentioned methods to jointly explore their advantages. Zhang et al. [88] performed adaptation in both the visual appearance-level and representation-level. Leveraging the unpaired image-to-image translation framework [182], the method proposed by Murez et al. [89] requires that the extracted features are able to reconstruct the images in both domains. In addition, they also aligned the extracted features in both domains.
Finding invariant representations alone is clearly not a sufficient condition for the success of domain adaptation. Zhao et al. [90] gave a simple counterexample where invariant representations lead to large joint error on source and target domains. So far, most methods focus on covariate shift which occurs on standard datasets but fails in most practical applications. For instance, when transferring knowledge from synthetic to real images [160], the supports of the input distributions are actually disjoint. Similar to covariate shift, label shift is also a long-standing problem in machine learning, but only a few works in domain adaptation have focused on solving it until recently. In this line of work, [90–94] proposed generalization bounds for this scenario and focused on detection and alignment by estimating the density ratio P(ys)/P(yt) and doing importance re-sampling on Y space.
Recently, pseudo-labeling has been exploited in a number of DA methods. Pan et al. [69] and Hu et al. [64] assigned pseudo-labels to images in the target domain and then performed domain alignment based on prototypes. These methods are mostly used for image classification. Zou et al.
[95] proposed to utilize pseudo-labeling in self-training for semantic segmentation, in which pseudo-labels are generated from high-confidence predictions. However, since pseudolabels are noisy, overconfident label belief can be paced on wrong classes, leading to propagated errors. In order to solve this issue, Zou et al. [96] proposed a confidence regularized self-training framework, in which pseudo-labels are treated as continuous latent variables jointly optimized via alternating optimization. Label regularization and model regularization are proposed as two types of confidence regularizations.
Ensemble methods have also been used for DA. [97, 98] originally developed ensemble methods for semi-supervised learning. Laine et al. [97] performed ensembling by averaging over past predictions for each example, while Tarvainen et al.
[98] performed ensemble by leveraging past network weights.
These ensemble approaches require high randomness in either inputs or network models, which can be provided by data augmentation, varying augmentation parameters, and utilizing dropout. French et al. [99] extended these methods for unsupervised domain adaptation. Images are first processed with stochastic data augmentation and then fed into both networks.
The student network is trained with gradient descent while the teacher network updates its weights using an exponential moving average of the student network’s weights. Stochastic weight averaging further improves the adaptation results as shown in [100]. Recently, Cai et al. [123] proposed Mean Teacher with Object Relations (MTOR) for object detection which integrates object relations into the consistency cost between teacher and student modules.
Other techniques are quite different from what we have mentioned above. Zhang et al. [101] proposed a curriculumstyle learning approach to minimize the domain gap in semantic segmentation. This method solves easy tasks first in order to infer some necessary properties about the target domain and then the network predictions in the target domain are enforced to follow those inferred properties during the training process.

Alignment approach which learns a domain-invariant classifier in Grassmann manifold with structural risk minimization, while performing dynamic distribution alignment to quantitatively account for the relative importance of marginal and conditional distributions. Saito et al. [103] introduced a new approach that attempts to align the distributions of the source and target domains by utilizing the task-specific decision boundaries. They proposed to maximize the discrepancy between two classifiers’ outputs to detect target samples that are far from the support of the source. Khodabandeh et al.
[124] recently proposed to address DA from the perspective of robust learning and showed that the problem may be ormulated as training with noisy labels. Chen et al. [104] proposed a target guided distillation approach to learn the real image style, which is achieved by training the segmentation model to imitate a pre-trained real style model using real images.
They further took advantage of the intrinsic spatial structure presented in urban scene images, and proposed a spatial-aware adaptation scheme to effectively align the distribution of two domains.

## G. Qualitative Comparison (Table VII)

To thoroughly review the various single-source DUDA methods, we use the following qualitative criteria
1) Theory guarantee: if the upper bound can be minimized by the algorithm
2) Efficiency: the computation cost of the training and inference of the algorithm.
3) Task scalability: if the algorithm is applicable to complex tasks, such as semantic segmentation and object detection.
4) Data scalability: if the algorithm is applicable to large and complex datasets with rather diversified images.
5) Data dependency: if the algorithm can be well trained with small datasets.
6) Optimizability: if the algorithm is easy to train and requires less hyper-parameter tuning.
7) Performance: how well the algorithm performs.

Discrepancy-based methods usually define a distance measurement between the source and target distributions. Based on this definition, an upper bound of the target risk can be derived and domain adaptation algorithms can be designed to minimize this upper bound. Compared with other DUDA categories, many of the existing discrepancy-based methods have better theoretical guarantees. Since most discrepancy-based methods do not add significantly large blocks onto the backbone network, the whole network architectures are usually not very complicated. On the one hand, the computation efficiency of the discrepancy-based methods is usually higher than other categories and the training of the network does not highly rely on large datasets. On the other hand, these methods are not as applicable to large and complex datasets with more diversified images as other categories. In terms of optimizability, since the networks are not very complicated, they are easier to train and require less hyperparameter tuning. Most of the discrepancybased methods learn image-level representations, instead of pixel-level ones, thus they are not as applicable to complex tasks, such as semantic segmentation, as other categories. It is difficult for most discrepancy-based methods to achieve satisfying performance on complex datasets and tasks.

Adversarial discriminative approaches are the most widely used methods to solve DA problems and achieve remarkable results. Several theoretical studies on these methods focus on the investigation of generalization bound and risk analysis.
These methods have competitive computational efficiency and task scalability. In terms of data scalability, they work well across different kinds of datasets. Due to the reliance on the convergence of a min-max game between the discriminator and the feature extractor, they do not always work well on small datasets and are also relatively difficult to optimize.
There is usually no good theoretical support behind adversarial generative approaches since they mainly leverage GAN or other kinds of generative models to reduce the visual gap between source and target domains. However, they usually perform well on many complex tasks with high dimensional solution space, such as semantic segmentation and object detection. It is also because of their reliance on the generative models that they usually require the source and target domains to have homogeneous visual patterns and cannot easily scale to more complex datasets. Since they rely on generative models to build pattern transformation between source and target domains, they require large-scale datasets to robustly train the generative model. Correspondingly, these approaches also require more computing resources and a more complicated optimization process.
Despite the apparent difference, both discrepancy-based methods and adversarial methods can be understood as approaches that attempt to align the marginal feature distributions of both domains. While both methods are intuitive and have seen empirical success in several cases, fundamental limitation exists for both lines of work.
In a recent paper [90], the authors proved an informationtheoretic lower bound on the joint error of methods based on learning domain-invariant representations, showing that when the label distributions of the two domains differ, any algorithm has to achieve a large error on at least one of the two domains. Since only source error could be minimized due to the availability of labeled samples, this implies an increasing error on the target domain. Furthermore, the better the distribution alignment, the worse the joint error. In a concurrent work, (author?) [185] also identified the insufficiency of learning domain-invariant representation for successful adaptation. They further analyzed the information loss of non-invertible transformations, and proposed a generalization upper bound that directly takes it into account.
While most of the work we discussed so far focuses on learning domain-invariant representations, methods based on estimating the importance ratio of density functions between target and source domains are also abundant in the literature [28, 186–189]. Most of these approaches exhibit provable generalization guarantees under the covariate shift assumption.

An interesting avenue for future research is combining the distribution alignment method using deep networks for feature learning with importance ratio reweighting. Note that, different from traditional methods where the importance ratio is estimated between the data density functions, recent work has explored the alternative direction where the importance ratio between the marginal label distributions of the two domains is estimated instead [92, 94]. The fundamental limitation of domain-invariant representations is the potential discrepancy between the marginal label distributions. To overcome such lower bound, one could use the importance ratio between label distributions to compensate for such label discrepancy, as explored in several recent work [91, 190].
Compared with other methods, self-supervision-based methods do not have a strong theoretical guarantee since these methods are mostly based on the intuition that by forcing the CNN encoder to perform the desired task on the source domain and the pretext tasks on the target domain, the CNN encoder could extract domain-invariant features for both. In terms of computation cost, self-supervision-based methods perform the self-supervision tasks with additional heads, which are normally light-weight CNNs. They normally have more computation cost than discrepancy-based methods, while having less computation cost than adversarial generative methods.
Self-supervision-based methods do not have assumptions on the downstream task, and are applicable to complex tasks.
In terms of data scalability, self-supervision-based methods are robust and applicable to complex datasets. The selfsupervision tasks are normally simple tasks which are easy to train along with the downstream task network. Finally, selfsupervision-based methods usually have better performance than discrepancy-based methods, but are less performant than adversarial discriminative and generative methods.

## H. Quantitative Comparison (Table VIII to Table XV)

In this section, we quantitatively compare different categories of single-source DUDA methods in three visual tasks, i.e. image classification, object detection, and semantic segmentation. First, we introduce detailed experimental settings, including datasets with their properties, and evaluation metrics.

Second, we analyze the results.

### 1) Image Classification

We compare the classification performance of different methods on 4 different datasets, Digit, Office-31, Office-Home, and VisDA-2017. The first three datasets contain several domains of images. A DA method is evaluated by performing adaptation from each domain to every other domain in the dataset, and averaging all adaptation performances. Classification accuracy is used as the evaluation metric.
Digit and Office-31 are relatively basic datasets for DA.
Because images in these datasets are mostly centered objects with simple backgrounds, many methods could achieve high adaptation accuracy, making it hard to compare them.
However, these datasets are still widely used since they are convenient for testing new ideas. Office-Home contains more domains (4) and the 12 source-to-target adaptation settings provide more diverse tests to mitigate the possibility of overfitting. VisDA-2017 is a challenging large-scale dataset with one simulation domain and one real-world domain.

### 2) Object Detection

We compare the detection performance of different methods on Cityscapes→KITTI and Cityscapes→Foggy Cityscapes. Each dataset contains bounding boxes of different categories. We use mean Average Precision (mAP) as the evaluation metric.
Cityscapes and KITTI are both real-world datasets, but collected from different cities. The scene layouts of the images in the two domains are different, which can test the ability to bridge the domain gap caused by both appearance and scene layout differences. Cityscapes and KITTI only have 5 shared categories in the adaptation setting. Foggy Cityscapes is a synthetic dataset simulating fog on Cityscapes images.
Cityscapes and Foggy Cityscapes have 8 classes of objects; since they have the same scene layouts, this adaptation task focuses on testing the appearance adaptation ability of a DA method.

### 3) Semantic Segmentation

We compare the segmentation performance of different methods on GTA→Cityscapes and SYNTHIA→Cityscapes. Mean intersection-over-union (mIoU) is utilized as the evaluation metric.
GTA and SYNTHIA are both synthetic datasets, while Cityscapes is a real-world dataset. Both GTA→Cityscapes and SYNTHIA→Cityscapes test the performance of simulation-toreal segmentation adaptation methods. Images in GTA and Cityscapes are taken from the dashcams, while images in SYNTHIA are taken from various points of view. Images in GTA have higher level of fidelity compared to images in SYNTHIA. Consequently, SYNTHIA has a larger domain gap than Cityscapes, and it can also test the adaptation method on the domain gap caused by different point of view angles.

### 4) Result Analysis

All the experiment result comparisons are shown in Table VIII, IX, X, XI (image classification); Table XII, XIII (object detection); and Table XIV, XV (semantic segmentation). For each backbone, the methods are sorted by average classification accuracy, mAP and mIoU.

The results show that, compared with object detection and semantic segmentation, it is easier for the methods under analysis to achieve better performance on the image classification task. Since classification is a relatively simple task, not requiring many local details for the global class prediction, no specific category performs significantly better than the others. For object detection and semantic segmentation, most of the published work utilize adversarial discriminative or adversarial generative methods since these two tasks require massive detailed local information about the images. Adversarial learning-based methods are powerful in performing local feature alignment while discrepancy-based and selfsupervision-based methods are less capable of capturing local information, leading to inferior performance on object detection and semantic segmentation tasks.

# V. FUTURE DIRECTIONS
Existing DUDA methods have achieved promising performance on many computer vision tasks, such as object classification and semantic segmentation. However, there is still a large performance gap between existing methods and the upper bound (train and test both on target domain). To help address the remaining challenges, we provide some possible improvements over the state-of-the-art methods. In addition,we present more practical settings of DA, new applications of DA and brave new perspectives in DA.

## A. New Methodologies of DA

### Incorporating Previous Knowledge.
As domain shift is usually caused by the imaging process, such as illumination changes [26], incorporating prior knowledge into the adaptation process may lead to a performance increase. For adversarial methods, imposing multi-level constraints jointly in the adaptation, such as low-level appearances, mid-level features, and high-level semantics, can better preserve the structure and attributes of the source data and thus perform better. Designing an effective and direct metric to evaluate the quality of adaptation, instead of testing the performance on the target domain, would accelerate the training process of GANs.

### Meta Learning Across Domains
Meta learning algorithms provides a learning to learn paradigm that is effective at learning meta models with the capability of solving new tasks in a fast manner. However, they require sufficient tasks for meta model training and the optimized model can only solve new tasks similar to the training ones. These limitations make them suffer from performance decline in presence of insufficiency of training tasks in the target domains and task heterogeneity, where the source tasks present different characteristics from the target tasks [191]. Besides the above challenge, there may be data distribution shift between the source tasks and target tasks, exposing more severe challenges to existing meta learning algorithms. Cross-domain meta learning provides promising solutions to address these challenges by essentially learning more transferable representations [153, 192].

### Contrastive Learning for DA
DUDA methods [193, 194] are recently focusing on the disentanglement [195] of the features into domain-invariant and domain-specific ones based on data variations. Domain-invariant features play an important part in reducing the noisy information from each domain, thus making learned features discriminative of the category.
Current approaches of contrastive learning for domain adaptation are highly dependent on the design of specific tasks.
For example, different DA tasks may rely on different pretext tasks. Therefore, a potential research direction is to design a common pretext task. These methods are often criticized for their computational cost since a large number of negative samples have to be selected for comparison with every single positive sample. Thus, an approach to decrease computational complexity is needed.

## B. More Practical Settings of DA

### Multi-modal DA

The labeled source domains may contain multi-modal data. For example, synthetic data generated by simulators (CARLA and GTA-V, etc.) may be of different modalities, such as LiDAR, RaDAR, and image. Other examples include the audio channel and visual channel of videos and the textual and visual information of social posts. Similar to multi-modal recognition [196, 197] and feature-level fusion in image classification and retrieval [198, 199], we believe that jointly combining and fusing different modalities to explore the combinations would boost the performance of DA. Another advantage of multi-modal DA is that even if some modalities are missing, the DA system can still work by leveraging information from other available modalities. For example, while the cameras for autonomous driving cannot capture images well at night, the LiDAR scanners are robust under almost all lighting conditions [4]. How to design effective fusion strategies is the main challenge. The simplest ways are to directly employ early fusion at the feature level or late fusion at the decision level. But to deal well with the incomplete data issue, fusion at the model-level, such as graph convolutional network [200], is probably a better choice.

### Multi-task DA

To the best of our knowledge, all the domain adaptation methods proposed so far only focus on a single task (e.g. semantic segmentation, robotic grasping, image classification) with single-modal input (e.g. images).
However, in many scenarios, several tasks need to be performed on the same data simultaneously (e.g. semantic segmentation and traffic sign identification for a driving image).
Separately adapting each task would be redundant in terms of computation, since the networks for both models may rely on the same set of features. So how to adapt multiple tasks simultaneously and efficiently is a promising direction to explore. One straightforward solution is to find a common feature representation that is beneficial for all the tasks. In order to guide learning towards an optimal shared feature space, methods based on adversarial learning may be used with novel designs.

### Continual Learning and Adaptation

Many machine learning models (e.g. semantic segmentation models) are trained on a fixed dataset and then deployed onto a real system, with the assumption that the data at test time has a similar distribution as the training data. However, this is often not the case. Imagine a segmentation model trained on images taken in the US with mostly sunny weather conditions. The cars with the trained model are sold all over the world, and different cars will be running under different domains (e.g. different cities, weathers, time of day, etc.). In order for the network to perform well all the time, continual learning and adaptation needs to be performed. Basically, the network is expected to have the ability to learn continually from a steam of experiential data, building on what has been learned previously, and adapting to varying new domains [201]. Some methods [202–204] try to limit the extent of weight sharing across experiences by focusing on preserving past knowledge.
A method is proposed in [205] to adapt to changing environments for semantic segmentation. However, the method requires synthesizing new images on the fly, which is not computationally efficient. Methods such as [206] may be used to find a compact representation of the whole dataset, which may be more efficient to fine-tune the model without forgetting the learned knowledge. Learning representations that are generalizable to different domains could make the network more robust against target domain change. [207, 208] proposed to use style transfer to randomize the input domains for better generalization performance.

### Federated Domain Adaptation

Data generated by IoT devices poses unique challenges for training machine learning models. Users's profile data typically contains sensitive information, thus cannot leave its hosting device for the sake of privacy preservation. Due to the growing storage and computational power of these devices and concerns about data privacy, it is increasingly preferable to store them in a decentralized way on individual devices rather than hosting them in a central storage. Federated Learning (FL) provides a privacy-preserving mechanism to leverage such decentralized data and computation resources to train machine learning models. The main idea behind federated learning is to have each node learn on its own local data and not share either the data or the model parameters. FL improves data privacy and efficiency in machine learning performed over networks of distributed devices, such as mobile phones, IoT and wearable devices, etc. While FL achieves better privacy and efficiency, existing methods ignore the fact that the data on each node are collected in a non-i.i.d manner, leading to domain shift between nodes [193]. Models trained with federated learning can still fail to generalize to new devices due to the problem of domain shift. Thus it is of great importance to develop domain adaptation algorithms for federated learning [209].
Such algorithms should be able to align the representations learned among different source and target devices.

### DA on the Edge

Nowadays, many vision-based perception models are deployed in edge devices, e.g. mobile phones, autonomous cars, and security cameras. These edge devices are usually deployed in different environments, with substantial need for domain adaptation. Different networks need to be personalized via learning on the users’ private data. Sending all the user data to the server, and training millions of networks for all the users would be very expensive. Instead, training networks on the device not only decreases computational complexity, but it also protects privacy since the collected data need not leave the device. While the edge devices normally have a limited budget in terms of computation and power, almost all the current state-of-the-art DA methods, e.g. the adversarial generative methods, require training on high-end GPUs for days. Invertible neural networks [210, 211] are beneficial to mitigate the memory limitation problem. Other methods, such as quantization, pruning, neural architecture search, and software-hardware co-design, can also be used for efficient on-device training. Performing DA on the edge with efficient deep learning techniques is a practical and fruitful research area to explore.

## C. New Applications of DA

### Robotics

Reinforcement learning (RL) algorithms are typically trained in simulation environments. There are two main reasons for this: first, RL algorithms normally require many interactions with the environment, while getting data from the real-world is relatively slow compared to simulation environments that can be sped up; second, training an agent in the real-world would damage the environment as well as the agent itself especially when the policy is not fully well learned. However, if we want to apply the policy learned in simulation into real-world, the domain difference needs to be handled. Methods such as domain randomization [212] have been proposed to mitigate the visual difference of the domains.
Normally, the source and target environments/domains are similar in terms of dynamics of both the environments and agents. An interesting direction is how to transfer if we know the detailed difference of the dynamics.

### Video Analysis

Current methods mainly focus on adapting images from the source domain to the target domain. Adapting videos is more challenging and worth studying. Effectively exploring the temporal correlation of videos may significantly improve the DA performance. Existing video style transfer methods [213–215] may fail to work for DA, since the semantics of generated videos cannot be guaranteed to be preserved.
Imposing some semantic constraint may help to solve this problem. Further, maintaining the temporal consistency [216] is an important factor. Audio is also an important channel in videos, which is not considered in these methods.

### Subjective Attributes

Existing DA methods work on objective tasks, such as object classification and semantic segmentation, while the adaptation for the understanding of subjective attributes, such as personality [217], aesthetics and emotions [218, 219], has been rarely explored. There are many other challenges to adapt these subjective attributes.
Take visual emotion for example: although the transferred images with pixel-level alignment may not change the highlevel semantics, the emotion may still be changed [6, 7].
Employing emotion-specific distance measure, such as Mikels’ emotion wheel [7], may help to tackle this problem. Further, emotions may be evoked by different features, such as lowlevel artistic elements (e.g. color) for abstract paintings and high-level semantics for natural images [199]. First determining the image style and then conducting adaptation with corresponding semantic consistency may perform better.

## D. Brave New Perspectives DA

### in the Wild

So far all the domain adaptation works mainly focus on a neat setting, however, domain adaptation problems in the real world can be a pretty complex combination of different “clean” settings. For example, in a practical domain adaptation setting, there may be several source domains available: some source domains have no labeled examples, some have few labeled samples, and some have abundant labeled samples. At the same time, the label spaces of the source domains and target domains may not be exactly the same. There may also be multiple target domains, with some target domains that have classes not existing in any source domains. Solving practical DA problems in the real-world remains an under-explored field.

### Model Robustness of DA

Due to the wide success of deep neural networks and their unexpected vulnerability of adversarial examples, there has been much attention placed on evaluating and quantifying the robustness of neural networks [220, 221]. However, all the current DA work only focus on boosting the performance on the target domain, without any consideration on the robustness of the adapted model. Investigating how to perform domain adaptation while maintaining the robustness of the model on the target domain is an interesting direction to explore.

### Neural Architecture Search for DA

Existing domain adaptation models usually manually design a specific neural network architecture based the proposed algorithm. However, there is not much work to automatically learn the optimal network architecture to address the domain shift issue. Neural architecture search (NAS) [222] is an emerging direction that automatically looks for the optimal neural network architecture for better performance or higher computational efficiency.
With the success of NAS, we suggest the research on automatically learning optimal network architecture that can be adapted to different domains. For instance, when detecting vehicles from traffic videos, the model can automatically and dynamically learn different network architectures for videos from different weather, e.g., sunny, rainy, cloudy, and snowy or different locations, e.g., London, New York, Rome and Tokyo. With different network architectures, the model can learn better generalized representation to different domains.

### Learning Common Sense for DA

Most of the existing domain adaptation models try to learn a generalized representation between the source and target domains. However, they do not discover the knowledge behind the visual tasks. We argue that human beings have better domain generalization capability because they can learn the “common sense” behind the tasks and infer prediction in different domains. To imitate the humanâA˘ Zs capability in domain generalization, we suggest ´ to investigate learning “common sense” for domain adaptation.
For instance, when the model learns that a computer screen is usually placed on a desk, it can have better performance when detecting the computer screen and the desk, no matter under what illumination, colorization, and camera views. By learning “common sense”, models can be better generalized to different domains.

# VI. CONCLUSION

This paper provides an overview of recent developments in deep unsupervised domain adaptation (DUDA) with the intent of offering a tool for researchers and practitioners to obtain a perspective on the field. Because of the vast literature on the subject, we decided to focus on homogeneous, single-source, single-target, strongly-supervised, and closed-set settings. We classified these methods into different categories, summarized the representative ones, and compared them, supported by experimental results. We believe that DUDA will continue to be an active and promising research area. We also suggested a number of research directions with a discussion of their main challenges and of some possible solutions.

# References

<details>
<summary>References</summary>
<div markdown="1">

[52] M. Long, H. Zhu, J. Wang, and M. I. Jordan, “Deep transfer learning with joint adaptation networks,” in ICML, 2017, pp. 2208–2217.

[53] Y. Li, N. Wang, J. Shi, X. Hou, and J. Liu, “Adaptive batch normalization for practical domain adaptation,” PR, vol. 80, pp. 109–117, 2018.

[54] F. M. Cariucci, L. Porzi, B. Caputo, E. Ricci, and S. R. Bulo, “Autodial: Automatic domain alignment layers,” in ICCV, 2017, pp. 5077–5085.

[55] C.-Y. Lee, T. Batra, M. H. Baig, and D. Ulbricht, “Sliced wasserstein discrepancy for unsupervised domain adaptation,” in CVPR, 2019, pp. 10 285–10 295.

[56] S. Roy, A. Siarohin, E. Sangineto, S. R. Bulo, N. Sebe, and E. Ricci, “Unsuper- vised domain adaptation using feature-whitening and consensus loss,” in CVPR, 2019, pp. 9471–9480.

[57] J. Hoffman, D. Wang, F. Yu, and T. Darrell, “Fcns in the wild: Pixel-level adversarial and constraint-based adaptation,” arXiv:1612.02649, 2016.

[58] Y. Ganin, E. Ustinova, H. Ajakan, P. Germain, H. Larochelle, F. Laviolette, M. Marchand, and V. Lempitsky, “Domain-adversarial training of neural net- works,” JMLR, vol. 17, no. 1, pp. 2096–2030, 2016.

[59] Y.-H. Chen, W.-Y. Chen, Y.-T. Chen, B.-C. Tsai, Y.-C. Frank Wang, and M. Sun, “No more discrimination: Cross city adaptation of road scene segmenters,” in ICCV, 2017, pp. 1992–2001.

[60] W. Hong, Z. Wang, M. Yang, and J. Yuan, “Conditional generative adversarial network for structured domain adaptation,” in CVPR, 2018, pp. 1335–1344.

[61] Y.-H. Tsai, W.-C. Hung, S. Schulter, K. Sohn, M.-H. Yang, and M. Chandraker, “Learning to adapt structured output space for semantic segmentation,” in CVPR, 2018, pp. 7472–7481.

[62] M. Long, Z. Cao, J. Wang, and M. I. Jordan, “Conditional adversarial domain adaptation,” in NeurIPS, 2018, pp. 1640–1650.

[63] S. Cicek and S. Soatto, “Unsupervised domain adaptation via regularized condi- tional alignment,” in ICCV, 2019, pp. 1416–1425.

[64] D. Hu, J. Liang, Q. Hou, H. Yan, Y. Chen, S. Yan, and J. Feng, “Panda: Prototypical unsupervised domain adaptation,” arXiv:2003.13274, 2020.

[65] H. Liu, M. Long, J. Wang, and M. Jordan, “Transferable adversarial training: A general approach to adapting deep classifiers,” in ICML, 2019, pp. 4013–4022.

[66] M. Xu, J. Zhang, B. Ni, T. Li, C. Wang, Q. Tian, and W. Zhang, “Adversarial domain adaptation with domain mixup,” in AAAI, 2019.

[67] Y. Du, Z. Tan, Q. Chen, X. Zhang, Y. Yao, and C. Wang, “Dual adversarial domain adaptation,” arXiv:2001.00153, 2020.

[68] M. Chen, S. Zhao, H. Liu, and D. Cai, “Adversarial-learned loss for domain adaptation,” in AAAI, 2020.

[69] Y. Pan, T. Yao, Y. Li, Y. Wang, C.-W. Ngo, and T. Mei, “Transferrable prototypical networks for unsupervised domain adaptation,” in CVPR, 2019, pp. 2239–2247.

[70] K. Bousmalis, N. Silberman, D. Dohan, D. Erhan, and D. Krishnan, “Unsupervised pixel-level domain adaptation with generative adversarial networks,” in CVPR, 2017, pp. 3722–3731.

[71] J. Hoffman, E. Tzeng, T. Park, J.-Y. Zhu, P. Isola, K. Saenko, A. A. Efros, and T. Darrell, “Cycada: Cycle-consistent adversarial domain adaptation,” in ICML, 2018, pp. 1994–2003.

[72] G. Kang, L. Zheng, Y. Yan, and Y. Yang, “Deep adversarial attention alignment for unsupervised domain adaptation: the benefit of target expectation maximization,” in ECCV, 2018, pp. 401–416.

[73] S. Sankaranarayanan, Y. Balaji, C. D. Castillo, and R. Chellappa, “Generate to adapt: Aligning domains using generative adversarial networks,” in CVPR, 2018, pp. 8503–8512.

[74] L. Tran, K. Sohn, X. Yu, X. Liu, and M. Chandraker, “Gotta adapt’em all: Joint pixel and feature-level domain adaptation for recognition in the wild,” in CVPR, 2019, pp. 2672–2681.

[75] J. Li, E. Chen, Z. Ding, L. Zhu, K. Lu, and Z. Huang, “Cycle-consistent conditional adversarial transfer networks,” in ACM MM, 2019, pp. 747–755.

[76] Z. Wu, X. Han, Y.-L. Lin, M. Gokhan Uzunbas, T. Goldstein, S. Nam Lim, and L. S. Davis, “Dcan: Dual channel-wise alignment networks for unsupervised scene adaptation,” in ECCV, 2018, pp. 518–534.

[77] M.-Y. Liu, T. Breuel, and J. Kautz, “Unsupervised image-to-image translation networks,” in NeurIPS, 2017, pp. 700–708.

[78] P. Russo, F. M. Carlucci, T. Tommasi, and B. Caputo, “From source to target and back: symmetric bi-directional adaptive gan,” in CVPR, 2018, pp. 8099–8108.

[79] M. Ghifary, W. B. Kleijn, M. Zhang, D. Balduzzi, and W. Li, “Deep reconstruction-classification networks for unsupervised domain adaptation,” in ECCV, 2016, pp. 597–613.

[80] K. Bousmalis, G. Trigeorgis, N. Silberman, D. Krishnan, and D. Erhan, “Domain separation networks,” in NeurIPS, 2016, pp. 343–351.

[81] Y. Sun, E. Tzeng, T. Darrell, and A. A. Efros, “Unsupervised domain adaptation through self-supervision,” arXiv:1909.11825, 2019.

[82] F. M. Carlucci, A. D’Innocente, S. Bucci, B. Caputo, and T. Tommasi, “Domain generalization by solving jigsaw puzzles,” in CVPR, 2019, pp. 2229–2238.

[83] J. Xu, L. Xiao, and A. M. López, “Self-supervised domain adaptation for computer vision tasks,” IEEE Access, vol. 7, pp. 156 694–156 706, 2019.

[84] Z. Feng, C. Xu, and D. Tao, “Self-supervised representation learning from multi- domain data,” in ICCV, 2019, pp. 3245–3255.

[85] D. Kim, K. Saito, T.-H. Oh, B. A. Plummer, S. Sclaroff, and K. Saenko, “Cross- domain self-supervised learning for domain adaptation with few source labels,” arXiv:2003.08264, 2020.

[86] I. Achituve, H. Maron, and G. Chechik, “Self-supervised learning for domain adaptation on point-clouds,” arXiv:2003.12641, 2020.

[87] E. Tzeng, K. Burns, K. Saenko, and T. Darrell, “Splat: semantic pixel-level adaptation transforms for detection,” arXiv:1812.00929, 2018.

[88] Y. Zhang, Z. Qiu, T. Yao, D. Liu, and T. Mei, “Fully convolutional adaptation 18 networks for semantic segmentation,” in CVPR, 2018, pp. 6810–6818.

[89] Z. Murez, S. Kolouri, D. Kriegman, R. Ramamoorthi, and K. Kim, “Image to image translation for domain adaptation,” in CVPR, 2018, pp. 4500–4509.

[90] H. Zhao, R. T. Des Combes, K. Zhang, and G. Gordon, “On learning invariant representations for domain adaptation,” in ICML, 2019, pp. 7523–7532.

[91] R. T. d. Combes, H. Zhao, Y.-X. Wang, and G. Gordon, “Domain adaptation with conditional distribution matching and generalized label shift,” arXiv:2003.04475, 2020.

[92] Z. Lipton, Y.-X. Wang, and A. Smola, “Detecting and correcting for label shift with black box predictors,” in ICML, 2018, pp. 3122–3130.

[93] S. Tan, X. Peng, and K. Saenko, “Generalized domain adaptation with covariate and label shift co-alignment,” arXiv:1910.10320, 2019.

[94] K. Azizzadenesheli, A. Liu, F. Yang, and A. Anandkumar, “Regularized learning for domain adaptation under label shifts,” in ICLR, 2019.

[95] Y. Zou, Z. Yu, B. Vijaya Kumar, and J. Wang, “Unsupervised domain adaptation for semantic segmentation via class-balanced self-training,” in ECCV, 2018, pp.
289–305.

[96] Y. Zou, Z. Yu, X. Liu, B. Kumar, and J. Wang, “Confidence regularized self- training,” in ICCV, 2019, pp. 5982–5991.

[97] S. Laine and T. Aila, “Temporal ensembling for semi-supervised learning,” in ICLR, 2017.

[98] A. Tarvainen and H. Valpola, “Mean teachers are better role models: Weight- averaged consistency targets improve semi-supervised deep learning results,” in NeurIPS, 2017, pp. 1195–1204.

[99] G. French, M. Mackiewicz, and M. Fisher, “Self-ensembling for visual domain adaptation,” in ICLR, 2018.

[100] B. Athiwaratkun, M. Finzi, P. Izmailov, and A. G. Wilson, “There are many consistent explanations of unlabeled data: Why you should average,” in ICLR, 2019.

[101] Y. Zhang, P. David, and B. Gong, “Curriculum domain adaptation for semantic segmentation of urban scenes,” in ICCV, 2017, pp. 2020–2030.

[102] J. Wang, W. Feng, Y. Chen, H. Yu, M. Huang, and P. S. Yu, “Visual domain adaptation with manifold embedded distribution alignment,” in ACM MM, 2018, pp. 402–410.

[103] K. Saito, K. Watanabe, Y. Ushiku, and T. Harada, “Maximum classifier discrep- ancy for unsupervised domain adaptation,” in CVPR, 2018, pp. 3723–3732.

[104] Y. Chen, W. Li, and L. Van Gool, “Road: Reality oriented adaptation for semantic segmentation of urban scenes,” in CVPR, 2018, pp. 7892–7901.

[105] Q. Wang, J. Gao, and X. Li, “Weakly supervised adversarial domain adaptation for semantic segmentation in urban scenes,” IEEE TIP, vol. 28, no. 9, pp. 4376–4386, 2019.

[106] Y. Shu, Z. Cao, M. Long, and J. Wang, “Transferable curriculum for weakly- supervised domain adaptation,” in AAAI, vol. 33, 2019, pp. 4951–4958.

[107] C. Wang and S. Mahadevan, “Heterogeneous domain adaptation using manifold alignment,” in IJCAI, 2011, pp. 1541–1546.

[108] W. Li, L. Duan, D. Xu, and I. W. Tsang, “Learning with augmented features for supervised and semi-supervised heterogeneous domain adaptation,” IEEE TPAMI, vol. 36, no. 6, pp. 1134–1148, 2014.

[109] Y.-H. Hubert Tsai, Y.-R. Yeh, and Y.-C. Frank Wang, “Learning cross-domain landmarks for heterogeneous domain adaptation,” in CVPR, 2016, pp. 5081–5090.

[110] J. Li, K. Lu, Z. Huang, L. Zhu, and H. T. Shen, “Heterogeneous domain adaptation through progressive alignment,” IEEE TNNLS, vol. 30, no. 5, pp. 1381–1391, 2018.

[111] H. Li, S. J. Pan, S. Wang, and A. C. Kot, “Heterogeneous domain adaptation via nonlinear matrix factorization,” IEEE TNNLS, 2019.

[112] Y. Yao, Y. Zhang, X. Li, and Y. Ye, “Heterogeneous domain adaptation via soft transfer network,” in ACM MM, 2019, pp. 1578–1586.

[113] H. Yu, M. Hu, and S. Chen, “Multi-target unsupervised domain adaptation without exactly shared categories,” arXiv:1809.00852, 2018.

[114] B. Gholami, P. Sahu, O. Rudovic, K. Bousmalis, and V. Pavlovic, “Unsupervised multi-target domain adaptation: An information theoretic approach,” IEEE TIP, vol. 29, pp. 3993–4002, 2020.

[115] P. Panareda Busto and J. Gall, “Open set domain adaptation,” in ICCV, 2017, pp.
754–763.

[116] K. Saito, S. Yamamoto, Y. Ushiku, and T. Harada, “Open set domain adaptation by backpropagation,” in ECCV, 2018, pp. 153–168.

[117] H. Liu, Z. Cao, M. Long, J. Wang, and Q. Yang, “Separate to adapt: Open set domain adaptation via progressive separation,” in CVPR, 2019, pp. 2927–2936.

[118] Z. Cao, M. Long, J. Wang, and M. I. Jordan, “Partial transfer learning with selective adversarial networks,” in CVPR, 2018, pp. 2724–2732.

[119] J. Zhang, Z. Ding, W. Li, and P. Ogunbona, “Importance weighted adversarial nets for partial domain adaptation,” in CVPR, 2018, pp. 8156–8164.

[120] Z. Cao, L. Ma, M. Long, and J. Wang, “Partial adversarial domain adaptation,” in ECCV, 2018, pp. 135–150.

[121] Z. Cao, K. You, M. Long, J. Wang, and Q. Yang, “Learning to transfer examples for partial domain adaptation,” in CVPR, 2019, pp. 2985–2994.

[122] K. You, M. Long, Z. Cao, J. Wang, and M. I. Jordan, “Universal domain adaptation,” in CVPR, 2019, pp. 2720–2729.

[123] Q. Cai, Y. Pan, C.-W. Ngo, X. Tian, L. Duan, and T. Yao, “Exploring object relation in mean teacher for cross-domain detection,” in CVPR, 2019, pp. 11 457– 11 466.

[124] M. Khodabandeh, A. Vahdat, M. Ranjbar, and W. G. Macready, “A robust learning approach to domain adaptive object detection,” in ICCV, 2019, pp. 480–490.

[125] X. Zhu, J. Pang, C. Yang, J. Shi, and D. Lin, “Adapting object detectors via selective cross-domain alignment,” in CVPR, 2019, pp. 687–696.

[126] Z. He and L. Zhang, “Multi-adversarial faster-rcnn for unrestricted object detection,” in ICCV, 2019, pp. 6668–6677.

[127] K. Saito, Y. Ushiku, T. Harada, and K. Saenko, “Strong-weak distribution alignment for adaptive object detection,” in CVPR, 2019, pp. 6956–6965.

[128] T. Kim, M. Jeong, S. Kim, S. Choi, and C. Kim, “Diversify and match: A domain adaptive representation learning paradigm for object detection,” in CVPR, 2019, pp. 12 456–12 465.

[129] R. Xie, F. Yu, J. Wang, Y. Wang, and L. Zhang, “Multi-level domain adaptive learning for cross-domain detection,” in ICCV Workshops, 2019.

[130] H.-K. Hsu, C.-H. Yao, Y.-H. Tsai, W.-C. Hung, H.-Y. Tseng, M. Singh, and M.-H.
Yang, “Progressive domain adaptation for object detection,” in WACV, 2020, pp.
749–757.

[131] Y. Zheng, D. Huang, S. Liu, and Y. Wang, “Cross-domain object detection through coarse-to-fine feature adaptation,” arXiv:2003.10275, 2020.

[132] E. Tzeng, J. Hoffman, T. Darrell, and K. Saenko, “Simultaneous deep transfer across domains and tasks,” in ICCV, 2015, pp. 4068–4076.

[133] P. L. Ballester, “Semi-supervised learning methods for unsupervised domain adaptation in medical imaging segmentation,” Pontifícia Universidade Católica do Rio Grande do Sul, 2019.

[134] K. Saito, D. Kim, S. Sclaroff, T. Darrell, and K. Saenko, “Semi-supervised domain adaptation via minimax entropy,” in ICCV, 2019, pp. 8050–8058.

[135] L. Duan, I. W. Tsang, D. Xu, and T.-S. Chua, “Domain adaptation from multiple sources via auxiliary classifiers,” in ICML, 2009, pp. 289–296.

[136] Q. Sun, R. Chattopadhyay, S. Panchanathan, and J. Ye, “A two-stage weighting framework for multi-source domain adaptation,” in NeurIPS, 2011, pp. 505–513.

[137] L. Duan, D. Xu, and S.-F. Chang, “Exploiting web images for event recognition in consumer videos: A multiple source domain adaptation approach,” in CVPR, 2012, pp. 1338–1345.

[138] R. Chattopadhyay, Q. Sun, W. Fan, I. Davidson, S. Panchanathan, and J. Ye, “Multisource domain adaptation and its application to early detection of fatigue,” ACM TKDD, vol. 6, no. 4, p. 18, 2012.

[139] L. Duan, D. Xu, and I. W.-H. Tsang, “Domain adaptation from multiple sources: A domain-dependent regularization approach,” IEEE TNNLS, vol. 23, no. 3, pp.
504–518, 2012.

[140] J. Yang, R. Yan, and A. G. Hauptmann, “Cross-domain video concept detection using adaptive svms,” in ACM MM, 2007, pp. 188–197.

[141] G. Schweikert, G. Rätsch, C. Widmer, and B. Schölkopf, “An empirical analysis of domain adaptation algorithms for genomic sequence analysis,” in NeurIPS, 2009, pp. 1433–1440.

[142] Z. Xu and S. Sun, “Multi-source transfer learning with multi-view adaboost,” in ICNIP, 2012, pp. 332–339.

[143] S.-L. Sun and H.-L. Shi, “Bayesian multi-source domain adaptation,” in ICML and Cybernetics, vol. 1, 2013, pp. 24–28.

[144] H. S. Bhatt, A. Rajkumar, and S. Roy, “Multi-source iterative adaptation for cross- domain classification.” in IJCAI, 2016, pp. 3691–3697.

[145] R. Xu, Z. Chen, W. Zuo, J. Yan, and L. Lin, “Deep cocktail network: Multi- source unsupervised domain adaptation with category shift,” in CVPR, 2018, pp.
3964–3973.

[146] H. Zhao, S. Zhang, G. Wu, J. M. Moura, J. P. Costeira, and G. J. Gordon, “Adversarial multiple source domain adaptation,” in NeurIPS, 2018, pp. 8568– 8579.

[147] J. Hoffman, M. Mohri, and N. Zhang, “Algorithms and theory for multiple-source adaptation,” in NeurIPS, 2018, pp. 8246–8256.

[148] X. Peng, Q. Bai, X. Xia, Z. Huang, K. Saenko, and B. Wang, “Moment matching for multi-source domain adaptation,” in ICCV, 2019, pp. 1406–1415.

[149] S. Zhao, B. Li, X. Yue, Y. Gu, P. Xu, R. Hu, H. Chai, and K. Keutzer, “Multi- source domain adaptation for semantic segmentation,” in NeurIPS, 2019, pp.
7285–7298.

[150] S. Zhao, G. Wang, S. Zhang, Y. Gu, Y. Li, Z. Song, P. Xu, R. Hu, H. Chai, and K. Keutzer, “Multi-source distilling domain adaptation,” in AAAI, 2020.

[151] C. Lin, S. Zhao, L. Meng, and T.-S. Chua, “Multi-source domain adaptation for visual sentiment classification,” in AAAI, 2020.

[152] K. Muandet, D. Balduzzi, and B. Schölkopf, “Domain generalization via invariant feature representation,” in ICML, 2013, pp. 10–18.

[153] D. Li, Y. Yang, Y.-Z. Song, and T. M. Hospedales, “Learning to generalize: Meta- learning for domain generalization,” in AAAI, 2018.

[154] H. Li, S. Jialin Pan, S. Wang, and A. C. Kot, “Domain generalization with adversarial feature learning,” in CVPR, 2018, pp. 5400–5409.

[155] Y. LeCun, L. Bottou, Y. Bengio, P. Haffner et al., “Gradient-based learning applied to document recognition,” PIEEE, vol. 86, no. 11, pp. 2278–2324, 1998.

[156] Y. Ganin and V. Lempitsky, “Unsupervised domain adaptation by backpropaga- tion,” in ICML, 2015, pp. 1180–1189.

[157] J. J. Hull, “A database for handwritten text recognition research,” IEEE TPAMI, vol. 16, no. 5, pp. 550–554, 1994.

[158] Y. Netzer, T. Wang, A. Coates, A. Bissacco, B. Wu, and A. Y. Ng, “Reading digits in natural images with unsupervised feature learning,” in NeurIPS Workshops, 2011.

[159] K. Saenko, B. Kulis, M. Fritz, and T. Darrell, “Adapting visual category models to new domains,” in ECCV, 2010, pp. 213–226.

[160] X. Peng, B. Usman, N. Kaushik, J. Hoffman, D. Wang, and K. Saenko, “Visda: The visual domain adaptation challenge,” arXiv:1710.06924, 2017.

[161] X. Zhang, Y. Sugano, M. Fritz, and A. Bulling, “Appearance-based gaze estimation in the wild,” in CVPR, 2015, pp. 4511–4520.

[162] J. Tompson, M. Stein, Y. Lecun, and K. Perlin, “Real-time continuous pose recovery of human hands using convolutional networks,” ACM TOG, vol. 33, no. 5, p. 169, 2014.

[163] A. Geiger, P. Lenz, and R. Urtasun, “Are we ready for autonomous driving? the 19 kitti vision benchmark suite,” in CVPR, 2012, pp. 3354–3361.

[164] C. Sakaridis, D. Dai, and L. Van Gool, “Semantic foggy scene understanding with synthetic data,” IJCV, vol. 126, no. 9, pp. 973–992, 2018.

[165] M. Johnson-Roberson, C. Barto, R. Mehta, S. N. Sridhar, K. Rosaen, and R. Vasudevan, “Driving in the matrix: Can virtual worlds replace human-generated annotations for real world tasks?” in ICRA, 2017, pp. 746–753.

[166] X. Peng, B. Usman, K. Saito, N. Kaushik, J. Hoffman, and K. Saenko, “Syn2real: A new benchmark for synthetic-to-real visual domain adaptation,” arXiv:1806.09755, 2018.

[167] F. Yu, W. Xian, Y. Chen, F. Liu, M. Liao, V. Madhavan, and T. Darrell, “Bdd100k: A diverse driving video database with scalable annotation tooling,” arXiv:1805.04687, 2018.

[168] G. Ros, L. Sellart, J. Materzynska, D. Vazquez, and A. M. Lopez, “The synthia dataset: A large collection of synthetic images for semantic segmentation of urban scenes,” in CVPR, 2016, pp. 3234–3243.

[169] P. Arbelaez, M. Maire, C. Fowlkes, and J. Malik, “Contour detection and hierarchical image segmentation,” IEEE TPAMI, vol. 33, no. 5, pp. 898–916, 2011.

[170] G. Griffin, A. Holub, and P. Perona, “Caltech-256 object category dataset,” California Institute of Technology, 2007.

[171] T.-Y. Lin, M. Maire, S. Belongie, J. Hays, P. Perona, D. Ramanan, P. Dollár, and C. L. Zitnick, “Microsoft coco: Common objects in context,” in ECCV. Springer, 2014, pp. 740–755.

[172] E. Real, J. Shlens, S. Mazzocchi, X. Pan, and V. Vanhoucke, “Youtube- boundingboxes: A large high-precision human-annotated data set for object detection in video,” in CVPR, 2017, pp. 5296–5305.

[173] S. Ben-David, J. Blitzer, K. Crammer, A. Kulesza, F. Pereira, and J. W. Vaughan, “A theory of learning from different domains,” Machine Learning, vol. 79, no.
1-2, pp. 151–175, 2010.

[174] Y. Mansour, M. Mohri, and A. Rostamizadeh, “Domain adaptation: Learning bounds and algorithms,” in COLT, 2009.

[175] Y. Mansour and M. Schain, “Robust domain adaptation,” Annals of Mathematics and Artificial Intelligence, vol. 71, no. 4, pp. 365–380, 2014.

[176] H. Xu and S. Mannor, “Robustness and generalization,” Machine Learning, vol. 86, no. 3, pp. 391–423, 2012.

[177] Y. Mansour, M. Mohri, and A. Rostamizadeh, “Multiple source adaptation and the rényi divergence,” in UAI, 2009, pp. 367–374.

[178] J. Bromley, I. Guyon, Y. LeCun, E. Säckinger, and R. Shah, “Signature verification using a" siamese" time delay neural network,” in NeurIPS, 1994, pp. 737–744.

[179] G. Koch, R. Zemel, and R. Salakhutdinov, “Siamese neural networks for one-shot image recognition,” in ICML Deep Learning Workshop, 2015.

[180] M.-Y. Liu and O. Tuzel, “Coupled generative adversarial networks,” in NeurIPS, 2016, pp. 469–477.

[181] I. Goodfellow, J. Pouget-Abadie, M. Mirza, B. Xu, D. Warde-Farley, S. Ozair, A. Courville, and Y. Bengio, “Generative adversarial nets,” in NeurIPS, 2014, pp.
2672–2680.

[182] J.-Y. Zhu, T. Park, P. Isola, and A. A. Efros, “Unpaired image-to-image translation using cycle-consistent adversarial networks,” in ICCV, 2017, pp. 2223–2232.

[183] D. P. Kingma and M. Welling, “Auto-encoding variational bayes,” in ICLR, 2014.

[184] M. Ghifary, W. Bastiaan Kleijn, M. Zhang, and D. Balduzzi, “Domain general- ization for object recognition with multi-task autoencoders,” in ICCV, 2015, pp.
2551–2559.

[185] F. D. Johansson, D. Sontag, and R. Ranganath, “Support and invertibility in domain-invariant representations,” in AISTATS, 2019, pp. 527–536.

[186] M. Sugiyama, M. Krauledat, and K.-R. MÃžller, “Covariate shift adaptation by importance weighted cross validation,” JMLR, vol. 8, no. May, pp. 985–1005, 2007.

[187] A. Gretton, A. Smola, J. Huang, M. Schmittfull, K. Borgwardt, and B. Schölkopf, “Covariate shift by kernel mean matching,” Dataset Shift in Machine Learning, vol. 3, no. 4, p. 5, 2009.

[188] Y.-L. Yu and C. Szepesvári, “Analysis of kernel mean matching under covariate shift,” in ICML, 2012, pp. 1147–1154.

[189] T. Adel, H. Zhao, and A. Wong, “Unsupervised domain adaptation with a relaxed covariate shift assumption,” in AAAI, 2017.

[190] Y. Li, M. Murias, S. Major, G. Dawson, and D. Carlson, “On target shift in adversarial domain adaptation,” in AISTATS, 2019, pp. 616–625.

[191] B. Kang and J. Feng, “Transferable meta learning across domains,” in UAI, 2018, pp. 177–187.

[192] Y. Balaji, S. Sankaranarayanan, and R. Chellappa, “Metareg: Towards domain generalization using meta-regularization,” in NeurIPS, 2018, pp. 998–1008.

[193] X. Peng, Z. Huang, Y. Zhu, and K. Saenko, “Federated adversarial domain adaptation,” in ICLR, 2020.

[194] X. Peng, Z. Huang, X. Sun, and K. Saenko, “Domain agnostic learning with disentangled representations,” in ICML, 2019, pp. 5102–5112.

[195] H.-Y. Lee, H.-Y. Tseng, J.-B. Huang, M. Singh, and M.-H. Yang, “Diverse image- to-image translation via disentangled representations,” in ECCV, 2018, pp. 35–51.

[196] M. Soleymani, J. Lichtenauer, T. Pun, and M. Pantic, “A multimodal database for affect recognition and implicit tagging,” IEEE TAFFC, vol. 3, no. 1, pp. 42–55.

[197] S. Shekhar, V. M. Patel, N. M. Nasrabadi, and R. Chellappa, “Joint sparse rep- resentation for robust multimodal biometrics recognition,” IEEE TPAMI, vol. 36, no. 1, pp. 113–126, 2013.

[198] Y. Gao, M. Wang, D. Tao, R. Ji, and Q. Dai, “3-d object retrieval and recognition with hypergraph analysis,” IEEE TIP, vol. 21, no. 9, pp. 4290–4303, 2012.

[199] S. Zhao, H. Yao, Y. Yang, and Y. Zhang, “Affective image retrieval via multi-graph learning,” in ACM MM, 2014, pp. 1025–1028.

[200] T. N. Kipf and M. Welling, “Semi-supervised classification with graph convolutional networks,” in ICLR, 2017.

[201] J. Gama, I. Žliobaitė, A. Bifet, M. Pechenizkiy, and A. Bouchachia, “A survey on concept drift adaptation,” CSUR, vol. 46, no. 4, pp. 1–37, 2014.

[202] J. Kirkpatrick, R. Pascanu, N. Rabinowitz, J. Veness, G. Desjardins, A. A. Rusu, K. Milan, J. Quan, T. Ramalho, A. Grabska-Barwinska et al., “Overcoming catastrophic forgetting in neural networks,” PNAS, vol. 114, no. 13, pp. 3521– 3526, 2017.

[203] F. Zenke, B. Poole, and S. Ganguli, “Continual learning through synaptic intelligence,” in ICML, 2017, pp. 3987–3995.

[204] S.-W. Lee, J.-H. Kim, J. Jun, J.-W. Ha, and B.-T. Zhang, “Overcoming catastrophic forgetting by incremental moment matching,” in NeurIPS, 2017, pp. 4652–4662.

[205] Z. Wu, X. Wang, J. E. Gonzalez, T. Goldstein, and L. S. Davis, “Ace: Adapting to changing environments for semantic segmentation,” in ICCV, 2019, pp. 2121– 2130.

[206] T. Wang, J.-Y. Zhu, A. Torralba, and A. A. Efros, “Dataset distillation,” arXiv:1811.10959, 2018.

[207] X. Yue, Y. Zhang, S. Zhao, A. Sangiovanni-Vincentelli, K. Keutzer, and B. Gong, “Domain randomization and pyramid consistency: Simulation-to-real generaliza- tion without accessing target domain data,” in ICCV, 2019, pp. 2100–2110.

[208] M. Kim and H. Byun, “Learning texture invariant representation for domain adaptation of semantic segmentation,” arXiv:2003.00867, 2020.

[209] D. Peterson, P. Kanani, and V. J. Marathe, “Private federated learning with domain adaptation,” arXiv:1912.06733, 2019.

[210] J.-H. Jacobsen, A. Smeulders, and E. Oyallon, “i-revnet: Deep invertible net- works,” in ICLR, 2018.

[211] L. Ardizzone, J. Kruse, S. Wirkert, D. Rahner, E. W. Pellegrini, R. S. Klessen, L. Maier-Hein, C. Rother, and U. Köthe, “Analyzing inverse problems with invertible neural networks,” in ICLR, 2019.

[212] J. Tobin, R. Fong, A. Ray, J. Schneider, W. Zaremba, and P. Abbeel, “Domain randomization for transferring deep neural networks from simulation to the real world,” in IROS, 2017, pp. 23–30.

[213] C. Vondrick, H. Pirsiavash, and A. Torralba, “Generating videos with scene dynamics,” in NeurIPS, 2016, pp. 613–621.

[214] S. Tulyakov, M.-Y. Liu, X. Yang, and J. Kautz, “Mocogan: Decomposing motion and content for video generation,” in CVPR, 2018, pp. 1526–1535.

[215] T.-C. Wang, M.-Y. Liu, J.-Y. Zhu, G. Liu, A. Tao, J. Kautz, and B. Catanzaro, “Video-to-video synthesis,” in NeurIPS, 2018, pp. 1144–1156.

[216] W.-S. Lai, J.-B. Huang, O. Wang, E. Shechtman, E. Yumer, and M.-H. Yang, “Learning blind video temporal consistency,” in ECCV, 2018, pp. 170–185.

[217] A. Vinciarelli and G. Mohammadi, “A survey of personality computing,” IEEE TAFFC, vol. 5, no. 3, pp. 273–291, 2014.

[218] D. Joshi, R. Datta, E. Fedorovskaya, Q.-T. Luong, J. Z. Wang, J. Li, and J. Luo, “Aesthetics and emotions in images,” IEEE SPM, vol. 28, no. 5, pp. 94–115, 2011.

[219] S. Zhao, G. Ding, Q. Huang, T.-S. Chua, B. W. Schuller, and K. Keutzer, “Affective image content analysis: A comprehensive survey,” in IJCAI, 2018, pp.
5534–5541.

[220] A. Boopathy, T.-W. Weng, P.-Y. Chen, S. Liu, and L. Daniel, “Cnn-cert: An efficient framework for certifying robustness of convolutional neural networks,” in AAAI, 2019, pp. 3240–3247.

[221] L. Weng, H. Zhang, H. Chen, Z. Song, C.-J. Hsieh, L. Daniel, D. Boning, and I. Dhillon, “Towards fast computation of certified robustness for relu networks,” in ICML, 2018, pp. 5276–5285.

[222] B. Wu, X. Dai, P. Zhang, Y. Wang, F. Sun, Y. Wu, Y. Tian, P. Vajda, Y. Jia, and K. Keutzer, “Fbnet: Hardware-aware efficient convnet design via differentiable neural architecture search,” in CVPR, 2019, pp. 10 734–10 742.

</div>
</details>
