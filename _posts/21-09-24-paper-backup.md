---
title: ๐ก Paper Backup
tags: paper ๐ก
mathjax_autoNumber: false
---

Self-supervised learning with image based synthetic teacher model   
(์ด๋ฏธ์ง ๊ธฐ๋ฐ์ ๊ฐ์ ๊ต์ฌ ๋ชจ๋ธ์ ์ด์ฉํ ์๊ธฐ ์ง๋ ํ์ต)

<!--more-->

- ๊ฐ์ฌ์ ๊ธ
- ๋ชฉ์ฐจ
- ๊ทธ๋ฆผ ๋ชฉ์ฐจ
- ํ ๋ชฉ์ฐจ

# ๊ตญ๋ฌธ ์์ฝ / ์๋ฌธ ์์ฝ

> 2์ชฝ ์ด๋ด

๋ณธ ๋ผ๋ฌธ์์๋ Unsupervised Domain Adaptation๊ณผ Semi-supervised learning์ ๊ฒฐํฉํ์ฌ domain ํ์ต ๋ฐ์ดํฐ์ ๋ํ ์์กด์ฑ์ ๊ฐ์์ํค๋ ๋์์ task loss๋ฅผ ์ต์ํ ํ  ์ ์๋ ๋ฐฉ์์ ๋ํด ์ฐ๊ตฌํ๊ณ ์ ํ๋ค.

- ํต์ฌ์ด: Unsupervised Domain Adaptation, Semi-supervised learning, Self-training, Pseudo-Labeling, Synthetic data

# 1. ์ฐ๊ตฌ ๋ฐฐ๊ฒฝ

ImageNet์ ๋ฐ์ดํฐ ๊ณต๊ฐ๋ฅผ ์์์ผ๋ก Data-driven ๊ธฐ๋ฐ์ Supervised learning์ด ์ฌ๋ฌ visual task ๋ถ์ผ์์ ์ข์ ์ฑ๊ณผ๋ฅผ ๊ฑฐ๋๋ค. ๊ฒ์ฆ๋ ์ข์ ๋ชจ๋ธ๋ค์ด ์ด๋ฏธ ์กด์ฌํ๊ธฐ ๋๋ฌธ์, ํน์  ๋ฌธ์ ์ ๋ง๋ AI๋ชจ๋ธ์ ๋ง๋ค๊ธฐ ์ํด์๋ ๊ทธ์ ๋ง๋ ์์ง์ ๋ฐ์ดํฐ๋ฅผ ์ผ๋ง๋ ๋ง์ด ํ๋ณดํ๋๋๊ฐ ๊ฒฐ๊ตญ ๋ชจ๋ธ์ ์ฑ๋ฅ์ ์ข์ฐํ๊ฒ ๋์๋ค.

ํ์ง๋ง ํน์  domain์ task์ ๋ง๋ ๋ฐ์ดํฐ๋ฅผ ํ๋ณดํ๊ณ , ๊ทธ ๋ฐ์ดํฐ์ labeling ํ๋ ๊ฒ์ ๋ง์ ์๊ฐ๊ณผ ๋น์ฉ์ ํ์๋ก ํ๋ค. ์ด๋ฌํ ๋ฐ์ดํฐ ์์กด์ ์ธ ๋ฌธ์ ๋ค์ ํด๊ฒฐํ๊ธฐ ์ํด ๋ฐ์ดํฐ๊ฐ ๋ถ์กฑํ๋๋ผ๋ ํจ๊ณผ์ ์ธ ํ์ต์ ํ  ์ ์๋ ๋ค์ํ ์ฐ๊ตฌ๋ค์ด ์งํ๋๊ณ  ์๋ค. ์ ์ฌํ task์์ ํ์ต๋ pretrain ๋ชจ๋ธ์ ๊ฐ์ ธ์ target task์ ์ ์ฉํ๋ Transfer Learning, Domain Adaptation, ๊ทธ๋ฆฌ๊ณ  labeled data๊ฐ ์ ๊ฑฐ๋ ์์ ๋ ์ฌ์ฉํ๋ Semi-Supervised Learning, Self-Supervised Learning ๋ฑ์ ์ฐ๊ตฌ๋ค์ด ์๋ค.

![์์จ์ฃผํ์ฐจ๋, ๋ก๋ณดํฑ์ค ๋ถ์ผ์์์ synthetic ๋ฐ์ดํฐ](/assets/images/21-09-24-paper-autonomous-synthetic-data.png)

Labeling ์์์ ๋น์ฉ์ด ํฐ ๋ฌธ์ ๋ ์์ง๋ง ํน์  ์ํฉ์ ๋ํ ๋ฐ์ดํฐ ์์ฒด๋ฅผ ์ป๊ธฐ ์ด๋ ค์ด ๊ฒฝ์ฐ๋ ์๋ค. ํนํ๋ ์์จ์ฃผํ์ฐจ๋, ๋ก๋ณดํฑ์ค ๋ถ์ผ์์๋ ํ์ค ์ธ๊ณ์์ ์ป๊ธฐ ์ด๋ ค์ด ๋ฐ์ดํฐ๋ค์ด ์กด์ฌํ๋ค. ์๋ฅผ ๋ค์ด ๊ต์ฐจ๋ก ํ๊ฐ์ด๋ฐ์ ์ฌ๋์ด ์๋ ๋ฐ์ดํฐ๋, ๋ก๋ด์ ์นด๋ฉ๋ผ ์์ ์์์ ๋ฐ์ดํฐ ๋ฑ์ ์ฝ๊ฒ ํ๋ํ  ์๊ฐ ์์ ๊ฒ์ด๋ค.

์ด ๊ฒฝ์ฐ ์ค์  ์ธ๊ณ๋ฅผ digital twin ํ์ฌ ์๋ฎฌ๋ ์ด์์ ๊ตฌ์ถํ๋ ๋ฐฉ์์ด ์๋ค. ์๋ฎฌ๋ ์ด์์ ์ฌ์ฉํ๊ฒ ๋๋ฉด ํ์ค ์ธ๊ณ์์ ์ฌํํ  ์ ์๋ ์ํฉ๋ค์ ์ฐ์ถํ๊ณ  ํ๊ฒฝ์ ์์ ์์ฌ๋ก ๋ณ๊ฒฝํ์ฌ ๋ฐ์ดํฐ๋ค์ ์ถ์ถํ  ์๊ฐ ์๋ค. ์ปดํจํฐ ๊ทธ๋ํฝ์ ์ํด RGB ์ด๋ฏธ์ง, depth ๋ฐ์ดํฐ, ๊ฐ์ฒด์ bounding box, pixel ๋ณ class, optical flow ๋ฑ์ ์ ํํ Ground Truth ๋ฐ์ดํฐ๋ค์ด ์๋์ผ๋ก ์์ฑ๋๊ธฐ ๋๋ฌธ์ ์ด๋ฌํ synthetic ๋ฐ์ดํฐ๋ฅผ ์ด์ฉํด AI ํ์ต์ ์ฌ์ฉํ  ์ ์๋ค.

Synthetic ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํด ํ์ตํ ๋ชจ๋ธ์ด ํ์ค ์ธ๊ณ์์ ์ ๋์ํ๊ธฐ ์ํด์๋ ์ค์  ์ธ๊ณ์ ์๋ฎฌ๋ ์ด์ ๊ฐ์ ๊ฒฉ์ฐจ๊ฐ ์์์ผ๋ง ํ๋ค. ํ์ง๋ง ์๋ฎฌ๋ ์ด์์ ์ต๋ํ ์ฌ์ค์ ์ผ๋ก ๋ง๋ค๊ธฐ ์ํด์๋ ๋ง์ ๋ธ๋ ฅ์ด ๋ค์ด๊ฐ๊ณ , ์ค์  ์ธ๊ณ์ ๋ชจ๋  ๋ฐ์ดํฐ๋ค์ ์ ๋์ ์ผ๋ก ๋ชจ๋ธ๋งํ๊ธฐ์๋ ์ฝ์ง ์์ ์ผ์ด๋ค.

