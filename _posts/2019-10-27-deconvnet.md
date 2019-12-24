---
layout: post
title:  "Visualizing and Understanding Convolutional Networks Korean Version(한국어버전)"
date:   2019-10-27 21:09:00 +0800
categories: Paper
tags: XAI
image: images/default.jpg
---

본 논문은 2013년에 쓰여졌으며 당시 ImageNet 2012에서 Krizhevsky가 CNN을 이용한 AlexNet이라는 모델로 엄청난 성능을 낸 것을 보고 **어떻게 그런 성능을 낼 수 있었는지(Why they perform so well)**와 **어떻게 개선할 수 있을지(how they might be improved)**를 연구한 논문이다.

**Authors**: Matthew D Zeiler, Rob Fergus  
**Conference**: ECCV  
**Paper**: [Visualizing and Understanding Convolutional Networks](https://arxiv.org/abs/1311.2901)  
**Year**: 2014   

# 역자의 말

영어를 한글로 변역하면서 변역된 단어가 적절하지 못한 경우 영어를 그대로 적용하였습니다. 부족한 부분이 있을 수 있으니 언제라도 잘못된 정보에 대해서는 comment나 메일을 통해서 지적 부탁드리겠습니다.

wogur379@gmail.com 

# Introduction

Alex krizhevsky의 CNN을 이용한 AlexNet이 ImageNet 2012에서 에러율 16.4%로 2등인 26.1%보다 월등히 높은 결과로 state of the art (SOTA)가 되었다. 

이런 좋은 결과를 낼 수 있는것에 3가지 요소로서 첫 번째로는 무수히 많은 데이터와 정답을 사용했고 두 번째로는 GPU 장비 덕분에 큰 모델을 학습 할 수 있었고 마지막으로는 더 좋은 정규화 방법들 덕분이였다.

그러나 문제는 이 모델들이 내부에서 어떤 작용을 하는지나 어떻게 모델이 좋은 결과를 낼 수 있었는지에 대한 인사이트가 거의 없었다.

이런 문제점을 해결하고자 본 논문에서는 모델 내부에서 **어떤 일이 벌어지는지 알기위한 시각화 방법**을 제안한다. 시각화는 [multi-layered Deconvolutional Network (deconvnet)](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.849.3679&rep=rep1&type=pdf)을 사용한다. 

그리고 모델의 개선하여 사용할 수 있는 방법을 확인하고자 AlexNet과 비슷한 모델을 만들어 ImageNet에 대해서 결과를 비교해보고 모델의 일반화를 검증해보기위해 **사전 학습된 모델(pre-trained model**)을 통해 마지막 softmax부분만 재학습시킨 후 ImageNet이 아닌 다른 데이터셋에도 적용시켜 보았다. 

# Related work

**Visualization**

지금까지 모델에 대한 정보를 얻기위해 시각화를 하는건 일반적인 일이였지만, 문제는 첫 번째 레이어에서만 어느 픽셀에 정보가 투영 되었는지 알 수 있다는 한계가 있었다. 

더 높은 레이어에는 다른 방법이 적용되어야한다. 그러나 문제는 invariances가 너무 복잡하여 단순한 이차 근사치(a simple quadratic approximation)밖에 나타낼 수 없다는 것이다. 반면 본 논문에서 제안하는 방식은 invariance의 비모수적인(non-parametric) 방식을 통해 어떤 패턴이 학습데이터로부터 특성치(feature map)을 활성화 했는지 보여준다.

본 논문의 접근 방식은 어떻게 Fully Connect Layer (FCN)으로부터 뒤로 투영해가면서 convnet 에서 saliency maps을 얻을 수 있는지에 대해 연구한 [Karen Simonyan의 논문](https://arxiv.org/abs/1312.6034)과 비슷하다. 여기서는 FCN 대한 convolutional features로 부터 뒤로 투영한다.

[Ross Girshick의 논문](https://arxiv.org/abs/1311.2524)은 더 높은 레이어에 강한 활성화(activation)를 나타내는 데이터셋 안에 패치들(patches)를 식별하는 시각화를 연구하였다. 본 논문에서는 입력 이미지의 일부분(crop)뿐만 아니라 특정 특성치을 자극하는 각 패치의 구조를 드러내는 top-down 투영이라는 점에서 다른점을 가지고 있다.

**Feature Generalization**

convnet의 특성을 다른 데이터셋에 일반화 하기 위해 사용하는 연구는 Ross Girshick의 논문과 [Jeff Donahue의 논문](https://arxiv.org/abs/1310.1531)에서 또한 이루어졌다.  Ross Girshick은 Caltech-101 그리고 Sun scenes 데이터셋으로 부터 SOTA의 성적을 내었고,  Jeff Donahue는 PASCAL VOC 데이터셋에서 SOTA의 성적을 내었다.

# Approach

본 논문은 [Yann LeCun](http://yann.lecun.com/exdb/publis/pdf/lecun-89e.pdf)과 [Alex Krizhevsky](https://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf)의 논문을 통해 표준 fully supervised convnet model을 사용하였다.

**본 논문에서 사용한 Model Architecture**

모델 구조는 Fig. 3. 와 같다. 구조에 대한 자세한 내용들은 이미지를 통해 이해하도록 한다.

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=11RyJChwP9_w2D6IlYRVF8DFg_GadQEEW' /><br>
    <i>Fig. 3. 본 논문에 사용된 8 layer convnet model</i>
</p>

이후 Training Details에서 더 자세히 나오지만 한 번에 설명하자면 AlexNet과 비슷한 구조를 사용했고, 몇가지 차이점이 있다. AlexNet은 GPU를 2개 사용하였고 여기서는 sparse connection을 사용한 layer 3,4,5를 dense connection으로 대체하였다. 가장 큰 차이는 첫 번째 레이어의 필터 크기를 7x7에서 5x5로 stirde를 4에서 2로 수정하였다는 것이다. 이를 통해 더 많은 정보를 유지할 수 있다.

**학습 데이터**

ImageNet 2012 데이터셋을 사용하였고, 각 RGB 이미지는 256x256으로 중심을 기준으로 crop resizing하였다. 그리고 하나의 이미지에 대해서 10개의 다른 부분으로 224x224로 하여 crop한 이미지를 사용했다.

**학습 과정**

학습 과정은 cross-entropy loss function을 사용했고, stochastic gradient descent (SGD)를 통해 파라미터를 최적화하였다. Learning rate는 검증데이터가 정체될때마다 메뉴얼하게 변경하였다.

**Hyperparameter**

- Batch size : 128
- Learning rate : 0.01
- Momentum : 0.9
- Dropout rate : 0.5

**그 외**

여섯 번째와 일곱 번째 레이어에 Dropout을 적용하였다. 모든 가중치의 초기값은 0.01로 하였고 bias는 0으로 하였다.

## Visualization with a Deconvnet

앞서 말한 것과 같이 입력값의 어떤 부분이 특성치를 활성화하였는지 알아보기위해 Deconvolutional Network 방법을 통해서 이 활성화된 값들로부터 입력값에 매핑을 하는 방법을 사용하였다. 

deconvnet은 기존 convnet과 다른거 없지만 그 과정이 반대로 이루어 진다는 것이다. convnet을 확인하기 위해서는 각 레이어에 deconvnet을 붙인다. 

입력 이미지는 레이어를 통과하여 계산된 convnet과 특성치로 표현된다. 계산된 convnet의 활성화를 확인하기위해 다른 모든 활성화값들을 0으로 치환하고 특성치를 입력값으로 deconvnet에 보낸다.

그러면 성공적으로 선택한 활성화를 일으킨 아래 레이어에서 활동(activity) 재구성하기 위해 unpool , rectify 그리고 filter 할 수 있다. 

**Unpooling**

convnet에서 max pooling은 거꾸로 적용될 수가 없다. 각 필터에서 최대값만 가져오는 방식인데 거꾸로 하기 위해서는 원래 위치가 어디인지알아야만 한다. 그래서 원래 위치가 어디에 있었는지 switch 변수들의 각 pooling 지역에서 최대값의 위치를 기록하여 unpooling 할 수 있었다. Fig. 1.아래에 black/white로 된 이미지가 바로 최대값의 위치를 기록하는 것을 보여준다. 

**Rectification** 

convnet은 positive값만 유지하는 ReLU 비선형 함수를 사용한다. 타당한 특성 재구성(valid feature reconstructions)을 얻기위해서는 deconvet도 마찬가지로 ReLU 함수를 적용한다.

**Filtering**

convnet에서는 이전 레이어의 특성치를 전달하기위해 학습된 필터를 사용한다. deconvnet에서는 이를 적용하기위해 같은 필터에 대해서 전치하여 사용한다. 단, 이전 레이어의 출력값이 아닌 수정된 맵(rectified map)을 사용한다. 즉, 각 필터를 수직, 수평으로 뒤집는 것을 말한다.

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1Z_aV-XWAFw9wV2XSwFHO1XxGHuItl2ZM' width="500"/><br>
    <i>Fig. 1. deconvnet 과정</i>
</p>

이러한 deconvnet 과정을 통해 선택한 활성화로부터 입력값의 어느 부분에 영향이 있었는지 확인 할 수 있다. 그러나 이 방법의 단점은 레이어에 있는 활동들에 대한 것이 아닌 단일 활성화에 대해서만 시각화 할 수 있다는 점이다.

하지만 이런 단점에도 불구하고 Fig. 6를 통해서 주어진 특성치를 자극하는 것이 어느부분인지 입력값에 대한 정확한 재구성이 가능하다는 것을 알 수 있다.

# Convnet Visualization

**Feature Visualization**

Fig. 2는 학습된 모델에 대해서 특성들의 시각화를 보여준다. 레이어 별로 가장 특성이 강하게 나타난 9개만 나타내었고, 어떤 특징이 나타나 있는지 보여준다.

예를 들면 두 번째 레이어의 경우 코너나 엣지같은 특징을 잡아낸다. 세 번째 레이어의 경우 조금 더 복잡한 invariance를 나타낸다. 네 번째 레이어는 각 클래스를 나타내는(강아지 얼굴이나 새의 다리같은) 중요한 variation을 나타낸다. 다섯 번째 레이어는 키보드나 강아지같이 물체 전제를 나타낸다.

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1IGMwXy_ckg2zUWtM9Cf_MABHetp-kDKV' width="400"/><br>
    <i>Fig. 2. Visualization of features in a fully trained model. 가장 feature map이 잘 나타난 9개의 activation만 나타낸다.</i>
</p>

**Feature Evolution during Training**

Fig.4. 에서는 모델을 학습하면서 중간중간 특성치를 추출하여 시각화하였다. 표현의 변화는 강한 활성화가 나타나면서부터 눈에 띄기 시작한다. 낮은 수준의 레이어는 금방 특징을 찾아내었고, 반면 높은 수준의 레이어는 충분히 학습이 이루어져야한다. 

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1GlCRVecreTl3jvE4106wA7eaFcmhIZi7' /><br>
    <i>Fig. 4.  학습 도중에 random하게 선택한 모델의 특성들의 변화를 나타낸다. epochs [1,2,5,10,20,30,40,64] 총 8번에 걸쳐 추출했다.</i>
</p>


**Feature Invariance**

Fig.5 에서는 5개의 샘플데이터를 수직으로 이동하거나 스케일을 조정하거나 회전하는 변화를 주었다. 상위와 하위 레이어로부터 변화 전과의 특징 벡터의 변화를 살펴보았다. 결과는 이동하거나 스케일이 변화하는데 있어서는 학습과정에서는 큰 변화는 없었다. 그러나 회전의 경우 invariance하지는 않았다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1OXtr4u2q0eGr_CE-uin8VO40V-_PHEIu' /><br>
    <i>Fig. 5. 2,3번째 열은 각각 첫 번째, 일곱 번째 레이어의 변화정도에 따른 원래값과의 euclidean distance이다. 4번째 열은 변화된 이미지의 실제값에 대한 확률이다.</i>
</p>


## Occlusion Sensitivity

이미지 분류에서 나올 수 있는 질문은 과연 모델이 물체를 정확히 판단하고 있는 것인지, 아니면 주변 컨텍스트를 사용하는것인지이다.

Fig.7 는 이런 질문에 대한 대답을 하기 위한 실험이다. 회색 박스로 이미지의 일부분을 가리고 출력값이 어떻게 변하는지 확인한다. 결과는 가려진 이미지에 대해 정답 클래스에 대한 확률이 큰 차이로 낮아졌다. 또한 특성맵의 활동 또한 매우 저하됨을 알 수 있다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1ZwWuKq9YzeF2gRof4bHBcA2kQR0xZdEb' /><br>
    <i>Fig. 7. 피쳐맵에 대한 투영값을 시각화함으로써 입력 이미지의 어떤 부분에 초점을 맞추고 예측했는지 알 수 있다.</i>
</p>

## Correspondence Analysis

강아지 사진을 랜덤하게 5개 가져온 후 눈과 코와 같은 얼굴을 특정 부분을 가린 후 피쳐맵의 변화에 대해 확인하였다. 차이는 Hamming distance를 통해서 계산하였으며, 이 값이 작을 수록 이미지간 일관성이 있다는 것을 뜻한다. 

$$\Delta_{l} = \sum_{i,j=1,i\neq j}^5 \mathcal{H}(sign(\epsilon_i^l, sign(\epsilon_j^l)), \\ \epsilon_i^l=x_i^l-\tilde{x}_i^l,\ where\ x_i^l\ and\ \tilde{x}_i^l\ are\ the\ feature\ vectors\ at\ layer\ l\\ for\ the\ original\ and\ occluded\ images\ respectively.$$

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=14Mu1vZHGb5av6YNvT_F8HXabpQp3yPbt' /><br>
    <i>Fig. 8. 이미지의 일부분을 회색 박스로 가렸을 때 예측에 어떤 변화가 있는지 확인</i>
</p>

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1mxH5DpLyrPPJS8OSLulPXNOIs8PME4xz' /><br>
    <i>Table 1. Occlusion Location에 대한 layer 5와 layer 7의 hamming distance</i>
</p>


# Experiments

## ImageNet 2012

결과적으로는 AlexNet보다 testset error를 1.7%(test top-5) 정도 더 낮췄다. 다른 모델과 비교했을 때test error가 14.8%로 SOTA 였다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1VRrUKXpuX5IdcgFrXbuRg6rste90NcIZ' /><br>
    <i>Table 2. ImageNet 2012 clasification error rates. The ∗ indicates models that were trained on both ImageNet 2011 and 2012 training sets.</i>
</p>

모델을 수정하면서 결과를 확인했다. FCN만 제외하거나 중간에 2개의 레이어를 제외한 경우 큰 차이는 없었지만 둘 다 제외하게 되면 차이가 많이나게 된다. 즉, 모델의 깊이가 성능에 영향이 있음을 나타낸다 그러나 너무 깊은 경우 과적합이 일어날 수 있다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1_TGtw-SV4cxttEIPhvSVXSUqeQlfyCAm' /><br>
    <i>Table 3. ImageNet 2012 classification error rates with various architectural changes to the model of AlexNet and our model</i>
</p>

## Feature Generalization

Caltech-101 데이터셋에 대해서 비교했을때 ImageNet 데이터셋을 사전학습한 모델이 더 좋은 성능을 내었다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1SJwyVWLpppEc7uAO88-VNart2F5trYSc' /><br>
    <i>Table 4. Caltech-101 classiﬁcation accuracy for our convnet models, against two leading alternate approaches.</i>
</p>

Caltech-256 데이터셋 또한 마찬가지였다. 심지어 클래스당 6개의 이미지만으로도 다른 두 모델의 클래스별 60장씩 학습한 것보다 좋은 성능을 내었다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1RPju7qIsnq7CG3nKSQZjemJKhn4aQmsQ' /><br>
    <i>Table 5. Caltech 256 classiﬁcation accuracies.</i>
</p>

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1IZuS11rGoS3TtETtIr2g-MZxjf2yEgkN' /><br>
    <i>Fig. 9. Caltech-256 classiﬁcation performance as the number of training images per class is varied. </i>
</p>

PASCAL 2012 데이터셋은 한 개의 이미지에 여러 물체가 들어있기도 하고 ImageNet과 이미지가 다른편이다. 그래서 PASCAL SOTA 모델 보다는 좋은 결과를 낼 수는 없었지만 몇몇 class에서는 더 높은 확률로 분류해내었다. 

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1XYi4RMyaEuBOqjVSZrukF-sj39x-KNBD' /><br>
    <i>Table 6. PASCAL 2012 classiﬁcation results, comparing our Imagenet-pretrained convnet against the leading two methods ([A]= (Sande et al., 2012) and [B] = (Yan et al., 2012)).</i>
</p>

# Discussion

PASCAL 데이터셋에 대한 결과는 이미지별 여러 물체를 고려한 다른 loss function을 사용했으면 더 성능이 좋았을거 같다고 한다. 이 네트워크는 object detection에서도 고려할 수 있다.
