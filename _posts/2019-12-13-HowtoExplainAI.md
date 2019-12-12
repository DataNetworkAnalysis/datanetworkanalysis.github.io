---
layout: post
title:  "How to Explain AI (AI를 설명하는 방법)"
date:   2019-09-11 3:17:00 +0800
categories: Paper
tags: Review XAI
---

# 시작말

비교적 최근에 나온 몇몇 논문들을 통해 딥러닝 모델을 눈으로 살펴볼 수 있는 방법(Methods)과 평가지표(Evaluations)에 대한 설명을 작성하였습니다.

본 글에 앞서 이 글은 이번 모두의 연구소에서 주최하는 AI College의 Explainable AI 분야에 참가하고자 1번문제에 대한 답변을 포스팅으로 작성한 글 입니다.

그동안 XAI에 관심은 많았지만 제대로 공부할 기회가 없었습니다. 이번 AI College를 통해 ~~퇴사각~~공부할 기회를 잡고 프로젝트에 참여하고자 합니다. 

첫 논문 리뷰이기 때문에 해석에 있어서 표현이 어색하거나 잘못된 부분이 있을 수 있습니다. 내용면에서 잘못된 부분은 **comment**를 통해 알려주시면 수정하도록 하겠습니다.

---

# What is Explainability

해석 가능하다는 것은 질문 `Why Question` 에 답을 할 수 있다라는 것 입니다.  `why and why-should`

**Explainability**는 `interpretability`와 `Completeness` 두가지를 모두 고려해야합니다.

- **Interpretability**는 시스템의 구조를 사람이 **이해**할 수 있도록 설명하는 것 입니다.
- **Completeness**는 시스템이 돌아가는 원리를 **정확한** 방법으로 설명할 수 있는 것 입니다.

두 가지를 동시에 높이는 것은 어렵습니다. 어쩔 수 없이 *trade off*를 가지고 있습니다.

이 두 가지를 고려하여 어떻게 딥러닝 모델을 정성적으로 정량적으로 평가할 수 있는지 알아보겠습니다.

# How to Visualize Neural Networks

딥러닝 모델에서 이미지를 설명하기위한 방법으로는 크게 세가지 방법이 있습니다.

1. *Backpropagation Based Method (BBMs)*
2. *Activation Based Methods (ABMs)*
3. *Pertubation Based Methods (PBMs)*

**Backpropagation Based Method; BBMs**

이름 그대로 입력값에 대해 `backpropagation`을 통한 오차의 정도를 계산하여 각 픽셀의 중요도를 나타내는 방법입니다.

대표적으로 **LRP**(Layer-wise Relevance Propagation), **DeepLIFT, SmoothGrad, VarGrad**이 있습니다.

BBMs 은 세밀한 중요도나 관련성을 표현하기에는 빠른 계산력과 생산성을 가지고 있지만, 이미지에 대한 품질이 좋지 못하고, 해석하기가 어렵습니다.

얼마나 신뢰가 있는지 알기 위해서는 기존 모델과 비슷한 모델로 보다 쉽게 설명하거나 전저리를 필수적으로 해야하지만 정확한 결과를 얻기 힘듦니다.

**Activation Based Methods; ABMs**

이 방법은 설명을 위해 각 Convolutional leyers 에서 나온 Activation들의 선형 결합한 가중치를 사용합니다.

