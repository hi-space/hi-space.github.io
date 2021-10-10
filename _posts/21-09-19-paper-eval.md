---
title: 💡 Paper 실험
tags: paper 💡 🔥
---

<!--more-->

# deeplab v3+ (backbone: resnet101) 

## cityspcaes 데이터셋으로 학습된 deeplab v3+ (backbone: resnet101)

### cityscapes 데이터셋에 대해 test

```sh
python -u ./tools/eval.py \
    --config-file configs/cityscapes_deeplabv3_plus_resnet.yaml \
    --input-img /home/yoo/data/cityscapes/leftImg8bit/test/ \
    TEST.TEST_MODEL_PATH /home/yoo/workspace/SSL-Synthetic-Segmentation/seg/checkpoints/deeplabv3_plus_resnet101_segmentron.pth
```

```sh
2021-09-20 11:56:41,733 Segmentron INFO: End validation pixAcc: 96.043, mIoU: 78.271
2021-09-20 11:56:41,734 Segmentron INFO: Category iou: 
 +------------+---------------+----------+
|  class id  |  class name   |   iou    |
+============+===============+==========+
|     0      |     road      | 0.97999  |
+------------+---------------+----------+
|     1      |   sidewalk    | 0.839992 |
+------------+---------------+----------+
|     2      |   building    | 0.92879  |
+------------+---------------+----------+
|     3      |     wall      | 0.583538 |
+------------+---------------+----------+
|     4      |     fence     | 0.605864 |
+------------+---------------+----------+
|     5      |     pole      | 0.665058 |
+------------+---------------+----------+
|     6      | traffic light | 0.731235 |
+------------+---------------+----------+
|     7      | traffic sign  | 0.797456 |
+------------+---------------+----------+
|     8      |  vegetation   | 0.927313 |
+------------+---------------+----------+
|     9      |    terrain    | 0.642276 |
+------------+---------------+----------+
|     10     |      sky      | 0.952313 |
+------------+---------------+----------+
|     11     |    person     | 0.833831 |
+------------+---------------+----------+
|     12     |     rider     | 0.656327 |
+------------+---------------+----------+
|     13     |      car      | 0.949435 |
+------------+---------------+----------+
|     14     |     truck     | 0.712475 |
+------------+---------------+----------+
|     15     |      bus      | 0.860635 |
+------------+---------------+----------+
|     16     |     train     | 0.712133 |
+------------+---------------+----------+
|     17     |  motorcycle   | 0.700857 |
+------------+---------------+----------+
|     18     |    bicycle    | 0.79189  |
+------------+---------------+----------+
```

### gta5 데이터셋에 대해 test (TODO)

```sh
python -u ./tools/eval.py \
    --config-file configs/gta5_deeplabv3_plus_resnet.yaml \
    --input-img /home/yoo/data/cityscapes/leftImg8bit/test/ \
    TEST.TEST_MODEL_PATH /home/yoo/workspace/SSL-Synthetic-Segmentation/seg/checkpoints/deeplabv3_plus_resnet101_segmentron.pth
```

```sh

```

### cyclegta 데이터셋에 대해 test (TODO)

```sh
python -u ./tools/eval.py \
--config-file configs/gta_deeplabv3_plus_resnet.yaml \
TEST.TEST_MODEL_PATH /home/yoo/workspace/SSL-Synthetic-Segmentation/seg/checkpoints/deeplabv3_plus_resnet101_segmentron.pth
```

```sh

```

# AdaptSegNet

### multi_640x360_lsgan_b2 

- epoch: 120000
- input size: 512,256
- batch size: 2
- lsgan

```sh
===>road:       65.35
===>sidewalk:   17.71
===>building:   65.1
===>wall:       6.13
===>fence:      8.04
===>pole:       6.6
===>light:      0.98
===>sign:       0.19
===>vegetation: 75.62
===>terrain:    17.64
===>sky:        65.49
===>person:     9.4
===>rider:      0.04
===>car:        44.67
===>truck:      7.29
===>bus:        0.46
===>train:      0.0
===>motocycle:  1.13
===>bicycle:    0.0
===> mIoU: 20.62
```