๋ณธ ๋ผ๋ฌธ์์๋ synthetic ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํ์ฌ domain ํ์ต ๋ฐ์ดํฐ์ ๋ํ ์์กด์ฑ์ ๊ฐ์์ํค๋ ๋์์ ์ค์  ์ธ๊ณ์์๋ ์ ๋์ํ  ์ ์๋๋ก task loss๋ฅผ ์ต์ํ ํ  ์ ์๋ ๋ฐฉ์์ ๋ํด ์ฐ๊ตฌํ๊ณ ์ ํ๋ค.

# 2. ์ ํ ์ฐ๊ตฌ

## 2.1. Semantic Segmentation

Semantic segmentation(SS) ๋ ์ด๋ฏธ์ง์ ๊ฐ ํฝ์์ด ์ด๋ ํด๋์ค์ ์ํ๋ ์ง ์์ธกํ๋ ๊ฒ์ผ๋ก, ์ด๋ฏธ์ง์ ์ ๋ฐ์ ์ธ ์๋ฏธ์ ๊ตฌ์กฐ๋ฅผ ํ์ํ์ฌ ๋ ๊น์ด ์๊ฒ ์ดํดํ๋ ์์์ด๋ค. ํฝ์ ์์ค์ ๋ ์ด๋ธ๋ง์ด ์ํ๋๊ธฐ ๋๋ฌธ์ dense labeling task ๋ผ๊ณ  ๋ถ๋ฆฌ๊ณ , ์ด๋ classification ์ด๋ localization์ ๋นํด ์ด๋ ค์ด ๋ฌธ์ ๋ก ๋ถ๋ฅ๋๋ค.

๋ฅ๋ฌ๋ ์ํคํ์ฒ๊ฐ ๋์ค๋ฉด์ ์ฑ๋ฅ์ด ์๋นํ ๊ฐ์ ๋์๋๋ฐ ์ต๊ทผ์๋ ์๋ ฅ ๊ณต๊ฐ ์ฐจ์์ ์ ์งํ๋ฉฐ ์ ์ญ์ semantic ์ ์ถ์ถํ๊ธฐ ์ํด encoder, decoder๋ก ๊ตฌ์ฑ๋ auto-encoder ๊ตฌ์กฐ๊ฐ ๋ง์ด ์ฌ์ฉ๋๊ณ  ์๋ค. ๋ํ์ ์ผ๋ก FCN, PSPNet, DRN, DeepLab ๋ฑ๊ณผ ๊ฐ์ ์ํคํ์ฒ๋ค์ด ์ ์๋์์ผ๋ฉฐ ์ข์ ์ฑ๋ฅ์ ๋ณด์ด๊ณ  ์๋ค.

ํ์ง๋ง ๋๋์ labeled ๋ฐ์ดํฐ์ ์์กด์ ์ด๋ผ ๋ฐ์ดํฐ์์ ํ๋ณดํ๋๋ฐ์ ๋ง์ ์๊ฐ์ ๋ค์ฌ์ผํ๋ค. ํฝ์ ๋ณ๋ก annotation ํ๋ ๊ฒ์ ๋ค๋ฅธ visual task ์ ๋นํด ๋น์ฉ๊ณผ ์๊ฐ์ด ๋ง์ด ์์๋๊ธฐ ๋๋ฌธ์ ์ํ๋ domain์ ๋ฐ์ดํฐ๋ฅผ ์ป๊ธฐ๊ฐ ์ฝ์ง ์๋ค.

์ด์ ๋ฐ๋ผ synthetic ๋ฐ์ดํฐ์ ๋ํ ํ์ฉ์ฒ๊ฐ ๋์ task๋ก ๋ถ๋ฅ๋  ์ ์๊ธฐ ๋๋ฌธ์ ๋ณธ ๋ผ๋ฌธ์์๋ semantic segmentation์ downstream task๋ก ์ค์ ํ์ฌ ์ฐ๊ตฌ๋ฅผ ์งํํ๊ณ ์ ํ๋ค. ์์ด ๋ง๊ณ  ์ฝ๊ฒ ์ป์ ์ ์๋ synthetic ๋ฐ์ดํฐ๋ฅผ ์ด์ฉํ์ฌ ์ง์์ ์ถ์ถํ๊ณ  ์ด๋ฅผ ํ์ฉํ์ฌ ์๋์ผ๋ก labeling ํ๋ ๋ฐ์ดํฐ์ ์์ ์ค์ด๊ณ ์ ํ๋ค. 

## 2.3. Unsupervised Domain Adaptation

์ผ๋ฐ์ ์ผ๋ก Labeled ๋ฐ์ดํฐ๊ฐ ๋ถ์กฑํ๋ฉด pre-trained๋ ํฐ ๋ชจ๋ธ์ ์ด์ฉํ์ฌ transfer learning์ ์ํํ๋ ๋ฐฉ๋ฒ์ด ์๋ค. Transfer learning์ ํ๊ธฐ ์ํด์๋ ํด๋น domain์ ๋ง๋ labeled ๋ฐ์ดํฐ๊ฐ ํ์ํ์ง๋ง Domain Adaptation(DA)์ ๋ค๋ฅธ domain(Source)์์ ๋ฐฐ์ด ์ง์์ ์ฌ์ฉํด ์๋ก์ด domain(Target)์ labeled ๋ฐ์ดํฐ๊ฐ ์๋ ์ํฉ์์๋ ๋ชจ๋ธ์ ์ฑ๋ฅ์ ์ฌ๋ฆด ์ ์๋ ๋ฐฉ๋ฒ์ด๋ค. Zero-shot learning, few-shot learning, self-supervised learning๊ณผ ํจ๊ป sample-efficient learning์ ํ ์ ํ์ด๋ค.

![](/assets/images/21-09-24-paper-domain-adaptation.png)

Source domain๊ณผ target domain ๊ฐ์ ๋ฐ์ดํฐ ๋ถํฌ๊ฐ ๋ค๋ฅด๊ธฐ ๋๋ฌธ์ source domain์์ ํ์ต๋ ๋ชจ๋ธ์ target domain์ ์ง์ ์ ์ผ๋ก transfer ํ๊ฒ ๋๋ฉด doamin shift(๋๋ domain gap)์ dataset bias์ ์ํด ์ค์ฐจ๊ฐ ๋ฐ์ํ  ์ ์๋ค. ์ด ๋ Domain Adaptation ๋ฐฉ๋ฒ๋ก ์ ์ฌ์ฉํ๋ฉด source ์ target ๋ฐ์ดํฐ ๋ถํฌ๋ฅผ ์ ์ฌํ๊ฒ ํ์ตํ  ์ ์๋ค. 

Unsupervised Domain Adaptation(UDA)๋ Target domain์ unlabeled ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํ๊ธฐ ์ํด ๋ค๋ฅธ domain์ labeled ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํ๋ ์ผ์ข์ Semi-supervised learning์ ๋ณํ์ด๋ผ๊ณ  ๋ณผ ์ ์๋ค. Target domain์ labeled ๋ฐ์ดํฐ๋ ํ์ํ์ง ์์ง๋ง ์ถฉ๋ถํ ์์ Unlabeled target ๋ฐ์ดํฐ๊ฐ ํ์ํ๋ค.

![](/assets/images/21-09-24-paper-adaptation-level.png)

DA ๋ฌธ์ ๋ฅผ ํ๊ธฐ ์ํ ์ฃผ์ ์ ๋ต์ source domain๊ณผ target domain ์ฌ์ด์ ๊ฒฉ์ฐจ๋ฅผ ์ค์์ผ๋ก์จ ์์ธก ๋ชจ๋ธ์ ์ฑ๋ฅ ์ ํ๋ฅผ ๋ง๋ ๋ฐฉ๋ฒ์ด๋ค. Input-level, feature-level, output-level์์ ๊ฐ๊ฐ adaptation ๋ชจ๋์ ์ถ๊ฐํ์ฌ domain ๊ฐ ๋ถ์ผ์น์ฑ์ ์ค์ผ ์ ์๋ค.

