---
title: Faster RCNN
Category: AI
tags: ai paper
---
# Faster R-CNN

Fast R-CNN에서 training/test pipeline을 통합함으로써 속도와 정확도를 올렸지만, 여전히 real-time으로 가기에는 속도가 많이 느렸다. Faster R-CNN에서는 Fast R-CNN에서 가장 큰 계산 부하를 차지하는 region proposal 방법을 개선했다. Selective Search 알고리즘을 CNN로 대체하게 되는 데, 이를 Region Proposal Network(RPN) 이라고 한다. 이 논문의 가장 핵심적인 부분이다.

![](/assets/images/20-04-20-faster-rcnn-2021-11-02-11-14-15.png)

기존에 Selective search로 진행했었던 region proposal 단계를 RPN으로 대체하고, 그렇게 뽑아낸 region proposal들을 각각 Fast R-CNN에 넣어준다. 그 이후의 과정은 Fast R-CNN 의 과정과 같다고 보면 된다. 그래서 Faster R-CNN을 두개의 모듈로 보고 있다.  
  - RPN (Region Proposals) : Deep conv layer를 거치며 뽑아낸 region proposal sets  
  - Fast-RCNN의 Roi Pooling Layer : 각 region proposal 분류 및 region proposal 위치 refine  
RPN과 Detection Network는 convolutional feature map (Shared CNN)을 공유하여 사용한다.  
  
전체적인 순서를 서술하자면,  
 1. Pre-trained 된 CNN 모델을 가져온다.  
 2. 위의 CNN 모델의 마지막 convolution layer로부터 feature map을 뽑아낸다.  
 3. object 인지 아닌지 판별하고, bounding box의 위치를 제안하는 Region Proposal Network를 훈련시킨다.  
 4. 제안된 region 들을 ROI pooling layer에 넘긴다.  
 5. bounding box의 위치를 재조정하고, fully connected layer로 넘겨 classification 한다.

<!--more-->

통합해서 전체의 그림을 보면 다음과 같다.   
- Region Proposal Network -> Object 인지, 아닌지
- Bounding box의 좌표값 -> region proposal 
- RoI Pooling -> 어떤 class의 Object 인지
- Bounding box의 좌표를 Object에 맞춰 재조정
  
### Resion Proposal Networks  

Image를 입력받아 사각형 형태의 Object Proposal과 Objectness Score를 출력한다. 이 네트워크는 Fully Convolutional network 형태로, Fast-RCNN와 convolutional layer를 공유하게 디자인되어 있다.

![](/assets/images/20-04-20-faster-rcnn-2021-11-02-11-14-31.png)

### Anchor box

Anchor box는 sliding window의 각 위치에서 bounding box의 후보로 사용되는 상자이다. (pre-defined reference box)  
동일한 크기의 sliding window(3x3x512)를 이동시키며 window의 위치를 중심으로 k개의 anchor box들을 적용하여 feature를 추출한다. 논문에서는 1:1, 1:2, 2:1, 2:2 등 3가지의 크기와 3가지 비율의 총 9가지의  anchor box를 사용한다.

![](/assets/images/20-04-20-faster-rcnn-2021-11-02-11-14-37.png)

image의 크기를 조정할 필요가 없고, filter의 크기를 변경할 필요도 없기 때문에 계산효율이 높은 방식이다.  
  
RPN을 training 할 때, 각 anchor 마다 obejct 인지 아닌지에 대해서 binary class label을 한다.  
 - positive label :  가장 높은 IoU(Intersection over Union)를 가지고 있는 anchor  
                             IoU > 0.7을 만족하는 anchor  
  - non-positive label : IoU < 0.3 인 anchor  
다음과 같은 기준으로 IoU ratio를 통해 positive sample과 negative sample을 나눈다.  
이렇게 labeling 한 정보를 바탕으로 training시킬 때 한 이미지 당 sample anchor를 (positive anchor : negative anchor = 1:1) 비율로 섞는다. positive anchor 갯수가 적을 경우는 negative sample로 채운다. (negative sample이 positive sample보다 훨씬 많기 때문)  
  
1. 초기 CNN의 마지막 레이어에 3x3 sliding window를 feature map에 적용한다.  
2. CNN을 통과한 feature map에서 각 sliding window 위치마다 k 개의 Anchor box를 이용해으로 여러 개의 가능한 region을 생성  
   - 각 anchor 마다 가능한 bounding box 좌표와 이 bounding box의 score를 계산  
3. 제안된 각 region 마다 objectness score와 region의 bounding box 의 좌표가 출력 된다.  
  
classification layer의 2k scores는 k bounding boxes의 softmax 확률을 나타낸다.  
  - 여기서 filter의 갯수는 (anchor box의 갯수 9개) * (score의 갯수 2개 : obect? / non-object?)로 결정된다.  

regression layer에서 나오는 4k coordinates는 bounding box의 좌표가 되는데, 이는 object인지 아닌지 분류할 수가 없기 때문에 anchor boxes의 objectness score가 특정 임계값 보다 높으면 해당 상자의 좌표가 region proposal로 전달 된다.  
  - 여기서 filter의 갯수는 (anchor box의 갯수 9개) * (각 box 좌표 4개 : x, y, w, h)로 결정된다.  
  
Faster R-CNN을 훈련시키기 위해서는 4가지의 loss가 필요하다.  
 - RPN Binary Classification (Object or not obejct)  
 - RPN Bounding box proposal  
 - Fast RCNN Classification (Normal object classification)  
 - Fast RCNN Bounding box regression (Improve previous Bounding box Proposal)
  
그 중에서 RPN의 Loss만 보면,

![](/assets/images/20-04-20-faster-rcnn-rpn-loss.png)

- $\pi$ : Predicted probability of anchor  
- $\pi^*$ : Ground-truth label (1 : anchor is positive, 0 : anchor is negative)  
- $\lambda$ : Balancing parameter. Ncls, Nreg 차이로 발생하는 불균형을 방지하기 위한 hyper-parameter.  
  - ex) cls의 mini-batch 크기가 256 이고(=Ncls), 이미지 내부에서 사용된 모든 anchor의 location이 약 2,400 이라 하면(Nreg), lambda 값은 10 정도로 설정.
- $t_i$ : Predicted Bounding box  
- $t_i^*$ : Ground-truth box

![](/assets/images/20-04-20-faster-rcnn-bbox-loss.png)

 Bounding box regression을 계산하는 Lreg에서는 4개의 coordinate\(x, y, w, h\)에 대해 특정 연산을 취하고, Smooth L1 loss function을 통해 Loss를 계산한다.  
R-CNN, Fast R-CNN에서는 모든 RoI가 그 크기와 비율에 상관없이 weight를 공유했었지만, 여기에선 k개의 anchor에 상응하는 k개의 regressor를 갖게 된다.

## Overall Network Architecture

![](/assets/images/20-04-20-faster-rcnn-2021-11-02-11-15-20.png)

end-to-end로 back-propagation과  SGD를 사용하여 training 한다.

## RCNN vs Faster RCNN

![](/assets/images/20-04-20-faster-rcnn-2021-11-02-11-15-30.png)

기존의 R-CNN 과의 결과 비교.  5 fps 처리가 가능하기 때문에 near real-time 이라고 한다.  
이와 같은 2-stage detection 구조는 pipeline이 복잡하고, 느리고, 각 component를 optimize 하기 어렵기 때문에 unified detection이 연구되는 추세이다. ex) Yolo, SSD 등