### multi_640x360_lsgan_b1

- epoch: 150000
- input size: 640 x 360
- batch size: 1
- lsgan

```sh
===>road:       53.36
===>sidewalk:   5.41
===>building:   60.38
===>wall:       2.67
===>fence:      5.73
===>pole:       13.99
===>light:      1.6
===>sign:       0.09
===>vegetation: 55.12
===>terrain:    7.85
===>sky:        43.61
===>person:     6.45
===>rider:      0.3
===>car:        30.93
===>truck:      6.03
===>bus:        0.72
===>train:      0.0
===>motocycle:  0.61
===>bicycle:    0.0
===> mIoU: 15.52
```

# Style transfer 된 데이터 학습

- CycleGAN을 이용해 GTA -> Cityscapes 데이터를 사용해 DA
- generative model을 이용해 appearance를 비슷하게 style transfer 했을 때 domain의 gap이 적어진다
- GTA 데이터 / GTA 데이터 + cycleGTA 데이터 / all cycleGTA 데이터 3가지 테스트

### gc_640x360_b1

- epoch: 110000
- crop_size: 640x360
- batch size: 1
- gta + citys

```sh
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

### agc_640x360_b1

- epoch: 110000
- crop_size: 640x360
- batch size: 1
- gta + cyclegta + citys

```sh
# epoch: 40000
===>road:       91.34
===>sidewalk:   45.88
===>building:   80.35
===>wall:       28.38
===>fence:      23.42
===>pole:       33.59
===>light:      15.43
===>sign:       36.43
===>vegetation: 77.56
===>terrain:    32.91
===>sky:        64.97
===>person:     55.25
===>rider:      22.84
===>car:        79.1
===>truck:      19.43
===>bus:        27.26
===>train:      0.72
===>motocycle:  18.64
===>bicycle:    25.82
===> mIoU: 41.02

# epoch: 110000
===>road:       88.92
===>sidewalk:   37.42
===>building:   81.08
===>wall:       27.72
===>fence:      18.86
===>pole:       36.92
===>light:      32.2
===>sign:       42.05
===>vegetation: 83.82
===>terrain:    37.89
===>sky:        74.95
===>person:     58.1
===>rider:      25.46
===>car:        82.14
===>truck:      25.67
===>bus:        27.35
===>train:      0.0
===>motocycle:  21.13
===>bicycle:    38.66
===> mIoU: 44.23
```

### aagc_640x360_b2

- epoch: 40000
- crop_size: 640x360
- batch size: 2
- cyclegta + citys

```sh
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

# Augmentation

- source domain 데이터는 cyclegta로 고정
- 학습 데이터를 넣을 때 AutoAugmentation 했을 때 성능 비교

### aagc_640x360_b2_multiaug

- epoch: 20000
- crop_size: 640x360
- batch size: 2
- cyclegta + citys
- source augmentation + target augmentation

```sh
===>road:       85.94
===>sidewalk:   44.74
===>building:   74.49
===>wall:       22.06
===>fence:      23.57
===>pole:       35.94
===>light:      29.81
===>sign:       39.41
===>vegetation: 81.76
===>terrain:    34.36
===>sky:        80.23
===>person:     56.52
===>rider:      24.77
===>car:        76.53
===>truck:      7.43
===>bus:        22.8
===>train:      10.1
===>motocycle:  17.2
===>bicycle:    32.39
===> mIoU: 42.11
```

# Student (Pseudo Labeling한 데이터셋 추가)

- Unlabeled 데이터에 hard labeling 하고 해당 데이터를 포함하여 학습
  - Cityscapes 만으로 학습된 Deeplab v3+ 모델 사용 (mIoU: 78.271)
- 좋은 성능을 보였던 `aagc_640x360_b1/GTA5_best.pth`(mIoU: 44.42) 를 restore 해서 추가 학습 진행