Adversarial Discriminative Adaptation by Backpropagation(CVPR 2017)๋ UDA์ GAN์ ๋์ํ์ฌ adversarial adaptation์ ์ฒ์ ์ ์ํ ๋ผ๋ฌธ์ผ๋ก, featue level์ domain discriminator๋ฅผ ์ถ๊ฐํ๊ณ  adversarial loss๋ฅผ ์ฌ์ฉํ์ฌ domain discrepancy๋ฅผ ์ต์ํ ํ๋ ๋ฐฉ๋ฒ์ ์ ์ํ๋ค. 

> feature level์ adversarial learning์ ๋์ํ์ฌ, source, target domain ๊ฐ confusion์ ์ ๋ํ๋ค. segmentation ๋ชจ๋ธ์ source, target์์ ๋์ผํ๊ฒ ์ฌ์ฉํ๊ณ  feature extractor๋ฅผ ํตํด ๋์จ feature๊ฐ๋ค์ด source domain์ธ์ง target domain์ธ์ง ๊ตฌ๋ถํ๊ธฐ ์ด๋ ต๊ฒ ํ์ต์ํด์ผ๋ก์จ domain ๋ถ์ผ์น์ฑ์ ์ค์ด๋ฉฐ ๋์์ segmentation ๋ชจ๋ธ์ด ํ์ต๋๋๋ก ํ๋ค.

Cycle-Consistent Adversarial Domain Adaptation(ICML 2018)์์๋ feature level์ adversarial learning์ ๊ทธ๋๋ก ์ฌ์ฉํ๊ณ , input-level์์ source domain ์ด๋ฏธ์ง๋ฅผ target ์ด๋ฏธ์ง์ ์ ์ฌํ ์คํ์ผ๋ก ์์ฑํ๋๋ก generative ๋ชจ๋ธ์ ๋์ํ ๋ฐฉ๋ฒ์ ์ ์ํ๋ค. Generative ๋ชจ๋ธ์ ๋์ํจ์ผ๋ก์จ input ๋ฐ์ดํฐ์ ์จ๊ฒจ์ง ํ๋ฅ ๋ถํฌ๋ฅผ ์ฐพ์๋ด๊ณ  ์ ์ฌํ ํ๋ฅ ๋ถํฌ์ ๋ฐ์ดํฐ๋ฅผ ์์ฑํด ์ฌ์ฉํ๋ ๋ฐฉ์์ด๋ค.

> Input level์์์ domain gap์ ์ค์ด๊ธฐ ์ํด source domain์ ์ด๋ฏธ์ง๋ฅผ target ์ด๋ฏธ์ง์ ์ ์ฌํ ์คํ์ผ๋ก ์์ฑํ๋๋ก generative ๋ชจ๋ธ์ ์ฌ์ฉํ๋ ๋ฐฉ๋ฒ์ด ์ฌ์ฉ๋๋ค. Generative ๋ชจ๋ธ์ ๋์ํจ์ผ๋ก์จ input ๋ฐ์ดํฐ์ ๋ํ ์จ๊ฒจ์ง ํ๋ฅ ๋ถํฌ๋ฅผ ์ฐพ์๋ด๊ณ , ์ด์ discriminator๋ฅผ ๋ํด domain confusion์ ์ ๋ํ  ์ ์๋ค. ๊ฒฐ๊ณผ์ ์ผ๋ก input-level์ adaptation module์ ์ ์ฌํ ํ๋ฅ ๋ถํฌ์ ๋ฐ์ดํฐ๋ฅผ ์์ฑํด ๋ด๋ ๋ฐ์ ๋ชฉํ๊ฐ ์๋ค. 

Learning to Adapted Structured Output space(CVPR 2018)์์๋ ๋ domain์ ์ด๋ฏธ์ง ์คํ์ผ์ด ๋ค๋ฅด๋๋ผ๋ segmentation ๊ฒฐ๊ณผ์ ํฌํจ๋์ด ์๋ ๊ณต๊ฐ ๋ ์ด์์, local context ๋ฑ์ ๊ตฌ์กฐํ๋ ํน์ฑ์ ์ ์ฌํ๊ธฐ ๋๋ฌธ์ ouput-level์ adversarial learning์ ๋์ํ์ฌ segmentation ์์ธก ๊ฒฐ๊ณผ distribution์ ์ ์ฌํ๊ฒ ํ์ตํ๋ ๋ฐฉ๋ฒ์ ์ ์ํ๋ค.

![](/assets/images/21-09-24-paper-da-methods.png)

์ ๋ฆฌํ๋ฉด ๊ธฐ์กด์ UDA์ฐ๊ตฌ๋ source domain์ ์ด๋ฏธ์ง๋ฅผ target ์ด๋ฏธ์ง์ ์ ์ฌํ ์คํ์ผ๋ก ์์ฑํ๊ธฐ ์ํด generative ๋ชจ๋ธ์ ์ฌ์ฉํ๊ฑฐ๋, domain confusion์ ์ํด discriminator ๋ฅผ ์ฌ์ฉํ๊ฑฐ๋, adversiarl learning์ ํตํด ๋ domain์ ์ฐจ์ด๋ฅผ ์ธก์ ํ๋ ๋ฐฉ์์ผ๋ก ๋ง์ด ์ฐ๊ตฌ๋๊ณ  ์๋ค. discrepancy-based methods, adversarial discriminative methods, generative model์ ๋ณํํ๊ฑฐ๋ ์ ์ ์ด ์์ด๊ฐ๋ฉฐ ๋ค์ํ ๋ฐฉ๋ฒ๋ค์ด ์ ์๋๊ณ  ์๋ค. ๊ทธ ์ธ์๋ ๋ถ๋ฅ๊ธฐ ๋ถ์ผ์น ๋ถ์, ์ํธ๋กํผ ์ต์ํ, ์ปค๋ฆฌํ๋ผ ํ์ต๊ณผ ๊ฐ์ ๋ฐฉ๋ฒ๋ ์ ์๋๋ค.

๋ณธ ๋ผ๋ฌธ์์๋ taget domain๊ณผ ์ ์ฌํ ์คํ์ผ์ ์ด๋ฏธ์ง๋ฅผ ์์ฑํ์ฌ ํ์ต ๋ฐ์ดํฐ๋ก ์ฌ์ฉํ๊ณ , feature-level ๊ณผ output-level์ adversarial learning์ ๋์ํจ์ผ๋ก์จ domain gap์ ์ค์ด๊ณ ์ ํ๋ค.

> - adversarial learning : target์ distribution์ด source์ ์ ์ฌํ๋๋ก ํ์ต์ ์งํ. source ์ชฝ์์ ํ์ตํ feature๋ค์ target domain condition์ผ๋ก ์ฎ๊ฒจ์ฃผ๊ฒ ๋ค ๋ผ๋ ์๋ฏธ.
> - Generative network : target image๋ฅผ source์ ์ ์ฌํ๊ฒ ๋ง๋ค์ด์ ํ์ต์ ์ํค์. ํน์ source์ style์ target์ ๋ง๊ฒ ์ฌ์์ฐํด์ ํ์ต์ํค์

