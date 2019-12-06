---
layout: post
title:  "SmoothGrad: removing noise by adding noise Korean Version(한국어버전)"
date:   2019-10-23 3:32:00 +0800
categories: Paper
tags: XAI
---

이번에 리뷰할 논문은 2017년 CVPR에 등록된 논문입니다.  Google AI 팀에서 연구하였으며 Interpretable AI를 위해 이미지 분류에서 새로운 방법은 제안합니다.

**Authors**: Daniel Smilkov, Nikhil Thorat, Been Kim, Fernanda Viégas, Martin Wattenberg  
**Conference**: CVPR  
**Paper**: [SmoothGrad: removing noise by adding noise](https://arxiv.org/abs/1706.03825)  
**Year**: 2017  

# 역자의 말

영어를 한글로 변역하면서 변역된 단어가 적절하지 못한 경우 영어를 그대로 적용하였습니다. 부족한 부분이 있을 수 있으니 언제라도 잘못된 정보에 대해서는 comment나 메일을 통해서 지적 부탁드리겠습니다.

wogur379@gmail.com 

# Introduction

Deep neural Networks 같은 복잡한 모델을 해석하는 것은 아직까지 큰 난제이고 이를 계속해서 연구하는 것은 어플리케이션을 만드는 것과 이 자체가 문제로서 중요한 일이다.

헬스케어 분야부터 교육까지 많은 분야에서 해석가능성(interpretability)는 중요하다. 

이미지 분류문제의 결과를 이해하기위해 일반적으로 사용하는 접근방식은 최종결과에 대해서 이미지의 어떤 부분이 영향을 미쳤는지 확인하는 것이다. 

이런 접근방식은 sensitivity maps, saliency maps or pixel attribution maps 등 다양하게 불린다. 

이런 방법들은 얼굴의 눈과같이 사람이 이해할 수 있는 부분에 강조되어 보여진다. 또는 종종 무작위로 선택되어 강조되기도 한다. 이런 노이즈가 실제 결과에 의미가 있는 부분인건지 단지 피상적이 요인때문에 생긴 것인지는 알 수 없다. 이런 노이즈가 무엇이든간에 조사해볼 만하다.

본 논문에서 말하는 SMOOTHGRAD는 굉장히 간단한 방법이고, 다른 sensitivity map에도 적용할 수 있다. 

핵심 아이디어는 이미지의 영향력 있는 부분을 찾고, 이미지에 노이즈를 추가한 샘플이미지를 통해서 다시 한번 찾는다. 또한 학습 과정에 노이즈를 추가하여 학습하는 정규화 방법이 sensitivity map을 찾는데 '소음 제거(de-noising)'효과가 있음을 알게되었다. 

본 논문에서는 SMOOTHGRAD 이외에도 다른 sensitivity maps과 비교하였고 그 결과에 대해서 연구하였다. 그리고 이 방법들이 적용되는 이유와 모델이 분류를 어떻게하고 있는지를 더 잘 반영하는 이유에 대해서 추측을 해보았다.

마지막으로 sensitivity maps을 더 잘 시각화할 수 있는 방법들에 대해서 의논했다. 

본 논문에서 사용된 200개가 넘는 예제들에 대한 코드는 링크를 통해 확인할 수 있다. 

[SmoothGrad](https://pair-code.github.io/saliency/)

# Gradients as sensitivity maps

이미지 분류 문제에서 하나의 이미지셋에서 클래스를 분류하는 것은 아래와 같은 수식으로 이해할 수 있다.

$$class(x)=argmax_{c\in C}S_{c}(x),\\C: image\ set,\ c: class,\  S_c: class\ activation\ function$$

만약 S_c가 구별이 가능하다면(piecewise differentiable) 입력 이미지 x에 대해서 sensitivity map M_c(x)를 다음과 같이 간다하게 정의할 수 있다.

$$M_c(x)=\partial S_c(x)/\partial x$$

M_c는 x의 각각의 픽셀들에 대한 작은 변화가 클래스 c에 대한 분류 점수와 얼마나 큰 차이가 있는지 만들어내느지 나타낸다. 그 결과로 M_c는 핵심이 되는 부분을 강조하게 된다.

그러나 단순히 gradients로 나타낸 결과는 Fig.1 과 같이 거칠게 나올 수 밖에 없다. 

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1wqawOR0AdqxOYsjZEZu7h2-b57wtNjKd' /><br>
    <i>Figure 1. A noisy sensitivity map, based on the gradient of the class score for gazelle for an image classification network.</i>
</p>


## Previous work on enhancing sensitivity maps

gradient를 사용한 시각화 방법에 대한 노이즈에 대해서는 여러 가설이 있다. 이미지에 아무렇게나 뿌려져 있는 것 같지만 이것이 의사결정에 중심적인 역할을 하고 있을 수 있다. 그러나 한편으로는 이것을 특성의 중요도에 대한 대안으로 사용하는 것이 최선이 아닐 수도 있다.

기본 gradient sensitivity map에 대한 개선된 버전들이 몇몇 제안되었고, 본 논문에서는 그 중 몇몇 핵심들을 요약했다.

영향력의 측도로 gradient를 사용하는 것에 대한 한 가지 이슈는 중요한 특성이 S_c 함수를 애매하게 할 수 있다는 것이다. 반대로 전체적으로 강한 효과가 있다고 할 수 있고, 지역적으로 작은 효과를 가지기도 한다.

[Layerwise Relevance Propagation](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4498753/), [DeepLift](https://www.semanticscholar.org/paper/Learning-Important-Features-Through-Propagating-Shrikumar-Greenside/1a2118bed729579528deb51e745d58dd3629baf6), [Integrated Gradients](https://arxiv.org/abs/1703.01365)와 같이 local sensitivity보다 각 픽셀에 대한 전체적인 중요도를 측정하는 것과 같이 잠재적인 문제들을 해결하기 위한 연구들이 있다. 이런 방법을 통해 만들어진 맵은 "saliency" 또는 "pixel attribution" 맵이라고 언급됐다.

sensitivity maps을 개선할 수 있는 또다른 방법은 역전파 방법 그자체를 결과에 대해 positive 부분을 강조하는 방향으로 바꾸는 것이다. 예를 들면 역전파 과정에서 ReLU의 미분을 사용하여 negative 부분을 모두 버리는 것과 같은 [Deconvolution](https://arxiv.org/abs/1311.2901)과 [Guided Backpropagation](https://arxiv.org/abs/1412.6806)방법이 있다. 연구의 목적은 high-level 단에서 feature를 보다 선명하게 보기 위함이다. 이와 비슷하게 여러 level단의 gradients를 결합하는 방식을 제안하는 [연구](https://arxiv.org/abs/1611.07450)도 있었다. 

본 논문에서 이후 비교를 위해 'vanilla' gradient map들로는 intergrated gradient 방법과 guided backpropagation 방법을 사용하였다. 'sensitivity map', 'saliency map' 그리고 'pixel attribution map'과 같이 여러 용어가 있지만 여기서는 'sensitivity map'으로 언급기로 한다.

## Smoothing noisy gradients

sensitivity map에서 발생한 noise에 대해서 설명하자면 부분적으로 발생하는 의미없는 지역적 변화 때문일 것이다. 일반적인 훈련 방법으로는 이 noise를 제거할 수 없다. 그리고 모델이 ReLU 함수를 사용하는한 S_c 함수는 계속해서 차별성을 가질 수 없다.

아래 Fig.2를 보면 왼쪽은 원본 이미지 x이고 오른쪽은 원본 이미지에 평균이 0, 표준편차가 0.01인 노이즈를 더해준 이미지이다. t는 noise를 더한 정도이고, noise에 따른 이미지에 대한 gradient값이 어느정도 변화가 있었는지 RGB에 대해서 나타낸다. 결과적으로 사람에게는 보이지 않을 정도의 차이지만 실제 gradient는 큰 변화가 있었음을 알 수있다.

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1Jwm-cXgyWEPw-xNPrSEX3Z07dvYm0qul' /><br>
    <i>Figure 2. The partial derivative of S_c with respect to RGB values of a sing</i>
</p>

이 결과를 통해서 새롭게 sensitivity maps을 만들 수 있는 방법을 고안하였다. 바로 gradient에 대한 sensitivity map을 만드는것이 아닌 가우시안 커널을 통해 sensitivity map을 만드는 것이다. 아래와 같은 식을 통해서 정의할 수 있다.

$$\hat{M_{c}}(x)=\frac{1}{n}\sum_{1}^{n}M_{c}(x+N(0,\sigma^{2}))$$

n개의 샘플 갯수만큼 이미지에 대해서 sensitivity map을 만들고 그에 대한 평균을 구한다. 본 논문에서 제시하는 SMOOTHGRAD가 바로 이 방법을 뜻한다.

# Experiments

SMOOTHGRAD에 대한 것을 평가하기위해 분류모델에 사용된 신경망 모델을 이용하였다. 결과적으로는 unsmooth한 sensitivity map보다 본 논문에서 제시한 SMOOTHGRAD 방법이 더 좋았음을 시각적으로 확인할 수 있다.

본 논문에서 사용한 분류모델은 ILSVRC-2013 데이터셋을 사용한 Inception V3 모델과 tensorflow 튜토리얼에 사용된 MNIST 학습 모델이다. 

## Visualization methods and techniques

sensitivity map은 일반적으로 히트맵 유형이다. 영향력 있는 부분에 대해서 특정 색상으로 확인 할 수있다. 이번 챕터에서는 다양한 시각화 방법과 sensitivity map을 계산하는 과정에 대해서 확인한다.

**Absolute value of gradients**

sensitivity map은 음수와 양수가 있는 signed values로 생성된다. 그러나 음수와 양수를 함께 표현할지에 대한 여부는 데이터셋의 특성에 따라 나뉜다. 예를 들어 MNIST의 경우 흑백이미지이기 때문에양수(positive) 부분에 대해서만 표현 할 수 있다. 또 다른 예를 들자면 하나의 공의 분류한다고 했을 때 만약 그 공이 어두운 공이고 밝은 배경이였다면 nagative gradient를 나타낼 것이고 그 반대라면 positive gradient를 나타낼 것이다.

**Capping outlying values**

또 다른 gradient에 대한 속성은 이상치에 대한 것이다. 예를 들어 극단적으로 높은 값을 갖는 이상치가 있다고 했을때 이상치에 대한 제거를 하지 않으면 전처리가 없이 sensitivity map을 그리게 되면 대부분 어두운 값을 가지게 될 것이다.

**Multiplying maps with the input images**

또 몇몇 방법은 gradient에 대한 값들을 곱하여 sensitivity map을 만들어내기도 한다. 이 방법은 얼마나 기존 이미지에 대해 명확히 특징을 잡아낼 수 있는지는 몰라도 결과는 보다 선명한 sensitivity map을 얻을 수 있다는 것이다. 

그러나 이 방법에 대한 단점은 부가적인 효과를 얻을 수가 없다는 점이다. 만약 어느 픽셀값들이 0이라고 했을때 이 방법을 사용한다면 sensitivity map을 얻기가 힘들것이다. 예를 들어 어느 분류기의 이미지가 0이라는 값으로 검정을 표현했을때, 하얀 배경에 있는 검정 공은 절대 검정공에 대해 표현될 수 없을 것이다. 왜냐하면 검정을 0으로 표현했기 때문에!

## Effect of noise level and sample size

SMOOTHGRAD는 두 가지 하이퍼파라미터를 가진다. 첫 번째는 가우시안 커널에 대한 표준편차 값이고, 두 번째는 샘플 수 이다.

**Noise, sigma**

Fig.3은 ImageNet에 대한 몇몇 샘플데이터에 대해서 noise에 대한 효과를 나타냈다. 두 번째 열부터 noise가 0%일때 이후에는 5%부터 50%까지 noise가 더해질 수록 sensitivity map에 대해 더 선명한 결과를 얻을 수 있는 것을 확인할 수 있다. map에 대해서는 정량적인 평가를 할 수 없기때문에 정성적인 평가를 할 수 밖에 없었다. 

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1xMnrs6j9YuIfgfKaYEz9XxjOLnLdx-st' /><br>
    <i>Figure 3. noise 정도에 따른 sensitivity map의 결과</i>
</p>

**Sample size, n**

Fig. 4 는 sample 수가 늘어남에 따라 gradient를 통한 sensitivity map의 결과가 점점 부드러워짐을 알 수 있다. n이 50보다 큰 경우에 더 눈에 띄게 좋아졌다.

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1hBSVzQ5Jst0caBCydUVxfK8XRgRPEFVQ' /><br>
    <i>Figure 4. sample 수에 따른 sensitivity map에 대한 결과</i>
</p>

## Qualitative comparison to baseline methods

sensitivity map을 정량적으로 평가할 수 있는 기준이 없기 때문에 이전에 연구되었던 [연구](https://arxiv.org/abs/1312.6034)를 기준으로 정량적 평가에 대한 두 가지 측면으로 확인하였다.

첫 번째로는 시각적인 요소를 기준으로 하였고, 두 번째는 하나의 이미지에 여러 물체가 있는 경우 이 물체들을 구분가능한 정도에 따라 평가하였다. 

시각적인 요소에 대해서 Fig. 5는 SmoothGrad 방법과 다른 3가지 sensitivity map방법에 대해서 비교를 하였다. 결과적으로 SmoothGrad가 Vanilla 방법과 Intergrated Gradient보다는 더 선명한 sensitivity map을 얻었지만 반면 마지막 3개의 이미지에 대해서는 Guided Backprop보다 덜 선명한 결과를 얻었다. 그러나 Guided Backprop의 경우 배경이 이미지가 일관적으로 하나의 색상을 나타낼때는 sensitivity map이 영~ 좋지 않았다. 이때는 여윽시 SmoothGrad가 더 좋았다.

이를 통해 분석해 보았을때 이미지가 어떤 배경 또는 질감(texture)에 있는냐에 따라 sensitivity가 달라질 수 있음을 알 수 있었다. 

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1Wx0cTgmtm85YiG5QxOoRdan6ZgowXtzi' /><br>
    <i>Figure 5. Qualitative evaluation of different methods.</i>
</p>

Fig. 6. 또한 다른 3개의 sensitivity map 방법과 SmoothGrad를 비교하였다. 하나의 이미지에 두개의 물체가 있을 때 각각의 물체를 식별하는 모델을 통해서 두 물체를 잘 구별하는지 시각화 하기위해서 두 물체에 대한 sensitivity map을 만든 후 값을 빼주었다. M_1(x) - M_2(x). 그리고 각각의 값에 대해서 RGB채널을 [-1,0,1]로 적용하였다. 그 결과로는 SmoothGrad가 정성적으로 평가했을 때 다른 세가지 모델에 비해서 보다 선명한 결과를 얻는 것을 확인할 수 있었다. 

여기서 또한 Guided BackProp에 대해서 왜 제대로 구별을 못하는 것인지에 대한 의문이 있었다.

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1zLQgqmIMraLdW1lgczXD3aQ9br2E59wb' /><br>
    <i>Figure 6. Discrimativity of different methods.</i>
</p>

## Combining SmoothGrad with other methods

SmoothGrad는 다른 sensitivity map 방법과 결합하여 사용할 수 있다. Fig. 7과 같이 noise한 샘플 데이터 n개를 통해서 integrated gradient 방법을 사용한 결과와 Guided BackProp 방법을 사용한 결과는 SmoothGrad를 적용하였을 때 보다 훨씬 선명한 sensitivity map을 얻을 수 있었다.

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1iRFfA4E2rUhjoA8Obug2ruDQMNRyVTDF' /><br>
    <i>Figure 7. Using SMOOTHGRAD in addition to existing gradient-based methods: Integrated Gradients and Guided BackProp.</i>
</p>

## Adding noise during training

지금까지의 SmoothGrad 방법과 마찬가지로 학습과정에서 noise를 추가한 이미지를 통해 학습하는 것 또한 보다 선명한 sensitivity map을 얻을 수 있다. Fig. 8과 Fig. 9를 통해서 학습과정에 noise를 추가한 이미지를 추가하고 평가 과정에서 noise한 샘플이미지에 대한 평균을 구하여 sensitivity map을 구하게 되면 보다 더 선명한 sensitivity map을 얻을 수 있음을 확인할 수 있었다. 

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1sdHLVcvl-t-sfDkQG0UqBl5tMBqkQf0z' /><br>
    <i>Figure 8. Effect of adding noise during training vs evaluation for MNIST.</i>
</p>

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1U5N0vQVCGrQsHS9PUkPvmQYyIM_t3A7B' /><br>
    <i>Figure 9. Effect of adding noise during training vs evaluation for Inception</i>
</p>

# Conclusion and future work

결과적으로 위의 실험 결과를 보았을때 sensitivity map을 얻기위한 두가지 형태는 평가 과정에서 n개의 noise한 샘플이미지의 평균으로 구하는 것과 학습과정에 noise한 데이터를 추가하는 방법이 있다. 

여기서는 이후 연구해야하는 논점이 몇가지 있었다. 

첫 번째로는 noise한 sensitivity map은 noise한 gradient 때문이지만 이를 부정할 수 있는 어떠한 근거나 이론적인 반박을 찾을 수 있는 가능성이 있다는 것이다. 또한 SMOOTHGRAD에 대한 효과가 다른이유에서가 아닌 다른 질감(texture) 때문일 수도 있다는 것이다.

두 번째로는 noise와 함께 학습하면서 더 smooth한 이미지를 만들기 위한 score function을 제시하는 방법이 있을 수 있다는 것이다. 즉, 정성적인 평가가 아닌 정량적인 평가를 할 수 있는 척도가 필요하다.