### aagc_640x360_b2_student

- epoch: 20000
- crop_size: 640x360
- batch size: 2
- cyclegta + citys

```sh
===>road:       88.78
===>sidewalk:   44.98
===>building:   83.67
===>wall:       30.68
===>fence:      23.65
===>pole:       38.28
===>light:      40.38
===>sign:       42.62
===>vegetation: 84.86
===>terrain:    40.18
===>sky:        85.6
===>person:     57.97
===>rider:      20.89
===>car:        70.89
===>truck:      28.74
===>bus:        35.36
===>train:      6.36
===>motocycle:  28.98
===>bicycle:    29.1
===> mIoU: 46.42
```

### aagc_640x360_b2_student_autoaug

- epoch: 25000
- crop_size: 640x360
- batch size: 2
- cyclegta + citys
- source autoaug

```sh
===>road:       88.61
===>sidewalk:   36.18
===>building:   83.27
===>wall:       21.64
===>fence:      23.35
===>pole:       37.87
===>light:      34.18
===>sign:       44.43
===>vegetation: 83.93
===>terrain:    25.36
===>sky:        82.0
===>person:     58.68
===>rider:      29.58
===>car:        84.47
===>truck:      30.46
===>bus:        36.82
===>train:      0.22
===>motocycle:  20.42
===>bicycle:    36.56
===> mIoU: 45.16
```

### aagc_640x360_b2_student_full

- epoch: 40000
- crop_size: 640x360
- batch size: 2
- cyclegta + citys
- no autoaug
- restore 하지 않고 처음부터 Pseudo Labeling 된 데이터 섞어서 학습
- 참고로 15000 epoch이 val loss가 작은데 mIoU는 35.41
- epoch 15000: 35.41
- epoch 25000: 43.72
- epoch 50000: 43.02
- epoch 60000: 45.56
  
```sh
===>road:       90.53
===>sidewalk:   42.43
===>building:   83.15
===>wall:       26.88
===>fence:      23.44
===>pole:       37.85
===>light:      38.19
===>sign:       41.9
===>vegetation: 83.78
===>terrain:    36.41
===>sky:        78.28
===>person:     61.78
===>rider:      30.68
===>car:        84.63
===>truck:      31.27
===>bus:        33.59
===>train:      1.13
===>motocycle:  24.9
===>bicycle:    40.72
===> mIoU: 46.92
```

### aagc_640x360_b2_student_full_autoaug

- epoch: 40000
- crop_size: 640x360
- batch size: 2
- cyclegta + citys
- source aug + target aug
- restore 하지 않고 처음부터 Pseudo Labeling 된 데이터 섞어서 학습
- epoch 45000: 41.71

```sh
===>road:       87.46         
===>sidewalk:   44.75         
===>building:   81.59         
===>wall:       18.89         
===>fence:      23.54         
===>pole:       37.5         
===>light:      33.2         
===>sign:       30.66         
===>vegetation: 81.87         
===>terrain:    27.34         
===>sky:        78.5         
===>person:     58.04         
===>rider:      19.09         
===>car:        82.37         
===>truck:      15.82         
===>bus:        30.46         
===>train:      1.11          
===>motocycle:  21.17         
===>bicycle:    32.73         
===> mIoU: 42.43
```

# Target 데이터 수를 줄였을 때 DA 성능 테스트

### gc_640x360_b2_d1000

- epoch: 10000
- crop_size: 640x360
- batch size: 2
- gta + citys
- cityscapes 데이터셋을 1000개만 사용
- ?? 데이터셋 줄였을 때 오히려 성능이 좋아졌다?

```sh
===>road:       38.07
===>sidewalk:   24.37
===>building:   51.74
===>wall:       11.07
===>fence:      18.88
===>pole:       33.92
===>light:      30.17
===>sign:       36.62
===>vegetation: 79.42
===>terrain:    6.25
===>sky:        61.99
===>person:     58.76
===>rider:      24.87
===>car:        70.17
===>truck:      13.29
===>bus:        32.75
===>train:      1.09
===>motocycle:  23.69
===>bicycle:    32.23
===> mIoU: 34.18
```