> - Entropy minimization, Generative ๋ชจ๋ธ, adversarial learning์ ํตํด domain invariance representation์ ์ถ์ถํ๋ ๋ฐฉ๋ฒ์ด ์ข์ ๊ฒฐ๊ณผ๋ฅผ ๊ฐ์ ธ์๋ค. 
> - ์ํธ๋กํผ ์ต์ํ[31, 43], ์์ฑ ๋ชจ๋ธ๋ง[16, 12] ๋ฐ ์ ๋์  ํ์ต[42, 41]์ ํตํด domain ๋ถ๋ณ ํํ์ ์ถ์ถํ๋ UDA ๋ฐฉ๋ฒ์ ์ํด ์ธ์์ ์ธ ๊ฒฐ๊ณผ๊ฐ ๋ฌ์ฑ๋์์ต๋๋ค. 
> - ๋ฉ์ธ ๊ฐ ๋ถ์ผ์น๋ฅผ ์ค์ด๊ธฐ ์ํด ์๋ง์ UDA ๋ฐฉ๋ฒ[19, 42, 43, 34]์ ์ ๋์  ํ์ต์ ๋์ํ์ฌ ๋ถํฌ ์ผ๊ด์ฑ์ ์ค์ ์ ๋ก๋๋ค. ์ด๋ฏธ์ง์์ ์ด๋ฏธ์ง๋ก์ ๋ณํ[21, 54]์์ ์๊ฐ์ ๋ฐ์ ์์ค ๋ฐ์ดํฐ์ ๋ฐ๋ผ ๋์ ์ด๋ฏธ์ง๋ฅผ ์์ฑํ๊ธฐ ์ํด UDA ๋ฐฉ๋ฒ ๋ฒ์ฃผ๊ฐ ์ ์๋์์ต๋๋ค[19, 18]. 
> - ๋์ ์์ฌ ๋ ์ด๋ธ์ ์ฌ์ฉํ ์์ฒด ๊ฐ๋์ ๋น๊ต์  ๊ฐ๋จํ์ง๋ง ํจ์จ์ ์ธ ์ ๊ทผ ๋ฐฉ์์ด์ง๋ง[6, 55] ๊ฐ๋์ ์ํ ์์ค ๋ฐ์ดํฐ๊ฐ ํ์ํฉ๋๋ค.
> - ์ฃผ๋ฅ ์ ๊ทผ ๋ฐฉ์ ์ค ํ๋๋ ์ ๋์  ํ์ต[42, 41, 6, 5, 17, 37, 19]์ด๋ฉฐ, ํ๋ณ์๋ฅผ ์ฌ์ฉํ์ฌ ๋ domain์ ์ฐจ์ด๋ฅผ ์ธก์ ํ๋ ๊ฒ์ ๋ชฉํ๋ก ํฉ๋๋ค. 
> - ์์ฑ ๋คํธ์ํฌ[38, 16, 50]๋ฅผ ํ์ฉํ์ฌ ์ฃผ์์ด ๋ฌ๋ฆฐ ์์ค ์ด๋ฏธ์ง์ ์คํ์ผ ์ ์ก ๊ธฐ์ ์ ์ ์ฉํ์ฌ ๋์ ์คํ์ผ ์ด๋ฏธ์ง๋ฅผ ์์ฑํ๋ ๊ฒ์๋๋ค. 
> - ์ ๋์  ํ์ต, ์์ฑ ๊ธฐ๋ฐ, ๋ถ๋ฅ๊ธฐ ๋ถ์ผ์น ๋ถ์, ์๊ฐ ํ์ต, ์ํธ๋กํผ ์ต์ํ, ์ปค๋ฆฌํ๋ผ ํ์ต๊ณผ ๊ฐ์ (์ํธ ๋ฐฐํ์ ์ด์ง ์์) ๋ฒ์ฃผ๋ฅผ ๊ธฐ๋ฐ

## 2.3. Semi-supervised learning

Semi-supervised learning(SSL)์ ์๋์ labeled ๋ฐ์ดํฐ๊ณผ ๋ค๋์ unlabeled ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํ์ฌ ํ์ตํ๋ ๋ฐฉ์์ด๋ค.

Self-training์ ์๋์ labeled ๋ฐ์ดํฐ๊ณผ ๋ค๋์ unlabeled ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํ์ฌ ํ์ตํ๋ ๋ฐฉ์์ธ Semi-supervised learning(SSL)์ ํ ๋ฐฉ๋ฒ์ด๋ค. Labeled ๋ฐ์ดํฐ๋ก ์ถฉ๋ถํ ํ์ต๋ ๋ชจ๋ธ์ ์ฌ์ฉํ์ฌ unlabeled ๋ฐ์ดํฐ์ pseudo-labeling ์ ํ๊ณ , pseudo labeled ๋ฐ์ดํฐ์ labeled ๋ฐ์ดํฐ๋ฅผ ํจ๊ป ํ์ต์ ์ฌ์ฉํ๋ ๋ฐฉ์์ด๋ค. 

Noisy Student(CVPR 2020)๋ ๋ํ์ ์ธ Teacher-student ๊ธฐ๋ฐ์ Self-training์ผ๋ก, labeled ๋ฐ์ดํฐ๋ก ํ์ต๋ teacher๋ฅผ ์ด์ฉํด unlabeled ๋ฐ์ดํฐ์ pseudo-labeling ํ๊ณ  ์ด ๋ฐ์ดํฐ์ ๋ธ์ด์ฆ๋ฅผ ์ถ๊ฐํ์ฌ student ๋ชจ๋ธ์ ํ์ตํ๋ ๋ฐฉ๋ฒ์ด๋ค. Student ๋ชจ๋ธ์ด ์ด๋์ ๋ ํ์ต์ด ๋๋ฉด teacher ๋ชจ๋ธ๋ก ์ค์ ํ๊ณ  ๋ค์ pseudo labeling์ ์ํํ๊ฒ ๋ง๋ค์ด ๋ฐ๋ณต์ ์ธ ํ์ต ํ์ดํ๋ผ์ธ์ ๊ตฌ์ฑํ๊ณ  ์ด๋ฅผ ํตํด SOTA๋ฅผ ๊ธฐ๋กํ  ์ ๋๋ก ์ข์ ์ฑ๋ฅ์ ๋ณด์ธ ์ฐ๊ตฌ์ด๋ค. Meta Pseudo Labels(CVPR 2021)์ Teacher ๋ชจ๋ธ๊ณผ Student ๋ชจ๋ธ์ ๋์์ ํ์ตํ๋ ๋ฐฉ๋ฒ์ผ๋ก, Teacher ๋ชจ๋ธ์ด ์ ํ์ต๋์ด ์์ง ์๋๋ผ๋ student ๋ชจ๋ธ๊ณผ ์ ๊ธฐ์ ์ผ๋ก ํ์ตํ  ์ ์๋๋ก Co-training ๋ฐฉ์์ ์ ์ํ์๋ค.

> Noise๋ฅผ ์ถ๊ฐํจ์ผ๋ก์จ regularization ํจ๊ณผ๋ฅผ ์ฃผ๊ณ  ์๋ก์ด input๋ค์ ๋ํด robust ํ๊ฒ ํ์ตํ๋๋ก ํ๋ค.

์ต๊ทผ ๋ง์ด ์ฌ์ฉ๋๋ SSL ๋ฐฉ๋ฒ์ Consistency regularization์ผ๋ก, Unlabeled ๋ฐ์ดํฐ์ noise๋ฅผ ์ถ๊ฐํ๋๋ผ๋ ์์ธก ๋ถํฌ๋ ์ ์ง๋๋ค๋ ์์ด๋์ด์ ๊ธฐ์ํ ๋ฐฉ๋ฒ์ด๋ค. ๋ธ์ด์ฆ๊ฐ ์๋ ์๋ณธ ๋ฐ์ดํฐ์ ๋ธ์ด์ฆ๊ฐ ์ฃผ์๋ ๋ฐ์ดํฐ๋ฅผ ๋์ผํ ์์ธก ๋ถํฌ๋ก ํ์ตํ๋ ๊ฒ์ด consistency regularizatio์ ๋ชฉํ์ด๋ค. 
Virtual Adversarial Training๋ ์๋ ฅ ๋ฐ์ดํฐ์ adversarial transformation๋ฅผ ์์ฑํ๊ณ  ์์ธก ๊ฒฐ๊ณผ ๊ฐ์ KL-divergence๋ฅผ ์ธก์ ํ์ฌ ํ์ต์ํค๋ ๋ฐฉ๋ฒ์ ์ ์ํ๋ค. 
Unsupervised Data Augmentation for Consistency Training๋ ๋ธ์ด์ฆ ๋์  ์ต์  augmentation ๊ธฐ๋ฒ์ธ AutoAugment๋ฅผ ์ฌ์ฉํ์ฌ ์์ธก ๊ฒฐ๊ณผ ๊ฐ KL-divergence๋ฅผ Loss๋ก ์ฌ์ฉํ ๋ฐฉ๋ฒ์ผ๋ก, ์ ์ labeled ๋ฐ์ดํฐ๋ง์ผ๋ก supervised learning์ ๋ฐ์ด๋๋ ์ฑ๋ฅ์ ๋ณด์๋ค.

