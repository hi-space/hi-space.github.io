---
title: Fast RCNN
Category: AI
tags: ai paper
---

# Fast RCNN

![](/assets/images/20-04-18-fast-rcnn-fast-rcnn.png)

R-CNN의 문제점은 모든 Region proposal 마다 CNN을 돌리고, 분류를 위한 SVM, BBox를 위한 linear regression까지 세가지 모델을 모두 훈련시키기 어렵고 시간이 오래 걸린다. 그래서 Fast RCNN에서는 feature extractor, classifier, bbox regressor를 하나의 파이프라인으로 처리한다. 한마디로, 전체 network가 End-to-end로 한번에 학습된다.  
  
Fast RCNN은 Selective Search로 RoI 영역을 뽑아내고 wrapping 하여 CNN에 넣는 대신, 이미지 전체를 CNN에 넣어서 RoI Pooling 하고 Classification과 BBox Regression을 동시에 거친다.  
    
\(1\) Input image를 CNN \(several conv + max pooling\)에 넣어서 feature map 추출  
\(2\) RoI Pooling Layer 에서 RoI를 인식하고, 동일한 크기의 feature vector를 추출하여 Fully Connected Layer 와 연결  
\(3\) Softmax classifier 와 Bounding box Regressor에 의해 결과 도출

<!--more-->

![](/assets/images/20-04-18-fast-rcnn-2021-11-02-11-11-37.png)

CNN에 넣기 위해 region proposal 마다 cropping 해서 처리했었는데, 이를 image level이 아닌 feature map level에서 진행함으로써 num of region proposal 의 CNN 연산이 1번의 CNN 연산으로 대폭 줄어든다. region proposal을 하기 전에 이미지에서 feature를 추출하기 때문에 중복 regions에 대해 여러번 CNN 작업을 할 필요가 없다.

![](/assets/images/20-04-18-fast-rcnn-2021-11-02-11-11-43.png)

![](/assets/images/20-04-18-fast-rcnn-2021-11-02-11-11-50.png)

 \(1\) Input image 로부터 다수의 Region proposal 추출 \(Selective search 알고리즘 사용\)  
\(2\) CNN의 입력으로 input image와 region proposal 를 넣어준다. 몇번의 conv 및 max pooling을 통해 conv featre map 생성  
\(3\) 각각의 region proposal를 Roi pooling layer를 통해 feature map으로부터 fixed-length feature vector를 추출  
    - 각각의 RoI는 4개의 tuple\(r, c, h, w\)로 구성. top-left corner 좌표, height, width.  
\(4\) 각 feature vector는 fully connected layer로 들어가서 층으로 들어가서 두개의 출력층으로 나온다.  
    - K개의 Object 클래스로 구별되는\(background 포함\) Softmax 확률 계산  
    - 각각의 Object에 대한 BBox 좌표  
        -&gt; 두 함수의 loss를 더한 multi-task loss를 기반으로 동시에 두가지 task를 학습 \(Log loss + smooth L1 loss\)  
        -&gt; 한 image에 대한 RoI들은 computation, memory를 공유

### RoI Pooling Layer

 - Region proposal을 동일한 사이즈의 벡터로 변환하기 위함.  
 - RoI 내의 feature를 H x W 크기의 작은 feature map으로 변환하기 위해 max pooling 한다. \(이 때 H, W는 hyper parameter\)  
 - feature map 상의 특정 영역에 대해 일정한 고정된 갯수의 bin으로 영역을 나눈 뒤, bin 에 대해 max pooling을 취함으로써 고정된 길이의 feature vector 추출 \(SPP-Net의 Spatial Pyramid Pooling 기법 사용\)  
- Selective search에서 찾은 region proposal 정보를 CNN을 통과하면서 유지시키고, 최종 CNN feature map 으로 부터 해당 영역을 추출하여 pooling 한다.  
 - Fast R-CNN의 핵심 아이디어는 RoI pooling. 어떤 사이즈의 RoI가 나오더라도 항상 일정한 크기의 RoI가 만들어지도록 stride를 잘 조정하여 max pooling을 한다.  
  
RoI Pooling 한 결과를 가지고 Classification과 Bounding box Regression을 수행한다.  
Total Loss = Classification Loss \(cross entropy\) + BBox Regression Loss \(smooth L1\)

![](/assets/images/20-04-18-fast-rcnn-smooth.png)

### Initializing from pre-trained networks

 - 마지막 max pooling layer -&gt; 네트워크의 첫번째 FC layer에 맞춰서 H, W를 세팅하고 RoI pooling layer로 대체  
 - 네트워크의 마지막 FC layer와 softmax -&gt; K+1 카테고리 classifier와 bbox regressor로 대체  
 - 네트워크가 두개의 입력\(이미지 리스트, 이미지에 있는 RoI 리스트\)을 받도록 수정

### Fine Tuning

 - Multi-task loss  
 - Mini-batch sampling  
 - backpropagation through RoI Pooling Layer  
 - SGD hyper-parameters  
  
전체 네트워크 레이어에 대해 training이 가능하다. 이는 R-CNN과 SPP-Net에서는 안됐던 것. 왜일까?

![](/assets/images/20-04-18-fast-rcnn-2021-11-02-11-12-18.png)

![](/assets/images/20-04-18-fast-rcnn-2021-11-02-11-12-25.png)