### aagc_640x360_b2_d1000

- epoch: 20000
- crop_size: 640x360
- batch size: 2
- cyclegta + citys
- cityscapes 데이터셋을 1000개만 사용

```sh
===>road:       85.81
===>sidewalk:   43.68
===>building:   78.32
===>wall:       22.36
===>fence:      18.93
===>pole:       37.12
===>light:      32.0 
===>sign:       42.13
===>vegetation: 76.79
===>terrain:    38.22
===>sky:        63.99
===>person:     57.86
===>rider:      23.76
===>car:        78.22
===>truck:      7.97 
===>bus:        18.39
===>train:      1.94 
===>motocycle:  15.45
===>bicycle:    39.07
===> mIoU: 41.16 
```

### aagc_640x360_b2_d500

- epoch: 20000
- crop_size: 640x360
- batch size: 2
- cyclegta + citys
- cityscapes 데이터셋을 500개만 사용

```sh
===>road:       89.78
===>sidewalk:   45.64
===>building:   82.37
===>wall:       24.37
===>fence:      23.86
===>pole:       37.2
===>light:      34.28
===>sign:       41.7
===>vegetation: 84.31
===>terrain:    40.03
===>sky:        80.6
===>person:     55.21
===>rider:      23.53
===>car:        81.14
===>truck:      18.97
===>bus:        32.25
===>train:      3.72
===>motocycle:  19.32
===>bicycle:    39.36
===> mIoU: 45.14
```

# Segmentation 모델만 target 데이터셋 줄여가며 성능 테스트

- 기존 사용하던 모델에서 discriminative 모델들을 제거하고 segmentation 모델로만 학습 진행
- SL의 경우 데이터셋이 줄어듦에 따라 성능이 급격히 저하되는 것을 확인
- but, 데이터가 아주 적어도 DA 보다 성능이 살짝 안좋다

### cityscapes_seg 

- epoch: 20000 (참고로 epoch 30000에서는 mIoU 69.12)
- crop_size: 640x360
- batch_size: 2
- cityscapes only (2975)

```sh
===>road:       96.45
===>sidewalk:   72.46
===>building:   88.62
===>wall:       48.61
===>fence:      46.65
===>pole:       46.26
===>light:      54.82
===>sign:       65.16
===>vegetation: 88.13
===>terrain:    50.95
===>sky:        89.02
===>person:     73.23
===>rider:      54.42
===>car:        91.93
===>truck:      72.11
===>bus:        75.82
===>train:      60.83
===>motocycle:  49.47
===>bicycle:    67.58
===> mIoU: 68.03
```

### cityscapes_seg_d2000

- epoch: 20000
- crop_size: 640x360
- batch_size: 2
- cityscapes only (2000)
- epoch 40000: 62.65

```sh
===>road:       95.13
===>sidewalk:   66.19
===>building:   79.77
===>wall:       24.22
===>fence:      36.12
===>pole:       40.34
===>light:      47.0
===>sign:       57.51
===>vegetation: 84.3
===>terrain:    40.12
===>sky:        86.49
===>person:     63.95
===>rider:      42.66
===>car:        88.14
===>truck:      50.63
===>bus:        59.17
===>train:      30.0
===>motocycle:  32.06
===>bicycle:    53.03
===> mIoU: 56.68
```

### cityscapes_seg_d1000

- epoch: 20000
- crop_size: 640x360
- batch_size: 2
- cityscapes only (1000)
- epoch 40000: 62.65