๊ทธ๋ฆฌ๊ณ  MixMatch, FixMatch ๋ฑ Pseudo-labeling๊ณผ augmentation, consistency regularization์ ํฌํจํ ๋ค์ํ regularization ๊ธฐ๋ฒ์ ํจ๊ป ์ฌ์ฉํ์ฌ ๋ค์ํ semi-supervised learning ์ฐ๊ตฌ๋ค์ด ๋์ค๊ณ  ์๋ค.

๋ณธ ๋ผ๋ฌธ์์๋ ํ์ต๋ adaptation ๋ชจ๋ธ์ ์ฌ์ฉํด unlabeled target ๋ฐ์ดํฐ์ pseudo-label์ ์์ฑํ๊ณ , ํ์ต ๋ฐ์ดํฐ๋ก ์ฌ์ฌ์ฉํ  ์ ์๋๋ก Self-training ํ์ดํ๋ผ์ธ์ ๊ตฌ์ฑํจ์ผ๋ก์จ target domain์ ๋ง๊ฒ ๋ชจ๋ธ์ fine-tuning ํ๋ค.

> - ๋์ domain์ ์์ ์ด๋ฏธ์ง ์ธํธ์๋ง ์ฃผ์์ ๋ฌ๊ณ  ๋ฐ ์ง๋ ํ์ต(SSL) ๊ธฐ์ ์ ์ฌ์ฉํ์ฌ ๋ ์ด๋ธ์ด ์ง์ ๋์ง ์์ ๋ฐ์ดํฐ๋ฅผ ์ถฉ๋ถํ ํ์ฉํ๋ ๊ฒ์๋๋ค[10, 30, 9, 29]. SSL ์ค์ ์์ ๋ ์ด๋ธ๋ง๋ ๋ฐ์ดํฐ์ ๋ถ์กฑ์ผ๋ก ์ธํด ํ๋ํ ๋ชจ๋ธ์ ๋ ์ด๋ธ๋ง๋ ๋ฐ์ดํฐ์ ์๋์ ๊ณผ์ ํฉ๋  ์ํ์ด ์์ต๋๋ค. ๋ค๋ฅธ domain์์ ์ฌ์ฉ ๊ฐ๋ฅํ ๋ ์ด๋ธ์ด ์ง์ ๋์ง ์์ ์ ํ๋ ๋ ์ด๋ธ์ด ์ง์ ๋ ๋ฐ์ดํฐ๋ฅผ ํจ๊ณผ์ ์ผ๋ก ํ์ฉํ๋ ๋ฐฉ๋ฒ์ ํฝ์ ๋จ์ ์์ธก ์์์ ๋ํ ๋ชจ๋ธ์ ์ ํ๋์ ์ผ๋ฐํ๋ฅผ ๊ฐ์ ํ๋ ๋ฐ ํต์ฌ์๋๋ค
> - ์์ฒด ํ๋ จ[21, 51, 23, 14]์ ๊ธฐ๋ฐํ ์ผ๋ถ ๋ฐฉ๋ฒ์ ๋ ์ด๋ธ์ด ์ง์ ๋์ง ์์ ๋ฐ์ดํฐ์ ์์ฌ ๋ ์ด๋ธ์ ์์ฑํ๊ณ  ์ด๋ฅผ ์ฌ์ฉํ์ฌ ๋ชจ๋ธ์ ๋ฏธ์ธ ์กฐ์ ํ๋ ๋ฐ ์ฌ์ฉ๋์์ต๋๋ค.
> - Self-Training : unlabeled data๋ฅผ ์ํด pseudo-labels๋ฅผ ์์ฑํด์ ํ์ตํ๋ ๋ฐฉ๋ฒ. ์ค์ค๋ก ํ์ํ GT๋ฅผ ๋๋ฆ๋๋ก ๋ง๋ค์ด์ ๊ทธ๊ฑธ ๊ธฐ๋ฐ์ผ๋ก ํ์ตํ๋ค๋ ์๋ฏธ
> - ๋ณธ ๋ผ๋ฌธ์์๋ ํ์ต๋ adaptation ๋ชจ๋ธ์ ์ฌ์ฉํด unlabeled ๋ฐ์ดํฐ์ pseudo-label์ ์์ฑํ๊ณ , ํ์ต ๋ฐ์ดํฐ๋ก ์ฌ์ฌ์ฉ ํ  ์ ์๋๋ก Self-training ํ์ดํ๋ผ์ธ์ ๊ตฌ์ฑํ๋ค.

# 3. ์ ์ ๋ฐฉ๋ฒ

Synthetic ๋ฐ์ดํฐ๋ง์ ํ์ต๋ ๋ชจ๋ธ์ real data์์ domain gap์ด ์กด์ฌํ๊ธฐ ๋๋ฌธ์, ์ค์  real world ์์ ์ฌ์ฉํ๊ธฐ์๋ ์ฑ๋ฅ์ด ์ข์ง ์๋ค. ๊ทธ๋ ๊ธฐ ๋๋ฌธ์ synthetic ๋ฐ์ดํฐ๋ง์ผ๋ก ํ์ต๋ ๋ชจ๋ธ์ ์ง์  adaptation ํ์ฌ ๋ชจ๋ธ์ ํ์ต์ํค์ง ์๊ณ , unlabeled real data์ pseudo-labeling ํด์ฃผ๋ teacher ๋ชจ๋ธ๋ก ์ฌ์ฉํ๊ณ ์ ํ๋ค.

Adapted data๋ก task model์ ์ถฉ๋ถํ ํ์ต์ํค๊ณ , ํ์ต๋ ๋ชจ๋ธ์ unlabeled ๋ฐ์ดํฐ์ pseudo labeling์ ํด์ค๋ค. ์ด๋์ ๋ labeled real data๊ฐ ๋ชจ์ด๋ฉด adapted data์ real data๋ฅผ ํจ๊ป ์๋ ฅ์ผ๋ก ๋ฃ์ด task model์ ํ์ต์ํค๊ณ , ์ ์ฐจ real dat์ ๋น์จ์ ๋๋ ค ๋๊ฐ์ผ๋ก์จ real world์์๋ ์ ๋์ํ๋ SSL ํ์ต ๋ฃจํ๋ฅผ ๊ตฌ์ฑํ๋ค.

![](/assets/images/21-09-24-paper-overview.png)

1. Real data์ synthetic data๋ฅผ ์ฌ์ฉํด GAN based DA ๋ชจ๋ธ์ ํ์ต์ํจ๋ค.
2. DA ๋ชจ๋ธ์ Generator๋ฅผ ์ด์ฉํด synthetic data๋ก๋ถํฐ adapted synthetic data๋ฅผ ์์ฑํ๋ค.
3. Adapted synthetic data์ GT ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํด task model์ ์ถฉ๋ถํ ํ์ต์ํจ๋ค.
4. ํ์ต๋ task model์ teacher ๋ชจ๋ธ๋ก ์ฌ์ฉํ์ฌ, unlabeled real data์ pseudo-labeling ํ๋ค.
5. Adapted synthetic data์ pseudo labeling๋ real data๋ฅผ ์ด์ฉํด task model์ ์ฌํ์ต์ํจ๋ค.
6. 4, 5 ๊ณผ์ ์ ๋ฐ๋ณตํ๋ค.

์์ ๊ฐ์ ํ์ต ๋ฃจํ๋ก ๋ฐ๋ณต์ ์ผ๋ก ํ์ต์ ์ํํ๋ฉฐ synthetic data์ real data ๊ฐ์ domain gap์ ์ค์ด๊ณ , ๋์์ pseudo-labeling ๋ชจ๋ธ์ ์ฑ๋ฅ์ ํจ๊ป ํฅ์์ํฌ ์ ์๋ ์์คํ์ ๊ตฌ์ฑํ๋ค. ๊ทธ๋ฆฌ๊ณ  ๋ฐ์ดํฐ์ ๋ค์ํ Noise ๊ธฐ๋ฒ ์คํ, unbalanced data ๋ณด๊ฐ ๋ฑ์ ๊ธฐ๋ฒ๋ค์ ์ด์ฉํด synthetic data์ ์ด์ ์ ์ด๋ฆฌ๊ณ  accuracy๋ฅผ ๋์ด์ฌ๋ฆด ์ ์๋๋ก ํ๋ค.
Real dataset๊ณผ Synthetic dataset๋ฅผ ์ด์ฉํด semantic segmentation์ ์ํํ๊ณ  Class ๋ณ IoU, mean IoU, pixel accuracy๋ฅผ ๋น๊ตํ์ฌ ์ ์ํ ๋ฐฉ๋ฒ์ผ๋ก ์ฑ๋ฅ์ด ์ข์์ง์ ํ์ธํ๋ค.