가장 대표적인 방법이 **CAM(Class Activation Map)**이라는 방법이고, 이어서 **Grad-CAM (Gradient-Class Activation Map)**, **Grad-CAM++**이 나왔습니다. CAM에 대한 자세한 설명과 Keras를 사용한 샘플 코드는 [여기](https://jsideas.net/class_activation_map/)에서 확인할 수 있습니다.

저의 첫 연구였던 "[Machine learning for detecting moyamoya disease in plain skull radiography using a convolutional neural network](https://www.researchgate.net/publication/329983021_Machine_learning_for_detecting_moyamoya_disease_in_plain_skull_radiography_using_a_convolutional_neural_network)" 또한 Grad-CAM을 통해 CNN 모델에 대한 설명성을 나타냈습니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1WN0CZYbw9ndiD0ssWFW4Gi_Xk8RXRl0V'/><br>
    <i>Source: Machine learning for detecting moyamoya disease in plain skull radiography using a convolutional neural network</i>
</p>

Kaggle 대회 중 하나인 State Farm Distracted Detection **데이터를 학습한 후 Grad-CAM으로 예측한 사진에 대해 표현했을 때 각 클래스별로 특징을 잘 찾아낸것을 알 수 있습니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1lI9guX46VhxFM1F38ZyyAkm1peUKAVXR'/><br>
    <i>Source: Kaggle의 State Farm Distracted Detection의 데이터</i>
</p>

학습에 사용한 [코드](https://github.com/bllfpc/kaggle/tree/master/Competitions/State%20Farm%20Distracted%20Driver%20Detection/code)와 시각화 [jupyter notebook](https://nbviewer.jupyter.org/github/bllfpc/kaggle/blob/master/Competitions/State%20Farm%20Distracted%20Driver%20Detection/code/visualization.ipynb) 파일

ABMs 는 기존 이미지 위에 영향이 있는 부분에 대해서 heat-map을 overlap하여 보기 쉽게 표현 할 수 있는 방법입니다. 그러나, 조금 더 세밀한 근거(fine-grained evidence)나 색 의존성(color dependencies)을 표현하는데 적합하지 않습니다. 또한 결과를 충분히 설명할 수 있다거나, 의사결정 과정을 나타냈다라고 보장하기는 힘들다는 단점이 있습니다.

**Perturbation Based Methods; PBMs**

PBMs 는 입력값에 대한 작은 변화(purtubation)에 따라 예측값이 어떻게 변하는지를 통해서 중요도를 파악하는 방법이라 할 수 있습니다.

대표적으로 LIME(Local Interpretable Model-Agnostic Explanation)이 있습니다.  LIME은 입력값에 대한 변화`perturbation` 를 통해 어떤 변수가 영향을 미치는지 설명하고, 입력값 근처에서 지역에 한정된 선형모델을 구합니다. 아래 그림을 통해 더 쉽게 이해 할 수 있습니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1kcv9wFAwqJwrNfEG9c2sCfphPmSzqd7d'/><br>
    <i>Source: Marco Tulio Ribeiro의 “Why Should I Trust You?” Explaining the Predictions of Any Classifier</i>
</p>

이미지의 경우 일부분을 가린 이미지를 학습된 모델에 예측했을 때, 예측값이 많이 떨어진다면 가려진 부분이 실제 예측하는데 큰 영향이 있다라는 것을 알 수 있습니다. 

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1q4zWxJGaW7or0UFwyZKaFW2crjMUkdly'/><br>
    <i>Source: Marco Tulio Ribeiro</i>
</p>

아래 예시와 같이 원래 개구리 이미지를 넣었을때 tree forg일 확률이 0.54이고,  이미지 일부를 가린 세개의 이미지에서 첫 번째 얼굴과 사과 부분만 가린 이미지는 확률이 0.85까지 올라갔고, 약간의 눈과 다른 일부분만 남긴 이미지는 0.00001로 확률이 떨어졌습니다. 마지막으로 사과의 일부분만 가린 이미지는 0.52의 확률을 냈습니다.  이를 통해서 첫 번째 이미지에서 보여지는 영역이 tree frog을 예측하는데 큰 영향이 있음을 알 수 있습니다.

LIME에 대한 간단한 설명 [동영상](https://www.youtube.com/watch?v=hUnRCxnydCc)

LIME은 어떤 모델이든 적용할 수 있지만 시간이 오래걸리고, 투박한(Coarse) 설명밖에 표현하지 못합니다.

위와 달리 변화된 이미지 버전(pertubed version of the image)에 최적화하여 설명을 표현하는 PBMs도 있습니다. 예를 들어 pertubed image **e**를 아래와 같이 정의합니다.

$$e = m\cdot x \ + \ (1-m)\cdot r$$

where m is a mask, x the input image, and r a reference image containing little information

이러한 방법은 이미지 공간에서 설명을 해석하기가 쉽다라는 장점이 있습니다. 

올해 CVPR에서 발표된 "Interpretable and Fine-Grained Visual Explanations for Convolutional Neural Networks"에서는 기존 PBMs에 대한 개선점을 선보였습니다. 여기서 사용된 방법을 `FGVis`라고 부릅니다.

이 PBMs는 최적화하는 과정에서 gradients를 필터링하는 새로운 `adversarial defense technique`을 제안합니다. 이 defense 방법은 fine-tune을 위해 hyperparmeters를 추가할 필요도 없고, pixel 각각을 최적화하기 때문에 이미지를 복원(resolution)할 필요도 없습니다. 

[adversarial defence technique 설명](https://www.notion.so/adversarial-defence-technique-d97bd12496e84351a69d0154c3eb233f)

다른 Explainable 모델들과 비교한 결과

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1CvrqNhsQe2xzQjxadT4xoVrDSYmQD7tI'/><br>
    <i>Source:  Interpretable and Fine-Grained Visual Explanations for Convolutional Neural Networks</i>
</p>

위 사진을 보면 b), f) 그리고 g)의 경우 예측에 불필요한 배경부분에 대한 정보도 포함되어 있어 표현이 투박(Coarse)합니다. 이 논문에서 제시한 FGVis의 경우 실제 사진을 예측했을때 필요한 최소한의 가장 필요한 정보만 보여준다는 것을 알 수 있습니다. 

FGVis의 경우 두개의 object가 있는 이미지도 각각 잘 설명할 수 있습니다. 심지어 object간 겹치는 경우도 각 클래스에 대한 중요도를 잘 찾아냅니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1U5_E1vTw2Mo_NLoujlBk_C-3XxQtifJY'/><br>
    <i>Source:  Interpretable and Fine-Grained Visual Explanations for Convolutional Neural Networks</i>
</p>

# How to Evaluate Explainable Models

최근 딥러닝 모델을 설명하기위한 많은 방법들이 나오고 사용되고 있습니다. 그럼에도 불구하고 이러한 방법들이 모델을 얼마나 잘 설명하는지 모델의 **범위(scope)**와 **품질(quality)**를 평가하는데 어려움이 있습니다. 

아래 이미지만 보더라도 모델의 학습 또는 입력값과 전혀 관계없는 Edge Detector가 다른 설명가능한 방법들의 결과와 시각적으로 큰차이가 없음을 알 수 있기 때문에 시각적인 요소로 평가하는 것은 적절하지 않습니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1vVCd9Da3MlyxJzqMVuYwc-jD4GXU1shG'/><br>
    <i>Source: Sanity Checks for Saliency Maps</i>
</p>

FGVis의 경우 다른 모델과 **예측값의 정도**를 통해서 설명가능성에 대해 비교를 했습니다.

아래 결과표는 FGVis가 다른 해석 방법들에 비해 얼마나 좋은지를 설명하는 표입니다. ImageNet의 Validation dataset에 적용한 결과이며, *deletion game*을 통해 값을 비교하였습니다. *deletion game*은 예측값에 가장 큰 영향을 주는 최소한의 pixel만을 제거하는 방법입니다. 

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1i-2q_C4KVIE1H1oWg69IgRIa-GfwpmZb'/><br>
    <i>Source:  Interpretable and Fine-Grained Visual Explanations for Convolutional Neural Networks</i>
</p>

위 결과에서는 FGVis가 *deletion game*을 통해 찾은 영향도 있는 pixel을 제거하였을때, 예측값이 ResNet50의 예측값이 0.0644, VGG16의 예측값이 0.0636 밖에 되지 않습니다. 다시말해, FGVis가 예측에 대한 설명력있는 pixel을 더 잘 찾았다라는 얘기입니다.

또 다른 평가 방법은 ***Sanity Checks for Saliency Maps***에서 제안한 방법입니다.

여기서 제안한 방법은 통계적 랜덤 테스트(statistical randomization test)입니다. 이 실험은 자연적인 실험(natural experiment)과 인위적으로 랜덤한 실험(artificially randomized experiment)을 비교합니다.

**실험 방법**

일반적인 구성으로는 두 가지 instantiations에 초점을 맞췄습니다.

- **a model parameter randomization test**
- **a data randomization test**

***A Model Parameter Randomization Test***

학습된 모델의 saliency method의 결과물과 학습되지 않은 모델의 saliency method의 결과물을 비교합니다. 만약 차이가 없다면 saliency method는 모델과 전혀 상관없다라는 얘기가 됩니다. 이런 sailency map은 모델 디버깅과 같이 모델 파라미터에 의존적인 task와는 전혀 도움이 되지않습니다.

***A Data Randomization Test***

라벨링된 데이터로 학습된 모델의 saliency method 결과와 무작위로 라벨링된 데이터로 학습된 모델의 saliency method의 결과르 비교합니다. 만약 차이가 없다면 saliency method는 이미지와 라벨의 관계에 따라 달라지는 것이 아니라고 볼 수 있습니다.

**시각화(Visualization) & 유사성 평가척도(Similarity Metrics)**

- 시각화(Visualization): `absolute-value(ABS)` 와 `diverging visualization` (positive와 negative의 색상을 다르게 표현)을 사용
- Spearman rank correlation with absolute value
- 유사성 평가(Similarity Metrics): 정량평가를 위해 아래와 같은 Metrics을 사용
    - *Spearman rank correlation without absolute value(diverging)*
    - *the structural similarity index (SSIM)*
    - *Pearson correlation of the histogram of gradients (HOGs)*
    - *the SSIM and HOGs similarity metric on ImageNet examples without absolute values*

**Model Parameter Randomization Test의 결과값**

아래를 보면 Guided Back-propagation과 Guided GradCAM의 경우 모델 학습과 전혀 관련이 없음을 보여줍니다. 

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=18CPqjI1c6ADumr-AH3HAMnvbt3zsM04P'/><br>
    <i>Source: Sanity Checks for Saliency Maps</i>
</p>

ImageNet 데이터에 대한 rank correlation을 보면 ABS와 No ABS의 경우 위에서 보인것과 같이 Guided Back-propagation과 Guided GradCAM의 경우 correlation이 거의 1임을 알 수 있습니다. 반면 Intergrated Gradients, Gradient-Input 그리고 Gradient의 경우 더 많이 layer/block의 가중치가 랜덤화될수록 rank correlation이 0에 가까워짐을 알 수 있습니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1SWoKmraQmmI0jHmLErL-Vbe2pB-Bo9ls'/><br>
    <i>Source: Sanity Checks for Saliency Maps</i>
</p>

HOGs와 SSIM도 위와 마찬가지의 결과를 나타냅니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1xGT8tPaW1Nb9E8MzX7_ubDLktWFDyRFW' width='700'/><br>
    <i>Source: Sanity Checks for Saliency Maps</i>
</p>

**Data Randomization Test의 결과값**

아래 결과값을 보면 GradCAM의 경우 random하게 labeling된 데이터를 학습한 모델과 아닌모델의 차이가 전혀 관련성이 없음을 알 수 있습니다. 

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1RNOxZjfsWyaxwvzrPw-3pZCmaGbjzWqw' width='700'/><br>
    <i>Source: Sanity Checks for Saliency Maps</i>
</p>

그러나 결과를 시각적 요소에 의존하는 것은 오해의 소지가 있습니다. Guided BackProp의 경우 시각적으로 그럴듯해 보이는 입력데이터의 중요한 부분이 강조되어 있음을 알 수 있습니다. 

gradient x input의 경우 시각적으로 변화가 있어보이나, 입력 구조는 여전히 마스크와 비슷합니다. 

바로 다음 해에 구글에서 Explainable AI에 대해 많은 연구를 하고 있는 Been Kim의 새로운 논문인 **BIM: Towards Quantitative Evaluation of Interpretability Methods with Ground Truth**가 나옵니다. 

여기서 제시하는 방법은 작년에 **Sanity checks for saliency maps**에서 제안한 방법보다 더 "좋은(harder)" 해석 방법이라고 얘기합니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1a_1LvyQOFcs_VmGLiK9tKGP82Hnq8Dya' width='700'/><br>
    <i>Source: BIM: Towards Quantitative Evaluation of Interpretability Methods with Ground Truth</i>
</p>

이 논문에서는 `false positive`에 집중한다고 얘기합니다. 그리고 이 중요하지 않은 특징(false positive가 집중하는 부분)이 어느정도 까지 잘못 파악하고 있는지를 정량적으로 평가하기 위해 3가지 평가산식을 제안합니다.

평가 산식을 말하기 전에 여기서 사용되는 해석방법의 중요도를 나타내는 개념을 설명해야합니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1duCe0wOMLiqcOtpkFKlicik27ZvJWZt0' width='700'/><br>
    <i>Source: BIM: Towards Quantitative Evaluation of Interpretability Methods with Ground Truth</i>
</p>

Source: BIM: Towards Quantitative Evaluation of Interpretability Methods with Ground Truth.

~~귀찮아서 그림으로 복사한게 맞습니다.~~ 위의 수식을 보면 `g`는 saliency map에 해당 하는 부분과 concept에 해당하는 부분이 어느정도 겹치는지 평균으로 나타내는 값입니다. 즉, 얼마나 concept을 잘 표현했는지입니다.

`G`의 경우는 맞게 분류된 X들에 대해서만 앞에서 계산한 g를 합한 후 X의 크기만큼 평균을 내준 값입니다. 즉, 맞게 분류된 X들에 대해서 얼마나 정확히 예측했는지 입니다.

다음은 `common feature (CF)`입니다. 하나이상의 클래스에서 의미가 있는 픽셀들의 집합을 말합니다. 이러한 CF가 클래스별로 얼마나 확률적으로 나타나는 지를  `commonality (k)`로 표현합니다. 

학습된 환경에 k가 어느정도냐에 따라서 CF가 예측에 얼마나 중요한 요소인지 아닌지 판단됩니다. ex) k=1인 경우는 모든 클래스에 CF가 존재함을 말합니다.