```sh
===>road:       93.94
===>sidewalk:   63.72
===>building:   85.84
===>wall:       22.78
===>fence:      30.39
===>pole:       44.03
===>light:      44.06
===>sign:       63.5
===>vegetation: 87.25
===>terrain:    45.07
===>sky:        85.2
===>person:     67.04
===>rider:      47.62
===>car:        88.86
===>truck:      47.17
===>bus:        63.05
===>train:      34.67
===>motocycle:  41.64
===>bicycle:    62.65
===> mIoU: 58.87
```

### cityscapes_seg_d500

- epoch: 20000
- crop_size: 640x360
- batch_size: 2
- cityscapes only (500)
- epoch 40000: 62.65

```sh
===>road:       94.48
===>sidewalk:   63.33
===>building:   85.77
===>wall:       22.97
===>fence:      29.15
===>pole:       42.14
===>light:      34.21
===>sign:       59.65
===>vegetation: 86.16
===>terrain:    43.79
===>sky:        84.85
===>person:     65.82
===>rider:      43.31
===>car:        87.9
===>truck:      41.4
===>bus:        51.61
===>train:      18.1
===>motocycle:  33.0
===>bicycle:    64.02
===> mIoU: 55.35
```

### cityscapes_seg_d100

- epoch: 20000
- crop_size: 640x360
- batch_size: 2
- cityscapes only (100)

```sh
===>road:       92.62
===>sidewalk:   56.93
===>building:   79.11
===>wall:       5.75
===>fence:      7.55
===>pole:       35.35
===>light:      3.63
===>sign:       40.23
===>vegetation: 84.5
===>terrain:    21.24
===>sky:        83.01
===>person:     59.26
===>rider:      1.35
===>car:        86.35
===>truck:      0.0
===>bus:        42.24
===>train:      0.0
===>motocycle:  10.89
===>bicycle:    59.52
===> mIoU: 40.5
```

# Seg-Uncertanity -> Single Segmentation

## Basic

- epoch: 45000
- crop_size: 640x360
- batch_size: 4
- cyclegta + cityscapes
- DeepLabMulti -> 일반적인 DeepLab 형태로 (auxiliary segmentation model 제거)
- Total loss = segmentation loss + adversarial loss (prediction 결과물에 dicriminator 로 domain confusion)
- epoch 40000: 41.71

```sh
===>road:       76.63
===>sidewalk:   25.13
===>building:   81.14
===>wall:       25.93
===>fence:      19.86
===>pole:       31.92
===>light:      34.95
===>sign:       39.1
===>vegetation: 79.13
===>terrain:    29.87
===>sky:        73.6
===>person:     56.98
===>rider:      25.24
===>car:        81.79
===>truck:      27.61
===>bus:        27.72
===>train:      9.53
===>motocycle:  26.9
===>bicycle:    42.08
===> mIoU: 42.9
```

## Single + Cutmix

- epoch: 40000
- crop_size: 640x360
- batch_size: 4
- cyclegta + cityscapes
- epoch 30000: 49.16
- ?? `loss = loss_seg1 + self.lambda_seg * loss_seg2` 라고 써야 cutmix loss도 들어가는데 `loss = loss_seg1 + self.lambda_seg * loss_seg1` 이렇게 하고 돌렸다? 그런데 성능이 좋았다?

```sh
===>road:       93.32
===>sidewalk:   58.31
===>building:   81.48
===>wall:       40.99
===>fence:      34.11
===>pole:       33.84
===>light:      36.54
===>sign:       49.44
===>vegetation: 84.32
===>terrain:    46.8
===>sky:        83.16
===>person:     64.4
===>rider:      42.62
===>car:        87.52
===>truck:      50.54
===>bus:        58.01
===>train:      31.75
===>motocycle:  37.83
===>bicycle:    52.8
===> mIoU: 56.2
```

## Single + Cutmix real 🔥 ⭐️

- epoch: 40000
- crop_size: 640x360
- batch_size: 4
- cyclegta + cityscapes
- `loss = loss_seg1 + self.lambda_seg * loss_seg2` 로 수정 