# Entripoy minimization

$$ entropy(p) = - \sum_{c=1}^C p_c \log p_c $$

## CutMix Segmentation

$$ r_x \sim Unif \{ 0, W \}, r_w = W \sqrt {1- \lambda}
\\
r_y \sim Unif \{ 0, H \}, r_h = H \sqrt {1- \lambda}
\\
B = \{ r_x , r_y, r_w, r_h \}
\\
$$

$$
\tilde {x} = M \odot x_s + (1-M) \odot x_t
\\
\tilde {y} = \lambda_{y_s} + (1- \lambda)y_t
$$

$$L_{seg} = -\sum_{h=1}^{H}\sum_{w=1}^{W}\sum_{c=1}^{C} y_s^i log(P(x_s^i)) - \lambda_{cm} \sum_{h=1}^{H}\sum_{w=1}^{W}\sum_{c=1}^{C} y_c^i log(P(x_c^i)) $$


$$ L_{adv} = \mathbb{E}[ log(D_p(P(x_s^i))) + log (1 - D_p(P(x_t^j))) ]
 $$

## Input-level Adaptation

๋จผ์  Input-level์์์ domain gap์ ์ค์ด๊ธฐ ์ํด source domain์ image appearance๊ฐ target domain๊ณผ ์ ์ฌํ๋๋ก ์์ฑํด๋ธ๋ค. ๋ํ์ ์ธ image-to-image translation ๋ฐฉ๋ฒ์ธ CycleGAN์ ์ด์ฉํ์ฌ target domain ์คํ์ผ์ ์ด๋ฏธ์ง๋ก ๋ณํ์์ผ domain adaptation ๋ชจ๋ธ์ source ๋ฐ์ดํฐ๋ก ์ฌ์ฉํ๊ณ ์ ํ๋ค.

$$ L(G, F, D_x, D_Y) = L_{GAN}(G, D_Y, X, Y) + L_{GAN}(F, D_x, Y, X) + \lambda L_{cyc}(G, F) $$

$$ G^*, F^* = arg min_{G, F} max_{D_x, D_y} L(G, F, D_x, D_y) $$

Adversiarl loss์ cycle consistency loss๋ฅผ ๊ฒฐํฉํ ๋ฐฉ๋ฒ์ผ๋ก, $X$๊ฐ source domain ์ํ, $Y$๊ฐ target domain ์ํ์ด๋ผ๊ณ  ํ์ ๋, $X \rightarrow Y$๋ก ๊ฐ๋ GAN์ adversarial loss์ $Y \rightarrow X$๋ก ๊ฐ๋ GAN์ adversarial loss์ ๊ฐ๊ฐ ์๋ณธ์ผ๋ก ๋ณต๊ตฌํ๋ cycle consistency loss๋ฅผ ๋ํด์ค ๊ฐ์ด total loss๊ฐ ๋๊ณ , ์ด loss๋ฅผ ์ต์ํ ํ๋ ๋ฐฉํฅ์ผ๋ก $G: X \rightarrow Y$์ $F: Y \rightarrow X$๋ฅผ ํ์ต์ํจ๋ค.

![](/assets/images/21-09-24-paper-style-transfer.png)
> GTA5(Source) ์ด๋ฏธ์ง๋ฅผ Cityscapes(Target) ์คํ์ผ๋ก image translation ํ ๊ฒฐ๊ณผ

Source domain๊ณผ target domain์ ๋ถํฌ๋ฅผ ๊ตฌ๋ถํ  ์ ์๋๋ก ํ์ต์ํค๋ ๋ฐฉ๋ฒ์ด๊ธฐ ๋๋ฌธ์ domain ๊ฐ gap์ด ์ค์์ ๊ฒ์ด๋ผ๊ณ  ๊ฐ์ ํ  ์ ์๊ณ , ํด๋น ๋ชจ๋ธ์ ํตํด ์์ฑ๋ ๋ฐ์ดํฐ๋ target domain ๋ถํฌ์ ์ข ๋ ๊ฐ๊น์ด ํํ๊ฐ ๋  ์ ์๋ค. Source ๋ฐ์ดํฐ๋ฅผ ๊ทธ๋๋ก ์ฌ์ฉํ๋ ๊ฒ ๋ณด๋ค Target domain์ ๋ง๊ฒ ์ด๋ฏธ์ง๋ฅผ ๋ณํํ์ฌ target task ๋ชจ๋ธ์ ์ฌ์ฉํ๋ฉด ์ฑ๋ฅ์ด ์ข์์ง๋ ๊ฒ์ ๋ณผ ์ ์์๋๋ฐ, ์ด์ ๊ดํ ์คํ ๊ฒฐ๊ณผ๋ 4.3์์ ํ์ธํ  ์ ์๋ค.

# 4. ์คํ ๋ฐ ๊ฒฐ๊ณผ

> UDA ๊ธฐ์ ์ ์ฃผ์ ์ธก๋ฉด์ ํ ๋ฐ์ดํฐ์ธํธ์์ ํ๋ํ ์ง์์ ๋ค๋ฅธ ์ปจํ์คํธ๋ก ์ด์ ํ๋ ๊ธฐ๋ฅ์๋๋ค. ๋ฐ๋ผ์ ๊ณ ๋ ค๋ ๋ฐ์ดํฐ๋ UDA ์๊ณ ๋ฆฌ์ฆ์ ์ค๊ณ ๋ฐ ํ๊ฐ์์ ๊ทผ๋ณธ์ ์ธ ์ญํ ์ ํฉ๋๋ค. ์ด ์น์์์๋ ๊ฐ์ฅ ํฅ๋ฏธ๋ก์ด ์ ํ๋ฆฌ์ผ์ด์ ์๋๋ฆฌ์ค ์ค ํ๋์ ์ด์ ์ ๋ง์ถฅ๋๋ค. 
> ๊ฐ๋๋์ง ์์ domain ์ ์ ์๋๋ฆฌ์ค์์ ๋์ domain์ ์๋ ์ค์  ์ํ์ ๊ฐ๋น์ผ ๋ ์ด๋ธ์ด ๊ต์ก์ ํ์ํ์ง ์๋ค๋ ์ ์ ๊ฐ์กฐํฉ๋๋ค. ๊ทธ๋ฌ๋ ์ ํ๋ ์์ ์ค์  ๋์ ์ํ์ ์๊ณ ๋ฆฌ์ฆ์ ์ฑ๋ฅ์ ํ์คํธ(๋๋ก๋ ๊ฒ์ฆ)ํ๊ธฐ ์ํด ์๋์ผ๋ก ๋ ์ด๋ธ์ ์ง์ ํด์ผ ํฉ๋๋ค. ๋ฐ๋ฉด์ ์์ค domain์ ํด๋นํ๋ ๋๊ท๋ชจ ํฉ์ฑ ๋ฐ์ดํฐ ์ธํธ์๋ ์ง๋ ํ์ต์ ํ์ฉ๋๋ ์ฃผ์์ด ์ฅ์ฐฉ๋์ด ์์ต๋๋ค.

## 4.1. ๋ฐ์ดํฐ์

![](/assets/images/21-09-24-paper-dataset.png)

Cityscapes๋ ์ ๋ฝ 50๊ฐ ๋์์ ๊ฑฐ๋ฆฌ์์ ์ฐจ๋ ์์ ์ผ๋ก ์บก์ณํ 2048x1024 ํฝ์์ ์ด๋ฏธ์ง ๋ฐ์ดํฐ์์ด๋ค. 2,975๊ฐ์ training set, 500๊ฐ์ validation set, 1,595๊ฐ์ test set์ผ๋ก ์ด 5,000๊ฐ์ ์ด๋ฏธ์ง ๋ฐ์ดํฐ์ pixel-wise semantic label๋ก ๊ตฌ์ฑ๋์ด ์๋ค. ์ถ๊ฐ๋ก label ๋์ด ์์ง ์์ 19,998 ๊ฐ์ raw ์ด๋ฏธ์ง ๋ฐ์ดํฐ๋ ์ ๊ณต๋๋ค.