위의 개념을 이해했다면 이제 3가지 평가 산식을 정의할 수 있습니다.

1. ***Model Dependence : model contrast score (MCS)***
    - MCS는 해당 concept c가 모델에 중요한지 안한지를 판단하는 기준입니다. MCS가 클수록 더 좋은 설명력을 나타낸다고 설명됩니다.
2. ***Input Dependence : input dependence rate (IDR)***
    - IDR은 두개의 입력데이터(CF가 100%인 경우와 없는 경우)를 비교해서 CF가 있는 경우 g가 더 작다라는 것을 나타내는 값입니다. 즉, 두 입력데이터의 차이가 명확한지를 나타내는 지표입니다.
    - 값은 확률로 나오고 확률값이 높을 수록 설명력이 더 높다는 것을 뜻합니다.
    - 1 - IDR의 경우 false positive rate를 말하며 얼마나 많은 이미지가 중요하지 않은 부분을 중요하다라고 생각하게끔 하고 있는지를 말합니다.
3. ***Input Independence : input independence rate (IIR)***
    - IDR이 차이가 있음을 보여야하는데에 반해 IIR은 차이가 없음을 보여야합니다.'
    - 입력값의 일부분에 `patch` 를 붙인것과 붙이지 않은것의 차이가 없도록 최적화합니다.
    - 약간 사람의 주관이 들어갈 수 있는 부분은 차이가 없다는 것에 대한 기준(t)을 사람이 정해주어야합니다.
    - IDR과 마찬가지로 확률값으로 나오고 확률이 높을 수록 설명력이 더 높다는 것을 뜻합니다.