```sh
# epoch: 40000
===>road:       92.43
===>sidewalk:   54.89
===>building:   82.12
===>wall:       39.52
===>fence:      33.87
===>pole:       34.49
===>light:      34.27
===>sign:       46.34
===>vegetation: 83.99
===>terrain:    45.93
===>sky:        84.47
===>person:     65.3
===>rider:      40.97
===>car:        88.18
===>truck:      50.24
===>bus:        57.5
===>train:      38.0
===>motocycle:  44.39
===>bicycle:    54.53
===> mIoU: 56.39

# epoch: 20000
===>road:       86.9
===>sidewalk:   44.31
===>building:   76.73
===>wall:       22.51
===>fence:      24.01
===>pole:       30.15
===>light:      33.86
===>sign:       45.68
===>vegetation: 82.93
===>terrain:    40.57
===>sky:        81.17
===>person:     60.33
===>rider:      36.59
===>car:        81.39
===>truck:      22.01
===>bus:        40.91
===>train:      26.94
===>motocycle:  30.49
===>bicycle:    52.01
===> mIoU: 48.39
```

## Single + FixMatach

- epoch: 30000
- crop_size: 640x360
- batch_size: 4
- cyclegta + cityscapes

```sh
===>road:       65.91
===>sidewalk:   10.96
===>building:   36.6
===>wall:       9.87
===>fence:      15.78
===>pole:       23.15
===>light:      25.58
===>sign:       16.91
===>vegetation: 4.13
===>terrain:    28.15
===>sky:        16.59
===>person:     31.05
===>rider:      13.15
===>car:        27.33
===>truck:      9.77
===>bus:        7.84
===>train:      0.01
===>motocycle:  22.81
===>bicycle:    19.21
===> mIoU: 20.25
```

---

# Pseudo Label

- train 데이터에 대해 pseudo labeling 한 후 GT 데이터와 mIoU 비교

### default pseudo labeling

- single_cutmix_real로 pseudo labeling evaluation
  
```sh
===>road:       92.14
===>sidewalk:   57.04
===>building:   76.98
===>wall:       34.39
===>fence:      30.71
===>pole:       21.46
===>light:      24.55
===>sign:       28.98
===>vegetation: 78.14
===>terrain:    53.26
===>sky:        84.79
===>person:     54.98
===>rider:      26.94
===>car:        83.78
===>truck:      48.48
===>bus:        54.27
===>train:      36.96
===>motocycle:  38.19
===>bicycle:    34.73
===> mIoU: 50.57
```

# Pseudo Labeled 데이터 training

- `Single + Cutmix real` (mIou: 56.39) 모델을 restore 한 후 pseudo labeling 한 cityscapes 데이터로 fine-tuning
- `aagc_640x360_b2_single_cutmix_student`

### cityscapes GT 데이터로 pseudo labeling한 데이터

```sh
# python eval_single.py \
#   --restore-from ./snapshots/aagc_640x360_b2_single_cutmix_student/CITYS_10000.pth
===>road:       96.13
===>sidewalk:   72.72
===>building:   88.12
===>wall:       44.52
===>fence:      45.92
===>pole:       42.66
===>light:      50.69
===>sign:       62.97
===>vegetation: 88.65
===>terrain:    55.41
===>sky:        88.4
===>person:     70.26
===>rider:      50.67
===>car:        90.81
===>truck:      73.5
===>bus:        70.26
===>train:      48.25
===>motocycle:  53.44
===>bicycle:    67.64
===> mIoU: 66.37
```

### single cutmix real 데이터로 pseudo labeling 한 데이터

- `aagc_640x360_b2_single_cutmix_real/GTA5_40000.pth` 로 pseudo labeling 한 데이터 사용
- train 데이터셋에 pseudo labeling 했을 때 mIoU가 50.57

```sh
ㅠㅠ  mIoU 1정도 나오려나 완전망
```

### single cutmix real 데이터에서 confidenc(> 0.5) 데이터만 pseudo labeling

- `output_batch[score_batch<0.5] = 255`
- 