GTA5๋ ๋์ ์ด์ ์ ์ํ ๋๊ท๋ชจ synthetic ๋ฐ์ดํฐ์ ์ค ํ๋์ด๋ค. ์ฌ์ค์ ์ธ ๊ทธ๋ํฝ์ ๋ณด์ฌ์ฃผ๋ GTA V ๊ฒ์์ ํตํด ๋ ๋๋ง ๋์๊ณ  ๋ฏธ๊ตญ ์คํ์ผ ๋์์์์ ์๋์ฐจ ๊ด์ ์์ ์ดฌ์๋์๋ค. 24,966๊ฐ์ 1914x1052 ํฝ์์ ์ด๋ฏธ์ง์ pixel-wise semantic label๋ก ๊ตฌ์ฑ๋์ด ์๋ค. Label์ Cityspcaes์ ํด๋์ค์ ๋ง๊ฒ 19๊ฐ ํด๋์ค๋ก ์ฌ๋งคํ ํ  ์ ์๋ค.

|  Dataset   |   Type    | # of Labeled Data | # of Raw Data | # of Classes |
| :--------: | :-------: | :---------------: | :-----------: | :----------: |
| Cityscapes |   Real    |       5,000       |    19,998     |      19      |
|    GTA     | Synthetic |      24,966       |       -       |      19      |

์คํ์์ ์์ ํ๋ Source ๋ฐ์ดํฐ๋ Synthetic ๋ฐ์ดํฐ์ธ GTA5์ด๊ณ , Target ๋ฐ์ดํฐ๋ ์ค์  ๋ฐ์ดํฐ์ธ Cityscapes๋ก ์ด๋ค. Cityscapes์ GTA5๋ ์๊ฐ์ ์ธ ์ฐจ์ด๊ฐ ์กด์ฌํ์ง๋ง ๋น์ทํ ์ฐจ๋์ ๋์ด์์ ๋๋ก ์ํฉ์ ์บก์ณํ ๋ฐ์ดํฐ๋ผ๋ ์ ์์ ๊ตฌ์กฐ๊ฐ ์ ์ฌํ๋ค๊ณ  ๋ณผ ์ ์๋ค. GTA5 ๋ฐ์ดํฐ๊ฐ Cityscapes ๋ฐ์ดํฐ ์์ ๋นํด ์ฝ 5๋ฐฐ ๊ฐ๋ ๋ง๊ธฐ ๋๋ฌธ์ GTA5์ ๋ฐ์ดํฐ๋ฅผ ์ต๋ํ ํ์ฉํ์ฌ ์ด๋ฏธ์ง์ ์ ๋ฐ์ ์ธ ์๋ฏธ์ ๊ตฌ์กฐ๋ฅผ ํ์ตํ๊ณ , Cityscapes์ unlabeled ๋ฐ์ดํฐ๊น์ง ์ ์ ํ ์ด์ฉํ์ฌ Cityscapes ํ๊ฒฝ์์ ์ ๋์ํ๋ Semantic segmentation ๋ชจ๋ธ์ ๊ตฌ์ฑํ์๋ค.

## 4.2. ํ๊ฐ Metric

ํ๊ฐ metric์ผ๋ก๋ Semantic segmentation ํ๊ฐ ์์ ์ฃผ๋ก ์ฌ์ฉ๋๋ ํด๋์ค ๋ณ IoU(Intersection over Union) ์ mIoU(Mean Intersection over Union)๋ฅผ ์ฌ์ฉํ๋ค. $TP_i$, $FP_I$, $FN_i$ ๋ ํน์  ํด๋์ค $i$์ True Positive, False Positive, False Negative ์ด๊ณ  $N$์ ํด๋์ค์ ๊ฐฏ์์ด๋ค.

$$ {IoU}_i = \left( \frac{TP_i}{FP_i + FN_i + TP_i} \right) $$

$$ mIoU = \sum_{i=1}^N \left( \frac{IoU_i}{N} \right ) $$

## 4.3. Input-level Adaptation

### 4.3.1. Semantic Segmentation

Source ์ด๋ฏธ์ง์์ Target ์ด๋ฏธ์ง ์คํ์ผ๋ก ์ฌ์์ฑ๋ ๋ฐ์ดํฐ๊ฐ domain gap์ ์ค์ด๋๋ฐ ์ผ๋ง๋ ๊ธฐ์ฌํ์๋์ง ํ์ธํ๊ธฐ ์ํ ์คํ์ด๋ค. ํด๋น ๋ชจ๋ธ์ Source domain ์ด๋ฏธ์ง์ Target ์ด๋ฏธ์ง ์คํ์ผ๋ก ์ฌ์์ฑ๋ ์ด๋ฏธ์ง(Source โ Domain)์ ์ ์ฉํ์ ๋ mIoU๋ฅผ ๋น๊ตํ๊ณ  ์ฑ๋ฅ์ด ๋์์ง์ ํ์ธํ๋ค.

![](/assets/images/21-09-24-paper-sem-seg.png)

์ฐ์  ์ผ๋ฐ์ ์ธ Semantic segmentation ๋ชจ๋ธ๊ณผ์ ๋น๊ต ๋์กฐ๊ตฐ์ ์ํด SOTA ๋ชจ๋ธ์ธ FCN๊ณผ DeepLab v3+ ์ฑ๋ฅ ๋น๊ต๋ฅผ ์ํํ์๋ค. Backbone์ ๋์ผํ๊ฒ Resnet-50์ผ๋ก ์ค์ ํ์๊ณ  Cityscapes์ Labeled ์ด๋ฏธ์ง๋ค๋ก Supervised-learning ๋ฐฉ์์ผ๋ก ํ์ต์์ผฐ๋ค.

|    model    | road | sidewalk | building | wall | fence | pole | traffic light | traffic sign | vegetation | terrain | sky  | person | rider | car  | truck | bus  | train | motorcycle | bicycle | Pixel Accuracy | mIoU  |
| :---------: | :--: | :------: | :------: | :--: | :---: | :--: | :-----------: | :----------: | :--------: | :-----: | :--: | :----: | :---: | :--: | :---: | :--: | :---: | :--------: | :-----: | :------------: | :---: |
|     FCN     | 0.97 |   0.81   |   0.9    | 0.26 | 0.45  | 0.54 |     0.67      |     0.75     |    0.91    |  0.53   | 0.93 |  0.78  | 0.57  | 0.93 | 0.48  | 0.7  | 0.36  |    0.56    |  0.75   |     94.693     | 68.03 |
| DeepLab v3+ | 0.98 |   0.84   |   0.92   | 0.56 | 0.59  | 0.64 |      0.7      |     0.78     |    0.92    |  0.64   | 0.95 |  0.81  | 0.63  | 0.94 | 0.69  | 0.76 | 0.43  |    0.63    |  0.76   |     95.826     | 74.97 |

DeepLab v3+๊ฐ ๋ ์ข์ ์ฑ๋ฅ์ ๋ณด์ด๊ธฐ๋ ํ์๊ณ , ๋ฒ์ ์ ๋ฐ๋ผ ์ฌ๋ฌ๊ฐ์ง ๋ณํ ๋ชจ๋ธ์ด ๋ง์ด ๋์ค๊ณ  ์๊ธฐ ๋๋ฌธ์ ์์ผ๋ก์ ์คํ์์๋ DeepLab ๊ธฐ๋ฐ์ ๋ชจ๋ธ์ Semantic segmentation task ๋ชจ๋ธ๋ก ์ฌ์ฉํ๋ค.

### 4.3.2. Image-to-image translation

์ด๋ฒ ์คํ์ ์ฌ์ฉํ ๋ชจ๋ธ์ Resnet-50์ backbone์ผ๋ก ํ DeepLab v3+ ๋ชจ๋ธ๋ก target domain ์ํ๋ก๋ง ํ์ต๋์๊ณ , Source domain๊ณผ Source โ Target domain ํ์คํธ์ ์ฌ์ฉํ ๋ฐ์ดํฐ์ ๊ฐฏ์๋ 24,966๋ก ๋์ผํ๊ฒ ์ค์ ํ์ฌ ์คํํ๋ค.