이 논문은 해석가능한 모델을 평가하기위해 반자연적인(반인위적인?) 이미지셋을 공개했고, a) 여섯개의 지역적인 특징만 설명할 수 있는(한번에 하나의 이미지만 표현할 수 있는) 해석방법들과 b) 전체적으로 설명할 수 있는(클래스에 대한 설명이 가능한) 해석 방법을 사용하여 평가했습니다.

**a)** **GradCAM**(GC), **Vinalla Gradient**(VG), **SmoothGrad**(SG), **Intergrated Gradient**(IG), **Guided Backpropagation**(GB) 그리고 **Gradient x Input**(GxI)

**b) TCAV**(Testing with a Concept Activation Vector)

TCAV는 이 논문의 저자인 Been Kim이 이전 논문에서 제안한 방법이다.

TCAV는 어떤 concept의 영향력을 나타내는 평가 기준이다. 예를 들어 얼룩말을 인식하는 모델과 'striped'를 정의하는 새로운 데이터를 주었을때, TCAV는 이 'striped'가 얼룩말을 예측하는데 미치는 영향력이 어느정도인지 계산한다.

**Model Dependence : model contrast score (MCS)의 결과값 비교**

MDS는 절대평가와 상대평가 두가지 방식으로 하였습니다. 절대평가는 k가 100%라는 가정하에 진행되었고 상대평가는 k가 바뀜에 따라 변화정도를 비교하였습니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=17HAtJPVVXPckFrKuHGjYtngoTG2D4KOf' width='700'/><br>
    <i>Source: BIM: Towards Quantitative Evaluation of Interpretability Methods with Ground Truth</i>