# Fine-tuning

- `aagc_640x360_b2_single_cutmix_real/GTA5_40000.pth` restore (mIoU: 56.39)
- cutmix augmentation 없이 GTA->Cityscapes adaptation 수행
- 당연한 말이지만 GTA5 도메인 knowledge가 들어가다보니 더 안좋아짐

```sh
# epoch: 10000
===>road:       81.66
===>sidewalk:   33.48
===>building:   81.06
===>wall:       22.77
===>fence:      25.27
===>pole:       34.12
===>light:      33.37
===>sign:       47.24
===>vegetation: 78.56
===>terrain:    33.18
===>sky:        71.27
===>person:     58.23
===>rider:      34.19
===>car:        85.59
===>truck:      44.36
===>bus:        40.0
===>train:      21.35
===>motocycle:  34.31
===>bicycle:    58.29
===> mIoU: 48.33

# epoch: 5000
===>road:       86.1
===>sidewalk:   41.23
===>building:   82.22
===>wall:       36.04
===>fence:      33.22
===>pole:       33.04
===>light:      34.9
===>sign:       52.01
===>vegetation: 82.35
===>terrain:    39.65
===>sky:        78.72
===>person:     61.45
===>rider:      40.96
===>car:        85.47
===>truck:      31.27
===>bus:        46.43
===>train:      33.82
===>motocycle:  33.28
===>bicycle:    60.09
===> mIoU: 52.22
```

# 데이터셋 줄여가며

### `Single + Cutmix real`에서 cityscapes 데이터셋 줄여가며 테스트

```sh
# citys 데이터: 1000 / epoch: 20000
===>road:       89.8
===>sidewalk:   47.83
===>building:   78.08
===>wall:       29.4
===>fence:      26.01
===>pole:       30.81
===>light:      31.92
===>sign:       44.57
===>vegetation: 81.86
===>terrain:    41.38
===>sky:        78.27
===>person:     61.61
===>rider:      34.83
===>car:        85.12
===>truck:      27.93
===>bus:        25.23
===>train:      15.34
===>motocycle:  30.78
===>bicycle:    53.05
===> mIoU: 48.1
```

```sh
# citys 데이터: 500 / epoch: 20000
===>road:       90.45
===>sidewalk:   51.56
===>building:   80.4
===>wall:       24.29
===>fence:      25.14
===>pole:       35.54
===>light:      33.18
===>sign:       48.83
===>vegetation: 83.67
===>terrain:    42.52
===>sky:        76.34
===>person:     58.22
===>rider:      34.15
===>car:        83.41
===>truck:      34.51
===>bus:        31.2
===>train:      24.78
===>motocycle:  32.29
===>bicycle:    56.12
===> mIoU: 49.82

# citys 데이터: 500 / epoch: 30000
===>road:       92.46
===>sidewalk:   53.44
===>building:   78.69
===>wall:       30.76
===>fence:      27.1
===>pole:       31.71
===>light:      29.52
===>sign:       49.72
===>vegetation: 83.57
===>terrain:    44.49
===>sky:        79.31
===>person:     61.18
===>rider:      36.39
===>car:        86.28
===>truck:      36.23
===>bus:        44.9
===>train:      34.16
===>motocycle:  32.62
===>bicycle:    47.8
===> mIoU: 51.6
```

```sh
# aagc_640x360_b2_single_cutmix_real_d100
# citys 데이터: 100 / epoch: 20000
===>road:       87.59
===>sidewalk:   46.34
===>building:   77.33
===>wall:       24.46
===>fence:      22.95
===>pole:       29.49
===>light:      30.65
===>sign:       44.55
===>vegetation: 82.1
===>terrain:    41.03
===>sky:        79.09
===>person:     61.43
===>rider:      33.66
===>car:        80.4
===>truck:      15.16
===>bus:        38.37
===>train:      0.01
===>motocycle:  23.69
===>bicycle:    55.05
===> mIoU: 45.97
```