![](/assets/images/21-09-24-paper-style-transfer-prediction.png)

์ ์ด๋ฏธ์ง๋ Labeled ๋ฐ์ดํฐ ์์ด Cityscapes ์ด๋ฏธ์ง์ GTA ์ด๋ฏธ์ง๋ง ์ด์ฉํ์ฌ CycleGAN์ ํ์ตํ ๊ฒฐ๊ณผ๋ฌผ์ด๋ค. 3์ด์ GTA โ Cityscapes๋ GTA ์ด๋ฏธ์ง๋ฅผ ์๋ ฅ ๋ฐ์ดํฐ๋ก ํ์ฌ Cityscapes ์คํ์ผ๋ก ์ฌ์์ฑํ ์ด๋ฏธ์ง ๊ฒฐ๊ณผ๋ฌผ์ด๋ค.
GTA5 ๋ฐ์ดํฐ๋ฅผ ์์ธกํ ๊ฒฐ๊ณผ๋ ์๊ฐ์ ์ผ๋ก ๋ดค์ ๋ ๋ง์ ๋ธ์ด์ฆ๊ฐ ์์ฌ์๋ ๊ฒ์ ๋ณผ ์ ์๋๋ฐ, Cityscapes์ GTA5์ ๋ฐ์ดํฐ ์์ฒด์ domain gap์ ์ํด Cityscapes ๋ฐ์ดํฐ์ ์ ์ฉํ์ ๋ ๋ณด๋ค ๋ ๋ง์ ์์ธก ์ค์ฐจ๊ฐ ๋ฐ์ํ๋ ๊ฒ์ ๋ณผ ์๊ฐ ์๋ค. ๋ฐ๋ฉด์ GTA5 โ Cityscapes ๋ฐ์ดํฐ๋ฅผ ์์ธกํ ๊ฒฐ๊ณผ๋ GTA5 ๋ฐ์ดํฐ์ ๋นํด ์ ์ ์ค์ฐจ๋ฅผ ๋ณด์ด๊ณ  ์๋ค.

|                         | Pixel Accuracy | mIoU  |
| :---------------------: | :------------: | :---: |
|      Source Domain      |     72.10      | 30.18 |
| Source โ Target Domain  |     79.07      | 36.26 |
|      Target Domain      |     95.82      | 74.97 |

GTA5 ์ด๋ฏธ์ง๋ฅผ ๊ทธ๋๋ก ์ฌ์ฉํ๋ ๊ฒ์ ๋นํด generative ๋ชจ๋ธ์ ํตํด target domain ์คํ์ผ๋ก ์์ฑ๋ ๋ฐ์ดํฐ๋ฅผ ์ด์ฉํ์ ๋ mIoU๊ฐ +6% ์ฆ๊ฐํ์๊ณ , target domain๊ณผ์ gap์ด ์ค์ด๋  ๊ฒ์ ์ ์ ์๋ค.

## 4.3. Data dependency ์คํ

- ์ ์ target ๋ฐ์ดํฐ์์ผ๋ก ์ ํ๋ จ๋  ์ ์๋์ง ํ์ธํ๊ธฐ ์ํด labeled ๋ฐ์ดํฐ๋ฅผ ์ค์ฌ๊ฐ๋ฉฐ ์คํ
- ์์ ๋ฐ์ดํฐ์์ผ๋ก ์ ํ๋ จ๋  ์ ์๋์ง ์ฌ๋ถ
- SSL ์ฑ๋ฅ์ ํ๊ฐํ๊ธฐ ์ํด ๋ณดํต Labled ๋ฐ์ดํฐ๋ฅผ ์ ๊ฒ ์ฌ์ฉํ๊ณ  Unlabeld ๋ฐ์ดํฐ๋ฅผ ๋ง์ด ์ฌ์ฉํด ํ์คํธํ๋ค.

![](/assets/images/21-09-24-paper-data-dependency.png)

![](/assets/images/21-09-24-paper-citys-seg.png){:width="300px"}
> cityscapes labeld ๋ฐ์ดํฐ ์ค์ฌ๊ฐ๋ฉฐ ์คํ

```sh
# cityscapes epoch 20000
# 2975: 68.03 (epoch: 20,) / 69.12 (epoch: 30,)
# 2000: 56.68 (epoch: 20,)
# 1000: 58.87 (epoch: 20,)
# 500: 55.35 (epoch: 20,)
# 100: 40.5 (epoch: 20,)
```

```sh
# ์ ์ ๋ชจ๋ธ
# 2975: 48.39 (epoch: 20,) / 56.39 (epoch 40,)
# 1000: 48.1 (epoch 20,)
# 500: 49.82 (epoch 20,) / 51.6 (epoch 30,)
# 100: 45.97 (epoch 20,) / 49.19 (epoch 40,)
```

![](/assets/images/21-09-24-paper-regularization-train.png)
- training loss

![](/assets/images/21-09-24-paper-regularization.png)
- ํ๋์ ์ ์ cityscapes_d100
- ํ์์ ์ ์ ์ ๋ฐฉ๋ฒ์ผ๋ก citys ๋ฐ์ดํฐ 100๊ฐ๋ง ์ฌ์ฉ
- validation loss

## 4.5. Performance ์คํ

- ์๊ณ ๋ฆฌ์ฆ์ด ์ผ๋ง๋ ์ ์ํ๋๋์ง
- SOTA ๋ชจ๋ธ๊ณผ ๋น๊ต
- single origin ํ์ต ๊ฒฐ๊ณผ (ce + adversiarl loss)
- 

# 5. ๊ฒฐ๋ก 

# 6. ์ฐธ๊ณ  ๋ฌธํ

- [Cycle-GAN](Unpaired Image-to-Image Translation using Cycle-Consistent Adversarial Networks)

---

1. Abstract
2. Introduction
3. Related Work
4. Method
5. Experiments
6. Conclusions
7. References

---

# ๋งํฌ

- [๋ผ๋ฌธ์ฌ์ฌ์ผ์ ](https://eyonsei.yonsei.ac.kr/info.asp?mid=m03_10)
- [ํ์๋ผ๋ฌธ ์์ฑ๋ฒ](https://graduate.yonsei.ac.kr/graduate/academic/notice_haksa.do?mode=view&articleNo=28347&article.offset=0&articleLimit=10&srCategoryId1=363)

# Backup

![](/assets/images/21-09-24-paper-image-translation-experiments.png)

### Domain Adaptation 

```sh
# Source: GTA
# Target: Cityscapes
# domain adaptation ๋ชจ๋ธ์ ํตํด task ๋ชจ๋ธ์ mIoU ์ธก์ 
===>road:       83.99
===>sidewalk:   10.25
===>building:   74.36
===>wall:       4.47
===>fence:      3.08
===>pole:       10.36
===>light:      11.83
===>sign:       11.46
===>vegetation: 84.24
===>terrain:    15.83
===>sky:        68.71
===>person:     39.09
===>rider:      0.15
===>car:        79.1
===>truck:      26.16
===>bus:        17.3
===>train:      0.0
===>motocycle:  2.78
===>bicycle:    0.01
===> mIoU: 28.59
```

```sh
# Source: Cityscapes ์คํ์ผ์ GTA5 ๋ฐ์ดํฐ
# Target: Cityscapes
# domain adaptation ๋ชจ๋ธ์ ํตํด task ๋ชจ๋ธ์ mIoU ์ธก์ 
===>road:       89.43
===>sidewalk:   26.21
===>building:   82.72
===>wall:       25.88
===>fence:      22.92
===>pole:       36.15
===>light:      40.42
===>sign:       38.88
===>vegetation: 84.1
===>terrain:    37.55
===>sky:        83.52
===>person:     59.09
===>rider:      26.71
===>car:        83.65
===>truck:      28.55
===>bus:        36.64
===>train:      0.14
===>motocycle:  14.6
===>bicycle:    26.76
===> mIoU: 44.42
```