</p>

k를 100%로 가정된 상황에서 모델 해석 방법간 비교를 하였을 때, GC가 매우 높은 MCS를 보입니다. 여기서 TCAV의 MCS는 object 모델의 TCAV score와 Scene 모델의 TCAV score의 차이입니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1AgkFDhe48Ko0_slMAcgbxY3tvNZHzitD' width='700'/><br>
    <i>Source: BIM: Towards Quantitative Evaluation of Interpretability Methods with Ground Truth</i>
</p>

k가 바뀜에 따라 상대 평가를 통해 비교해 보았을때 Figure 6의 saliency map이 k가 커지면서 점점 멍멍이에 대한 중요도를 놓치고 있음을 알 수 있습니다. k가 커지면서 GC와 TCAV는 급격히 0에 가까워집니다. Accuracy와는 TCAV가 상관관계가 0.95로 가장 높고, 다음으로는 GC가 0.92로 높습니다.

**Input Dependence : input dependence rate (IDR)** 

아래 Fugure 8에서 (a)를 보면 두 입력데이터의 차이(CF가 100%인 경우) 시각적인 요소만으로는 어떤게 더 좋다라고 얘기하기 어렵습니다. Figure9의 (a)를 보면 IDR로 판단할 수 있습니다. 여기서는 GC와 VG가 가장 정확하게 평가된 CF를 나타냅니다. 즉, 진짜라고 잘못 판단한 경우(false positive)가 가장 적다라는 의미입니다.

**Input Independence : input independence rate (IIR)**

IIR에서는 GC와 VG를 제외한 나머지 모든 모델이 false positive가 80%를 넘습니다. GB의 경우 선명한 멍멍이의 모습을 볼 수 있습니다. 여기서 t는 10%로 정하였습니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1HB2SUm3bRayUJuGTpSHQYxUdRiz-ww6L' width='700'/><br>
    <i>Source: BIM: Towards Quantitative Evaluation of Interpretability Methods with Ground Truth</i>
</p>

---
# 맺음말

본 리뷰를 마치며, 본격적으로 Explainable AI에 대한 공부를 위해 논문 리뷰를 처음으로 써보았습니다. 이후에는 조금 더 세밀하게 여러 딥러닝 모델의 해석방법에 대한 원리와 과정을 설명하는 포스팅을 써볼 계획입니다.

최종적으로는 많은 딥러닝 모델을 해석하는 방법을 구현해보며 확인하는 과정을 설명하는 포스팅을 써보려 합니다. 부족한 부분에 대해서 많은 지적과 관심 부탁드리겠습니다.

긴 글 읽어주셔서 감사합니다.

---

# Reference

1. GILPIN, Leilani H., et al. Explaining explanations: An overview of interpretability of machine learning. In: 2018 IEEE 5th International Conference on data science and advanced analytics (DSAA). IEEE, 2018. p. 80-89.
2. ZHANG, Quanshi; NIAN WU, Ying; ZHU, Song-Chun. Interpretable convolutional neural networks. In: Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition. 2018. p. 8827-8836.
3. WAGNER, Jorg, et al. Interpretable and Fine-Grained Visual Explanations for Convolutional Neural Networks. In: Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition. 2019. p. 9097-9107.
4. ADEBAYO, Julius, et al. Sanity checks for saliency maps. In: Advances in Neural Information Processing Systems. 2018. p. 9505-9515.
5. YANG, Mengjiao; KIM, Been. BIM: Towards Quantitative Evaluation of Interpretability Methods with Ground Truth. arXiv preprint arXiv:1907.09701, 2019.
6. Kim, T., Heo, J., Jang, D. K., Sunwoo, L., Kim, J., Lee, K. J., ... & Oh, C. W. (2019). Machine learning for detecting moyamoya disease in plain skull radiography using a convolutional neural network. EBioMedicine, 40, 636-642.
7. 조용래님의 블로그
    [머신러닝 모델의 블랙박스 속을 들여다보기 : LIME](https://dreamgonfly.github.io/2017/11/05/LIME.html)
8. 강준식님의 블로그
    [CAM: 대선주자 얼굴 위치 추적기](https://jsideas.net/class_activation_map/)
9. bllfpc(허재혁) github 
    [bllfpc/kaggle : State Farm Distracted Driver Detection - Visualization unsing Grad-CAM](https://github.com/bllfpc/kaggle/tree/master/Competitions/State%20Farm%20Distracted%20Driver%20Detection/code)